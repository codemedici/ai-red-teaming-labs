# 🛡️ AI Red-Teaming Labs

Mission-driven, end-to-end security scenarios for professional LLM red & blue teams.
Each mission spins up a complete attack surface (chatbot, RAG, autonomous agent, or model-supply-chain) and embeds **multiple vulnerabilities** you’ll encounter in real production environments.

> **Why another lab repo?**
> Traditional “one bug = one lab” kits don’t reflect how modern AI attacks unfold.
> Real operators chain vulnerabilities, pivot between services, and evade layered defenses.
> These labs model that reality.

---

## ✨ Key Features

| Feature                     | Description                                                                                                                                                         |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Mission-based**           | Five flagship missions, each mirroring a real-world deployment (public chatbot, vector RAG, plugin agent, finetuned model marketplace, side-channel on-prem model). |
| **Modular vulnerabilities** | Re-usable modules (`shared/vulns/`) toggled via YAML: prompt-injection, insecure tool call, training backdoor, pickle RCE, timing channels, etc.                    |
| **Real models**             | Ships with an offline llama-cpp container (Llama-2-7B-Q4\_K\_M). Swap to OpenAI, Bedrock, or SageMaker via one config line.                                         |
| **Red-team tooling ready**  | Preset configs for **Garak**, **PyRIT**, **PromptBench**, plus custom exploit scripts.                                                                              |
| **Blue-team observability** | OpenLLMetry tracing, Prometheus metrics, Loki logs, Grafana dashboards, guardrail adapters (Lakera Guard, LLMGuard).                                                |
| **CTF support**             | Scoreboard micro-service, per-team flags, OAuth login, rate-limiting.                                                                                               |
| **CI / Dev-container**      | GitHub Actions builds every mission, runs nbval on notebooks, lints code, and validates exploits continue to succeed.                                               |

---

## 📦 Repository Layout

```
ai-red-teaming-labs/
├─ core/
│  ├─ bootstrap/            # docker-compose, terraform modules
│  ├─ telemetry/            # OpenLLMetry exporter, Grafana dashboards
│  └─ shared/
│       ├─ vulns/           # reusable vulnerability modules
│       ├─ guardrails/      # Lakera / LLMGuard wrappers
│       └─ utils.py
├─ missions/                # Scenario folders
│  ├─ M1_phish-portal/
│  │   ├─ mission.yaml
│  │   ├─ docker-compose.yml
│  │   ├─ notebook.ipynb
│  │   └─ flags/
│  ├─ M2_supply-chain/
│  ├─ M3_rag-breakout/
│  ├─ M4_agent-escalation/
│  └─ M5_hardware-sidechan/
└─ README.md
```

---

## 🚀 Getting Started (local, offline)

```bash
git clone https://github.com/codemedici/ai-red-teaming-labs.git
cd ai-red-teaming-labs

# spin up mission 1
make up-M1_phish-portal      # builds & starts docker-compose in detached mode

# follow logs
make logs-M1_phish-portal    # tail -f the FastAPI gateway

# open red-team notebook
jupyter notebook missions/M1_phish-portal/notebook.ipynb
```

> **Hardware:** 16 GB RAM recommended (7B gguf model).
> **Online alt:** set `MODEL_BACKEND=openai` in `mission.yaml` and export `OPENAI_API_KEY`.

---

## 🎯 Missions Overview

| Mission                   | Real-world Target                     | Weaknesses Chained                               | Flag Location  |
| ------------------------- | ------------------------------------- | ------------------------------------------------ | -------------- |
| **M1 Phish-Portal**       | Public airline chatbot (Azure OpenAI) | Prompt-inj → output filter bypass → PII dump     | env var in pod |
| **M2 Supply-Chain**       | Private PyPI finetune wheel           | Pickle RCE → weight backdoor trigger             | S3 bucket file |
| **M3 RAG Breakout**       | Internal wiki Q\&A with pgvector      | Indirect inj → vector poison → lateral doc pivot | hidden HR PDF  |
| **M4 Agent Escalation**   | LangChain agent (browser + shell)     | Jailbreak → insecure tool call → OS RCE          | `/tmp/flag`    |
| **M5 Hardware Side-Chan** | On-prem llama-cpp                     | Per-token timing leak → covert exfil             | timing channel |

---

## 🛠 Red-Team Tool Quick Start

```bash
# Garak recon against Mission 1
garak http://localhost:8080/chat -p garak_profiles/recon.toml -o garak_report.html

# PyRIT fuzz policy
pyrit run pyrit_profiles/airline.yaml --url http://localhost:8080/chat
```

---

## 🛡 Blue-Team Observability

| Port | Service                             |
| ---- | ----------------------------------- |
| 8080 | FastAPI gateway                     |
| 3000 | Grafana dashboard (`admin / admin`) |
| 9090 | Prometheus                          |
| 3100 | Loki log store                      |

---

## 🤝 Contributing

1. Fork → feature branch
2. Add / modify a **vulnerability module** or **mission**
3. Run `make ci` locally (black, mypy, pytest, nbval)
4. PR → template auto-runs full CI matrix.
   *Merging requires all exploits still pass.*

---

## 📜 License

MIT © 2025 – cosimo de Medici.
Third-party models and Docker images are licensed separately—see each mission’s README.

---

*Happy hacking & stay secure!* 💀
