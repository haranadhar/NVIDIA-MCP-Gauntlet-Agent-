# NVIDIA-MCP-Gauntlet-Agent-
Track 1 — Competition agent
Goal: finish a working end-to-end agent within two days, by November 21.

Do not touch Fuzzball until this track reaches “definition of done.”
Track 1 outcome
By the end of two days, you should have:
[ ] Python 3.12 environment working
[ ] Arena doctor passing
[ ] Baseline agent completed
[ ] Customized strategy completed
[ ] MCP tools discovered dynamically
[ ] Text challenge completed
[ ] Image path tested or verified
[ ] Timeout fallback present
[ ] Agent packaged successfully
[ ] Submission-ready tarball created
Day 1 — Setup, baseline, and architecture
Block 1: 0–60 minutes — Mac setup
cd ~/Documents
git clone https://github.com/HarshiniVenkat/Agent-Gauntlet-Starter-Kit.git
cd Agent-Gauntlet-Starter-Kit

python3.12 -m venv .venv
source .venv/bin/activate

python -m pip install --upgrade pip
python -m pip install -r requirements.txt

cp .env.example .env
open -e .env
Set:
ARENA_SERVER=https://gauntlet-practice.136.85.160.148.sslip.io
ARENA_API_KEY=AGENT_GAUNTLET_DC
AGENT_ID=team-yourname
Run:
python -m arena_clients.doctor
Stop and fix setup problems until the doctor passes.
Block 2: 60–120 minutes — Run the baseline
python examples/python_simple/agent.py
Confirm that:
- The agent registers.
- It discovers the MCP server.
- It receives a challenge.
- It calls the LLM proxy.
- It submits an answer.
- A score is returned.
Do not modify code before completing one baseline run.
Block 3: 2–4 hours — Read only the important files
Read:
AGENTS.md
base_strategy.py
my_strategy.py
examples/python_simple/agent.py
docs/discovering-tools.md
docs/submitting.md
Your understanding should be:
register
→ discover tools
→ prepare prompt
→ call model
→ optionally call MCP tools
→ format answer
→ submit
The starter kit identifies my_strategy.py as the primary customization point and supports hooks for model selection, prompts, tools, image handling, and timeout behavior. 
Block 4: 4–6 hours — Customize my_strategy.py
Change:
- agent_id
- agent_name
- Text system prompt
- Text strategy notes
- Temperature
- Token limit
- Image strategy notes
Use a prompt similar to:
text_system_prompt = """
You are an autonomous challenge-solving agent.

Read the challenge carefully.
Follow the required output format exactly.
Use available tools only when they are relevant.
Check calculations, decoded values, extracted values, and ordering.
Do not invent tool results.
Submit a valid answer before timeout.

Your first line must be exactly:
ANSWER: <final answer>

Keep reasoning concise.
"""
Use:
text_temperature = 0.0
text_max_tokens = 8192
Block 5: 6–8 hours — Run experiments
Run at least five Practice attempts:
python examples/python_simple/agent.py
Create a simple log:
run-01:
challenge:
score:
tools:
model:
latency:
failure:
change:
Change only one thing between runs.

Prioritize:
1. Correct answer format
2. Tool discovery
3. Model selection
4. Timeout safety
5. Verification
Day 2 — Reliability, packaging, and submission readiness
Block 1: 0–2 hours — Confirm MCP discovery
Your agent must call the equivalent of:
tool_names = await mcp.list_tools()
tool_defs = await mcp.list_tool_defs()
It should inspect:
- Tool name
- Description
- Input schema
- Required parameters
Do not hardcode Practice-specific tool names. The tool list can differ between Practice and the live event. 
Block 2: 2–4 hours — Add reliability behavior
Verify that the agent:
- Does not call the same tool repeatedly without a reason.
- Does not spend the full time exploring.
- Submits a best-effort answer before timeout.
- Uses the required ANSWER: format.
- Sends a progress thought before submission.
- Handles tool errors without crashing.
The core policy should be:
Discover → select → call → interpret → verify → submit
Block 3: 4–6 hours — Test text and image behavior
Run enough Practice attempts to cover both modalities if Practice rotates them.

For image challenges:
- Prefer image-capable models.
- Keep output resolution standard.
- Avoid HD/4K unless required.
- Do not add image processing complexity unless the challenge needs it.
If image testing is unavailable, at minimum verify that the image hooks do not crash and that the agent can select an image-capable model.
Block 4: 6–7 hours — Package
From the repository root:
python -m arena_clients.doctor

python -m arena_clients.package \
  --agent-id team-yourname \
  --agent-name "Your Team"

python -m arena_clients.package \
  --check dist/gauntlet-submission
Expected output:
dist/team-yourname-submission.tar.gz
dist/team-yourname-submission.tar.gz.sha256
The package must contain the runtime, strategy, entrypoint, and locked dependencies. The public submission repository should contain exactly those two files at its root. 
Block 5: 7–8 hours — Freeze candidate
Create a Git commit:
git add my_strategy.py
git commit -m "Finalize first competition agent"
Do not make large architectural changes after this point.
Track 1 definition of done
Track 1 is complete when this succeeds:
python -m arena_clients.doctor
python examples/python_simple/agent.py
python -m arena_clients.package --check dist/gauntlet-submission
At that point, you have a viable competition candidate.
Track 2 — CIQ Fuzzball learning track
Start this only after Track 1 works.

Goal:
Learn how AI workloads, agents, containers, GPUs, and workflows are orchestrated through infrastructure.

Fuzzball is designed to run reproducible HPC and AI workflows across clusters and cloud environments. It can manage jobs, services, storage, model workloads, and GPU resources. 
Fuzzball Phase 1 — Learn the platform
Understand these concepts:
Workflow       collection of jobs and services
Job            finite computation
Service        long-running endpoint
Artifact       output produced by a job
Container      reproducible runtime
Scheduler      decides where work runs
GPU request    compute requirement
MCP server     agent interface to infrastructure
Your first goal is not model training. It is running one simple workflow successfully.
Fuzzball Phase 2 — Run a basic CPU workflow
Start with a small containerized job:
1. Pull a Python container.
2. Run a script.
3. Write a result file.
4. Save the result as an artifact.
5. Inspect the job logs.
Do not begin with multi-GPU training, Kubernetes, distributed inference, or fine-tuning.
Fuzzball Phase 3 — Run an agent evaluation workflow
Create a workflow that:
1. Installs or loads your Gauntlet environment.
2. Runs the agent.
3. Captures score and latency.
4. Stores the result.
5. Makes the result available for comparison.
This connects Fuzzball directly to your competition work without changing the competition agent.

Conceptually:
Fuzzball workflow
 ├── setup environment
 ├── run agent
 ├── collect score
 ├── collect latency
 └── store evaluation artifact
Fuzzball Phase 4 — Add GPU work
Only after the CPU workflow works:
1. Request one GPU.
2. Run a small inference task.
3. Measure startup time.
4. Measure inference latency.
5. Compare local Mac versus GPU execution.
You do not need to train a model. The learning objective is infrastructure operation.
Fuzzball Phase 5 — Explore MCP infrastructure control
If your Fuzzball access includes its MCP server, study how an agent could:
inspect available environments
inspect workflow templates
draft a workflow
submit a workflow
monitor a job
retrieve logs
inspect artifacts
Keep write/run operations approval-controlled. CIQ describes Fuzzball’s agent interface as allowing infrastructure inspection and workflow control within configured permissions. 
What not to do in Track 2
Do not:
- Move the competition agent to Fuzzball before it works locally.
- Rent RunPod or Lambda Labs during the first two days.
- Train a model.
- Build a new frontend.
- Rewrite the starter kit in LangGraph or CrewAI immediately.
- Create a complex Kubernetes deployment.
- Add infrastructure features that do not improve your score.
Recommended order
Track 1:
Mac setup
→ baseline
→ strategy
→ MCP discovery
→ reliability
→ package

Track 2:
Fuzzball basics
→ CPU workflow
→ agent evaluation workflow
→ GPU workflow
→ infrastructure MCP
