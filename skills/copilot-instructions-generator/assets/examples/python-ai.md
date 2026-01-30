# Python AI/ML Application

Machine learning and AI application using Python.

## Tech Stack

- **Language**: Python 3.11+
- **ML Framework**: PyTorch / TensorFlow / scikit-learn
- **Data**: Pandas, NumPy
- **LLM**: LangChain / OpenAI / Hugging Face
- **Experiment Tracking**: MLflow / Weights & Biases
- **Environment**: Poetry / uv + pyenv

## Project Structure

- `src/` - Source code
  - `data/` - Data loading and preprocessing
  - `models/` - Model definitions
  - `training/` - Training loops and configs
  - `inference/` - Inference pipelines
  - `evaluation/` - Metrics and evaluation
  - `utils/` - Utility functions
- `notebooks/` - Jupyter notebooks
- `experiments/` - Experiment configs
- `tests/` - Test files
- `data/` - Data directory (gitignored)
- `models/` - Saved models (gitignored)

## Development Commands

- **Train**: `python -m src.training.train --config experiments/config.yaml`
- **Evaluate**: `python -m src.evaluation.evaluate --model models/latest`
- **Serve**: `python -m src.inference.serve`
- **Test**: `pytest tests/`
- **Lint**: `ruff check src/`

## Coding Guidelines

### Data Loading

```python
import pandas as pd
from torch.utils.data import Dataset, DataLoader
from typing import Tuple

class CustomDataset(Dataset):
    """Custom dataset for training."""
    
    def __init__(self, data_path: str, transform=None):
        self.data = pd.read_parquet(data_path)
        self.transform = transform
    
    def __len__(self) -> int:
        return len(self.data)
    
    def __getitem__(self, idx: int) -> Tuple[torch.Tensor, torch.Tensor]:
        row = self.data.iloc[idx]
        features = torch.tensor(row['features'], dtype=torch.float32)
        label = torch.tensor(row['label'], dtype=torch.long)
        
        if self.transform:
            features = self.transform(features)
        
        return features, label


def create_dataloaders(
    train_path: str,
    val_path: str,
    batch_size: int = 32,
    num_workers: int = 4,
) -> Tuple[DataLoader, DataLoader]:
    """Create train and validation dataloaders."""
    train_dataset = CustomDataset(train_path)
    val_dataset = CustomDataset(val_path)
    
    train_loader = DataLoader(
        train_dataset,
        batch_size=batch_size,
        shuffle=True,
        num_workers=num_workers,
        pin_memory=True,
    )
    val_loader = DataLoader(
        val_dataset,
        batch_size=batch_size,
        shuffle=False,
        num_workers=num_workers,
    )
    
    return train_loader, val_loader
```

### Model Definition (PyTorch)

```python
import torch
import torch.nn as nn
from typing import Optional

class TransformerModel(nn.Module):
    """Transformer-based model for sequence classification."""
    
    def __init__(
        self,
        vocab_size: int,
        d_model: int = 512,
        nhead: int = 8,
        num_layers: int = 6,
        num_classes: int = 2,
        dropout: float = 0.1,
    ):
        super().__init__()
        self.embedding = nn.Embedding(vocab_size, d_model)
        self.pos_encoder = PositionalEncoding(d_model, dropout)
        
        encoder_layer = nn.TransformerEncoderLayer(
            d_model=d_model,
            nhead=nhead,
            dropout=dropout,
            batch_first=True,
        )
        self.transformer = nn.TransformerEncoder(encoder_layer, num_layers)
        self.classifier = nn.Linear(d_model, num_classes)
    
    def forward(
        self, 
        x: torch.Tensor, 
        mask: Optional[torch.Tensor] = None
    ) -> torch.Tensor:
        x = self.embedding(x)
        x = self.pos_encoder(x)
        x = self.transformer(x, src_key_padding_mask=mask)
        x = x.mean(dim=1)  # Global average pooling
        return self.classifier(x)
```

### Training Loop

```python
import torch
from torch.optim import AdamW
from torch.optim.lr_scheduler import CosineAnnealingLR
from tqdm import tqdm
import mlflow

def train_epoch(
    model: nn.Module,
    loader: DataLoader,
    optimizer: torch.optim.Optimizer,
    criterion: nn.Module,
    device: torch.device,
) -> float:
    """Train for one epoch."""
    model.train()
    total_loss = 0.0
    
    for batch in tqdm(loader, desc="Training"):
        inputs, labels = batch
        inputs, labels = inputs.to(device), labels.to(device)
        
        optimizer.zero_grad()
        outputs = model(inputs)
        loss = criterion(outputs, labels)
        loss.backward()
        
        torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=1.0)
        optimizer.step()
        
        total_loss += loss.item()
    
    return total_loss / len(loader)


def train(config: dict):
    """Main training function."""
    mlflow.start_run()
    mlflow.log_params(config)
    
    device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
    
    model = TransformerModel(**config["model"]).to(device)
    optimizer = AdamW(model.parameters(), lr=config["lr"])
    scheduler = CosineAnnealingLR(optimizer, T_max=config["epochs"])
    criterion = nn.CrossEntropyLoss()
    
    train_loader, val_loader = create_dataloaders(**config["data"])
    
    best_val_loss = float("inf")
    
    for epoch in range(config["epochs"]):
        train_loss = train_epoch(model, train_loader, optimizer, criterion, device)
        val_loss, val_acc = evaluate(model, val_loader, criterion, device)
        scheduler.step()
        
        mlflow.log_metrics({
            "train_loss": train_loss,
            "val_loss": val_loss,
            "val_accuracy": val_acc,
        }, step=epoch)
        
        if val_loss < best_val_loss:
            best_val_loss = val_loss
            torch.save(model.state_dict(), "models/best_model.pt")
    
    mlflow.end_run()
```

### LangChain / LLM Patterns

```python
from langchain.chat_models import ChatOpenAI
from langchain.prompts import ChatPromptTemplate
from langchain.output_parsers import PydanticOutputParser
from pydantic import BaseModel, Field

class Analysis(BaseModel):
    """Structured output for analysis."""
    summary: str = Field(description="Brief summary")
    sentiment: str = Field(description="Sentiment: positive, negative, neutral")
    key_points: list[str] = Field(description="Key points")

def create_analysis_chain():
    """Create LangChain for text analysis."""
    llm = ChatOpenAI(model="gpt-4", temperature=0)
    parser = PydanticOutputParser(pydantic_object=Analysis)
    
    prompt = ChatPromptTemplate.from_messages([
        ("system", "Analyze the following text and provide structured output."),
        ("system", "{format_instructions}"),
        ("human", "{text}"),
    ])
    
    chain = prompt | llm | parser
    return chain

async def analyze_text(text: str) -> Analysis:
    """Analyze text using LLM."""
    chain = create_analysis_chain()
    result = await chain.ainvoke({
        "text": text,
        "format_instructions": chain.middle[0].get_format_instructions(),
    })
    return result
```

### Configuration

```python
from pydantic import BaseModel
from typing import Optional
import yaml

class ModelConfig(BaseModel):
    vocab_size: int
    d_model: int = 512
    nhead: int = 8
    num_layers: int = 6
    num_classes: int = 2

class TrainingConfig(BaseModel):
    model: ModelConfig
    lr: float = 1e-4
    epochs: int = 10
    batch_size: int = 32
    seed: int = 42

def load_config(path: str) -> TrainingConfig:
    """Load configuration from YAML."""
    with open(path) as f:
        data = yaml.safe_load(f)
    return TrainingConfig(**data)
```

### Testing

```python
import pytest
import torch

@pytest.fixture
def model():
    return TransformerModel(vocab_size=1000, num_classes=2)

def test_model_forward(model):
    batch = torch.randint(0, 1000, (4, 32))
    output = model(batch)
    assert output.shape == (4, 2)

def test_model_training_step(model):
    model.train()
    batch = torch.randint(0, 1000, (4, 32))
    labels = torch.randint(0, 2, (4,))
    
    output = model(batch)
    loss = nn.CrossEntropyLoss()(output, labels)
    loss.backward()
    
    assert loss.item() > 0
```

## Key Guidelines

1. Use type hints for all functions
2. Track experiments with MLflow/W&B
3. Set random seeds for reproducibility
4. Use configuration files for hyperparameters
5. Implement proper data versioning (DVC)
