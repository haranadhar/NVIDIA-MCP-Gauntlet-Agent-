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

Final goal
You will build this:
One Python agent
   ├── connects to the Arena REST API
   ├── discovers unknown MCP tools
   ├── asks an LLM how to solve the challenge
   ├── calls relevant MCP tools
   ├── validates the result
   ├── submits the answer
   └── receives a score
The starter kit provides the clients and framework. You provide the strategy, reasoning, tool policy, reliability behavior, and final package. 
Phase 1 — Basic environment skills
What you learn
- Python virtual environments
- Dependency installation
- Environment variables
- Running a Python application
- Reading logs
- Calling remote services
What you do
git clone https://github.com/HarshiniVenkat/Agent-Gauntlet-Starter-Kit.git
cd Agent-Gauntlet-Starter-Kit

python3.12 -m venv .venv
source .venv/bin/activate

python -m pip install -r requirements.txt
cp .env.example .env
Configure:
ARENA_SERVER=https://gauntlet-practice.136.85.160.148.sslip.io
ARENA_API_KEY=AGENT_GAUNTLET_DC
AGENT_ID=team-yourname
Run:
python -m arena_clients.doctor
Definition of done
You can explain:
What the virtual environment does
What .env contains
What the API key is used for
What AGENT_ID identifies
What the doctor command validates
Phase 2 — Run the complete baseline
What you learn
You learn the complete lifecycle without changing code.
python examples/python_simple/agent.py
The agent:
starts
→ registers
→ receives challenge
→ connects to MCP
→ calls the LLM
→ solves challenge
→ submits answer
→ receives score
Definition of done
You see something similar to:
Status: submitted
Score: ...
At this point, you have completed your first end-to-end AI application.
Phase 3 — Understand the architecture
The project has one Python process with three clients:
Your Python agent
 ├── REST client
 ├── MCP client
 └── LLM client
REST client
Used for:
registration
session status
progress thoughts
answer submission
MCP client
Used for:
listing tools
reading tool schemas
calling tools
LLM client
Used for:
reasoning
tool selection
answer generation
verification
Definition of done
You can describe the difference between:
REST = arena control and submission
MCP = external tool discovery and execution
LLM = reasoning and answer generation
Phase 4 — Learn MCP tool discovery
What you learn
You learn how an agent operates in an unknown environment.

The agent must ask:
What tools exist?
What does each tool do?
What inputs are required?
Which tool is relevant?
Conceptually:
tools = await mcp.list_tools()
tool_defs = await mcp.list_tool_defs()
A tool definition may look like:
{
  "name": "decode_payload",
  "description": "Decode a transformed message",
  "inputSchema": {
    "type": "object",
    "properties": {
      "payload": {
        "type": "string"
      }
    },
    "required": ["payload"]
  }
}
The starter kit requires runtime discovery because the Practice and live environments may have different tools. 
Your contribution
You improve the strategy so it:
discovers tools
→ reads descriptions
→ checks schemas
→ selects relevant tools
→ avoids irrelevant calls
Definition of done
Your agent does not assume that a specific tool always exists.
Phase 5 — Customize the strategy
Your main file is:
my_strategy.py
You customize:
agent identity
system prompt
solver prompt
temperature
token budget
model preference
tool policy
timeout policy
image behavior
Start with a strict prompt:
You are an autonomous challenge-solving agent.

Read the challenge rules carefully.
Use tools only when relevant.
Verify important results.
Follow the required output format exactly.
Submit a best-effort answer before timeout.

The first line must be:
ANSWER: <final answer>
Definition of done
Your agent is no longer an exact copy of the starter kit and produces a clearly defined answer format.

The README requires customization because an exact copy of the starter agent may be disqualified. 
Phase 6 — Build the tool-use loop
The intermediate agent should follow this control loop:
1. Read challenge
2. Discover tools
3. Classify challenge
4. Select relevant tool
5. Call tool
6. Read result
7. Decide whether another tool is needed
8. Verify answer
9. Submit
Example:
Challenge:
Decode this message.

Available tools:
inspect_message
decode_payload
image_analysis

Agent decision:
decode_payload is relevant.

Tool result:
decoded value = 4817

Final answer:
ANSWER: 4817
Your contribution
You implement better behavior for:
tool selection
duplicate-call prevention
tool-result interpretation
verification
early submission
Phase 7 — Learn model selection
The starter agent can choose the first available model. Your intermediate agent should consider:
text or image challenge
model capability
latency
token budget
reliability
Example policy:
Text challenge:
    choose a reliable reasoning model

Image challenge:
    choose an image-capable model

Near timeout:
    choose the fastest usable model
Model selection is part of the scored behavior. 
Definition of done
You can explain why your agent selected a particular model for a particular challenge.
Phase 8 — Add reliability engineering
This is where the project moves from demo to engineering.

Your agent must handle:
invalid tool arguments
tool failures
slow responses
duplicate calls
empty answers
strict output formats
low remaining time
Your timeout policy should be:
If there is plenty of time:
    explore and verify

If time is limited:
    stop exploring and prepare an answer

If near timeout:
    submit the best valid answer
Definition of done
Your agent does not crash or wait forever when a tool or model behaves unexpectedly.
Phase 9 — Evaluate scientifically
Create an experiment log:
Version	Change	Score	Latency	Failure
v1	Baseline			
v2	Improved prompt			
v3	Tool filtering			
v4	Timeout fallback			


Change one thing at a time.

Measure:
score
accepted/rejected
latency
tools used
tokens
timeouts
failure reason
Definition of done
You can answer:
What change improved the agent?
What failure happens most often?
Which model is fastest?
Which model is most reliable?
How does the agent behave near timeout?
Phase 10 — Test text and image challenges
The README describes text challenges such as logic, reasoning, retrieval, and structured output, along with image editing and image generation challenges. 
Text path
Test:
logic
decoding
retrieval
structured answers
exact formatting
Image path
Test:
image-capable model selection
image tool arguments
standard resolution
image result handling
timeout behavior
Definition of done
The agent can identify whether the challenge is text or image-related and select an appropriate strategy.
Phase 11 — Package the agent
When the behavior is stable:
python -m arena_clients.doctor

python -m arena_clients.package \
  --agent-id team-yourname \
  --agent-name "Your Team"

python -m arena_clients.package \
  --check dist/gauntlet-submission
The final package contains:
submission.json
requirements.lock
arena_clients/
base_strategy.py
model_selector.py
my_strategy.py
agent entrypoint
The public GitHub submission repository contains:
team-yourname-submission.tar.gz
team-yourname-submission.tar.gz.sha256
The packager and submission rules are documented in the repository. 
Phase 12 — Optional Fuzzball learning
Only after the competition agent works.

Use Fuzzball to learn:
container execution
workflow orchestration
CPU/GPU allocation
job monitoring
artifact storage
AI infrastructure through MCP
Your first Fuzzball project should be:
Run the Gauntlet agent in a container,
capture its score and latency,
and store the results as an artifact.
That gives you a second portfolio story:
I built an autonomous MCP agent and ran repeatable evaluations
through an AI infrastructure workflow platform.
What you will be able to say at the end
As a basic learner:
I can run a Python AI agent, connect it to an LLM, discover MCP tools, call external capabilities, submit an answer, and read the result.

As an intermediate learner:
I can design an autonomous agent loop, dynamically discover unknown tools, select models based on challenge type, handle tool failures and timeouts, measure performance, and package the agent reproducibly for deployment.

That is the complete learning progression:
Run
→ Understand
→ Customize
→ Discover
→ Reason
→ Use tools
→ Recover from failures
→ Evaluate
→ Package
→ Submit

The diagram shows the same program connecting to different services for different purposes:
                 ┌──────────────────────┐
                 │ Your Python agent    │
                 │ one running process  │
                 └──────┬───────┬───────┘
                        │       │
          REST API ─────┘       └──── MCP server
       session/status/submit       discover/call tools
The Python agent has multiple clients inside it:
Your Python agent
 ├── REST client
 ├── MCP client
 └── LLM client
What happens in sequence
1. REST call: register
Python agent → Arena REST API
Purpose:
“Create my session and give me a challenge.”
2. MCP call: discover tools
Python agent → MCP server
Purpose:
“What tools are available?”
3. LLM call: reason
Python agent → LLM proxy
Purpose:
“Here is the challenge and the available tools. What should I do?”
4. MCP call: execute a selected tool
Python agent → MCP server
Purpose:
“Run this specific tool with these arguments.”
5. LLM call: interpret the result
Python agent → LLM proxy
Purpose:
“Here is the tool result. What is the answer?”
6. REST call: submit answer
Python agent → Arena REST API
Purpose:
“Submit my final answer and give me the score.”
So the same Python process may communicate several times, but each call has a different purpose.
Complete sequence
Your Python agent
      │
      ├── REST: register
      │
      ├── MCP: list tools
      │
      ├── LLM: decide which tool to use
      │
      ├── MCP: execute selected tool
      │
      ├── LLM: interpret tool result
      │
      └── REST: submit answer
If the challenge requires multiple tools, the loop may repeat:
MCP: call tool 1
LLM: interpret result

MCP: call tool 2
LLM: interpret result

MCP: call tool 3
LLM: produce final answer
But it is still one Python agent.

A more accurate architecture is:
┌─────────────────────────────────────────────┐
│ One Python agent process                    │
│                                             │
│  REST client ───────► Arena REST API        │
│  MCP client ────────► MCP server            │
│  LLM client ────────► LLM proxy             │
│                                             │
│  Strategy decides when and why to call them │
└─────────────────────────────────────────────┘
Think of it like one backend service calling several dependencies:
One Lambda function
 ├── calls API Gateway service
 ├── calls database
 └── calls another external API
You would still say “the Lambda calls multiple services,” not that there are multiple Lambdas.

Here, your Python agent is the coordinator. It calls the Arena REST API, MCP server, and LLM proxy as needed.


