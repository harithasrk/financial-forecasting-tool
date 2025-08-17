# Financial Forecasting Tool

A RAG-based system for analyzing Carrier Corporation's earnings data and making financial forecasts. Built this to automate the tedious process of digging through quarterly reports to answer specific financial questions.

## What it does

- Loads quarterly earnings reports (in markdown format) into a searchable database
- Answers questions about financial metrics, trends, and performance
- Makes forecasts based on historical data patterns
- Runs entirely locally using Ollama (no API costs or data privacy concerns)

## Example queries it handles well

- "What was Carrier's GAAP EPS in Q1 2024?"
- "Forecast Carrier's operating margin for Q4 2024 based on historical trends"
- "Show me EPS trends over the last 8 quarters"
- "What are the main business segments?"

## Requirements

You'll need these installed:
- Python 3.8+
- [Ollama](https://ollama.ai/) running locally
- A few GB of disk space for the models and vector database

## Quick start

1. **Get Ollama running:**
   ```bash
   # Install ollama, then pull the models
   ollama pull llama3
   ollama pull nomic-embed-text
   ```

2. **Clone and setup:**
   ```bash
   git clone https://github.com/harithasrk/financial-forecasting-tool.git
   cd financial-forecasting-tool
   pip install -r requirements.txt
   ```

3. **Configure your data path:**
   ```bash
   cp .env.example .env
   # Edit .env to point to your markdown files
   ```

4. **Run the notebook:**
   ```bash
   jupyter notebook query_data_2.ipynb
   ```

## How to use it

### Jupyter notebook (easiest way)
Just open `query_data_2.ipynb` and run the cells. The last cell has example questions you can try.

### Python script
```python
from src.financial_rag import CarrierRAGSystem

# Point it to your data
config = {"data_path": "/path/to/your/markdown/files"}
rag = CarrierRAGSystem(config)

# Build the search index (takes a few minutes first time)
rag.build_vectorstore()

# Ask questions
result = rag.answer_query("What was the revenue growth in Q2 2024?")
print(result["answer"])
```

### Interactive mode
```python
rag.interactive_query()  # Starts a command-line chat interface
```

## Configuration

Main settings you can tweak in `.env`:

- `DATA_PATH`: Where your markdown earnings files live
- `CHAT_MODEL`: Which Ollama model to use (llama3, mistral, etc.)
- `EMBED_MODEL`: Embedding model for search (nomic-embed-text works well)
- `CHUNK_SIZE`: How to split documents (1000 chars is usually good)

## Data format

Drop your earnings report markdown files in a folder like this:
```
earnings_data/
├── Q1_2024.md
├── Q2_2024.md
├── Q3_2024.md
└── ...
```

The system will automatically load and index all `.md` files it finds.

## Performance notes

- First run takes longer (building the vector database)
- Subsequent runs are much faster (loads existing database)
- More data = better forecasting, but slower initial setup
- Works best with at least 8-12 quarters of data

## Limitations

- Only as good as the data you feed it
- Forecasts are based purely on historical patterns (no external market data)
- Sometimes makes up numbers if it can't find exact figures (working on fixing this)
- Requires decent amount of RAM for larger datasets

## Troubleshooting

**"Ollama not running"**: Make sure Ollama is installed and the service is running on port 11434

**"No .md files found"**: Check your DATA_PATH in the .env file

**"Memory errors"**: Try reducing BATCH_SIZE in the config

**"Slow performance"**: The vector database gets big with lots of data. Consider using an SSD.
