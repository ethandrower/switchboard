
---

# 🎛️ Switchboard

### The VRAM-Aware Semantic Router for Scrappy LangGraph Agents.

**Switchboard** is a drop-in LangGraph node that intelligently routes tasks between your local LLMs (via Ollama) and SOTA cloud APIs. It stops you from wasting money on "easy" cloud calls and stops you from wasting time on "complex" local hallucinations.

## 🚀 Why Switchboard?

Most routers are just "LLM-as-a-Judge." Switchboard is different. It’s built for the **12GB VRAM crowd**:

* **VRAM-Aware:** It knows if `mistral-small` is already loaded in your GPU. If it is, it routes there to avoid the 5-second "hot swap" delay.
* **Semantic Tiering:** Uses a tiny, sub-10ms embedding model to classify tasks before hitting a "real" LLM.
* **Budget-First:** Set a monthly cap. Once reached, Switchboard automatically aggressively routes to local models unless a task is flagged as "Mission Critical."
* **LangGraph Native:** Designed specifically as a `Conditional Edge` logic for your graphs.

## 📦 Installation

```bash
pip install switchboard-ai

```

*Note: Requires a running Ollama instance on your Linux desktop.*

## 🛠️ Quick Start

Drop it into your existing LangGraph workflow as the "brain" that decides which model handles the next node.

```python
from switchboard import VRAMRouter
from langgraph.graph import StateGraph

# Initialize the router with your specific hardware profile
router = VRAMRouter(
    local_url="http://100.x.x.x:11434", # Your Tailscale Linux IP
    vram_limit_gb=12,
    threshold=0.7 # Intelligence threshold (0.0 to 1.0)
)

def route_logic(state):
    # Switchboard analyzes the prompt and VRAM state
    return router.decide(state["next_task"])

workflow = StateGraph(MyState)
workflow.add_conditional_edges("supervisor", route_logic, {
    "local": "research_node", # Runs on Linux Desktop
    "cloud": "ceo_node"       # Runs on Claude 4.5
})

```

## 🧠 Logic Flow

1. **Classification:** A fast embedding check determines: *Is this a "Routine" (PM/Standup) or "Complex" (Strategic Research) task?*
2. **Hardware Check:** Is the required local model already in the GPU? (If yes, +10 to the "Local" score).
3. **Decision:** - **Local:** Route to Ollama (Mistral 12B or Qwen 14B).
* **Cloud:** Route to Anthropic/OpenAI (if reasoning requirements are high).



## 🗺️ Roadmap

* [ ] **Ollama Hot-Swap Prediction:** Predict the next model needed and pre-load it into System RAM.
* [ ] **Jira/Slack Connector Nodes:** Pre-built logic for "Waiting for Human" states.
* [ ] **The "Savings" CLI:** A command to see exactly how much money Switchboard saved you this month.

---

