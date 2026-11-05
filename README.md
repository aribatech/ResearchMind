# ResearchMind: Multi-Agent Research Assistant

ResearchMind turns a single topic into a reviewed research report. Four specialized AI agents work as a pipeline: one searches the web, one reads the best source in depth, one writes the report, and one critiques it. You can run it from the terminal or through a Streamlit web app.

## How it works

```
Topic  ->  Search Agent  ->  Reader Agent  ->  Writer Chain  ->  Critic Chain  ->  Report + Feedback
```

| Step | Component | What it does |
|------|-----------|--------------|
| 1 | Search Agent | Finds recent, reliable information about the topic |
| 2 | Reader Agent | Picks the most relevant URL from the search results and scrapes it for deeper content |
| 3 | Writer Chain | Combines the search results and scraped content into a full research report |
| 4 | Critic Chain | Reviews the finished report and returns feedback |

## Features

- End-to-end research from one input: just type a topic
- Two interfaces: command line and a Streamlit web UI
- Live pipeline progress in the web UI
- Automatic retry with waiting when the LLM API rate limit is reached
- Download the final report as a Markdown file
- Raw search results and scraped content available for fact-checking

## Project structure

```
Multi Agent/
├── agents.py          # Builds the search agent, reader agent, writer chain and critic chain
├── tools.py           # Tools the agents can call (web search and page scraping)
├── pipeline.py        # Runs the full pipeline in the terminal
├── app.py             # Streamlit web UI
├── src/multi_agent/   # Package folder
├── pyproject.toml     # Project metadata and dependencies
├── uv.lock            # Locked dependency versions
└── README.md
```

## Tech stack

- Python 3.14
- [LangChain](https://python.langchain.com/) and [LangGraph](https://langchain-ai.github.io/langgraph/) for agents and chains
- [Groq](https://groq.com/) as the LLM provider (via `langchain-groq`)
- [Streamlit](https://streamlit.io/) for the web interface
- [uv](https://docs.astral.sh/uv/) for dependency management

## Getting started

### 1. Clone the project

```bash
git clone <your-repo-url>
cd "Multi Agent"
```

### 2. Install dependencies

```bash
uv sync
```

If Streamlit is missing:

```bash
uv add streamlit
```

### 3. Add your API keys

Create a `.env` file in the project root:

```env
GROQ_API_KEY=your_groq_api_key_here
```

If your search tool in `tools.py` needs its own API key, add it to the same file. Never commit `.env` to Git.

### 4. Run it

**Web UI:**

```bash
streamlit run app.py
```

Then open http://localhost:8501 in your browser.

**Terminal:**

```bash
python pipeline.py
```

You will be asked to enter a research topic, and each step prints its output as it finishes.

## Example topics

- Latest developments in AI agents
- How retrieval-augmented generation works
- FastAPI vs Flask: pros and cons

## Troubleshooting

### `RateLimitError: Error code: 429`

This means the LLM provider's token limit was reached. Free tiers are small (for example, Groq's free tier for `openai/gpt-oss-20b` allows 8000 tokens per minute), and the pipeline sends a lot of text between steps.

- The web UI retries automatically: it waits and tries again up to 5 times.
- If it keeps failing, switch to a model with higher limits in `agents.py`, or another provider such as Mistral (`pip install langchain-mistralai`, then use `ChatMistralAI`).
- Shortening the text passed from one step to the next also reduces token use.

### The scraper returns nothing or fails

Some websites block bots. Try a different topic, or run the pipeline again so the reader agent picks a different URL.

### Streamlit shows a missing module error

Make sure your virtual environment is active and dependencies are installed:

```bash
source .venv/bin/activate
uv sync
```

## Configuration tips

- **Change the model:** edit the LLM definition in `agents.py`.
- **Change the report style:** edit the writer chain prompt in `agents.py`.
- **Change how strict the critic is:** edit the critic chain prompt in `agents.py`.

## Roadmap

- Let the reader agent scrape several sources instead of one
- Show citations and source links inside the report
- Export the report as PDF
- Save and revisit past research

## License

Add your preferred license here (for example MIT).