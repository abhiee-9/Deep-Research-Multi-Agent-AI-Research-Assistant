# 🔎 Deep Research — Multi-Agent AI Research Assistant

A Multi-agent research system that takes a single natural-language query and turns it into a fully-cited, structured research report - planned, searched, written, and delivered end-to-end without manual intervention.

Built on the **OpenAI Agents SDK**, orchestrating four specialized agents that plan searches, execute them, synthesize findings, and email the final report - all surfaced through a custom-branded **Gradio** interface with live status streaming.

## ✨ Features

- **Multi-agent orchestration** - Four purpose-built agents collaborate in a pipeline, each with a narrow responsibility instead of one monolithic prompt:
  - **Planner Agent** - breaks the query into a configurable number of targeted web-search terms, each with a stated rationale (structured output via Pydantic).
  - **Search Agent** - runs each search using OpenAI's `WebSearchTool` and condenses results into a tight 2–3 paragraph summary.
  - **Writer Agent** - synthesizes all search summaries into a long-form (1,000+ word) markdown report, plus a short summary and suggested follow-up questions.
  - **Email Agent** - converts the finished report into a clean HTML email (with a plain-text fallback) and sends it automatically.
- **Async, streaming UX** - The Gradio UI streams live status updates ("Searches planned...", "Writing report...", "Email sent...") as the pipeline progresses, instead of a single blocking call.
- **Parallelized search execution** - All planned searches run concurrently via `asyncio.gather` rather than sequentially, cutting total research time significantly.
- **Traceable runs** - Every research run is wrapped in an OpenAI trace with a shareable trace URL, making the full agent-to-agent handoff inspectable for debugging.
- **Graceful delivery fallback** - If email isn't configured, reports are pushed via Pushover instead, controlled by a single `USE_EMAIL` environment flag.
- **Custom UI theme** - Hand-built CSS/JS on top of Gradio's `Blocks`: monospace/brutalist branding, dark-mode support, and auto-focus on the query field.

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Agent Orchestration | OpenAI Agents SDK (`Agent`, `Runner`, `trace`) |
| LLM | OpenAI GPT models (configurable via `DEFAULT_MODEL_NAME`) |
| Structured Outputs | Pydantic (`BaseModel`, `Field`) |
| Web Search | OpenAI `WebSearchTool` |
| UI | Gradio (`Blocks`) with custom CSS/JS |
| Delivery | SMTP (email) + Pushover (push notification fallback) |
| Config | `python-dotenv` |

## 📂 Project Structure

```
.
├── app.py               # Gradio app entrypoint (branded UI, streaming output)
├── simple.py             # Minimal Gradio entrypoint (no custom styling)
├── research_manager.py     # Orchestrates the planner → search → writer → email pipeline
├── planner_agent.py        # Agent: turns a query into a structured web-search plan
├── search_agent.py         # Agent: executes a web search and summarizes results
├── writer_agent.py         # Agent: synthesizes search results into a full report
├── email_agent.py          # Agent: converts the report into HTML and sends it
├── messenger.py           # Email (SMTP) + Pushover delivery helpers
├── styles.py             # Custom CSS, JS, header markup, and example queries
└── requirements.txt
```

## ⚙️ How It Works

1. A visitor submits a research question through the Gradio UI (`app.py` / `simple.py`).
2. `research_manager.py` kicks off a traced run: the **Planner Agent** returns a `WebSearchPlan` — a structured list of search terms with reasoning.
3. Each planned search is dispatched concurrently to the **Search Agent**, which uses `WebSearchTool` to search the web and return a concise summary.
4. All summaries are passed to the **Writer Agent**, which produces a `ReportData` object: a short summary, a long-form markdown report, and follow-up research questions.
5. The **Email Agent** takes the finished report, drafts an HTML email with an appropriate subject line, and sends it via SMTP (or pushes a notification if email is disabled).
6. Status updates are yielded back to the UI at every stage, and the final markdown report is rendered directly in the browser.

## 🚀 Running Locally

```bash
# 1. Clone the repo
git clone https://github.com/abhiee-9/deep-research.git
cd deep-research

# 2. Install dependencies
pip install -r requirements.txt

# 3. Configure environment variables in a .env file
OPENAI_API_KEY=your_openai_api_key
DEFAULT_MODEL_NAME=gpt-5.4-mini
HOW_MANY_SEARCHES=5

USE_EMAIL=true
EMAIL_ADDRESS=your_email@example.com
EMAIL_SMTP_SERVER=smtp.your-provider.com
EMAIL_APP_PASSWORD=your_app_password

# Optional fallback if USE_EMAIL=false
PUSHOVER_USER=your_pushover_user_key
PUSHOVER_TOKEN=your_pushover_app_token

# 4. Launch the app
python app.py
```

The app starts a local Gradio server where you can submit a research query and watch the agent pipeline plan, search, write, and deliver a report in real time.

## 💡 Why I Built This

I wanted hands-on depth with **multi-agent systems** - going beyond a single LLM call with tools to a pipeline where specialized agents hand structured outputs to one another, run concurrently, and are traceable end-to-end. It's a direct extension of my move toward AI-driven data and automation engineering: the same orchestration and structured-output patterns here apply directly to building reliable, production-style agentic data pipelines.

## 📬 Contact

**Abhijeet Patil**
[LinkedIn](https://www.linkedin.com/in/abhijeetpatil9/) · [GitHub](https://github.com/abhiee-9) · abhijeetpatil0922@gmail.com
