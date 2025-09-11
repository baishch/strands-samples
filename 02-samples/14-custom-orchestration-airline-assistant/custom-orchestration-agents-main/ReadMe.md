# Custom Orchestration with Taubench

This repository contains custom orchestration code using the taubench dataset.

## Description

This project integrates taubench into a custom orchestration solution. Taubench is a benchmarking dataset that allows for standardized performance testing and analysis.

## Orchestration Patterns

### 1. ReAct (Single/Multi-Turn)
- **Single**: Direct tool execution with reasoning
- **Files**: `awsStrands-reAct_singleTurn.ipynb`

### 2. REWOO (Reasoning Without Observation)
- **Pattern**: Planner → Worker → Solver
- **Benefits**: Structured planning, deterministic execution
- **File**: `reWoo_singleTurn.ipynb`

### 3. REWOO-ReAct Hybrid
- **Pattern**: Planner → Solver (ReAct execution)
- **Benefits**: Planning reliability + execution flexibility
- **File**: `reWoo-reAct_singleTurn.ipynb`

### 4. Reflexion (Reflection and revision loop)
- **Pattern**: Draft → Revisor (self-improvement)
- **Benefits**: Quality-focused iterative refinement
- **File**: `reflexion_singleTurn.ipynb`

## Getting Started

## Project Structure

```
custom-orchestration-agents/
├── taubench/
│   ├── data/
│   │   ├── tau-bench/
│   │   │   └── tau_bench/envs/airline/
│   │   │       ├── tasks_singleturn.json    # Test questions dataset
│   │   │       ├── data/                    # Flight/user/reservation data
│   │   │       └── tools/                   # 14 airline domain tools
│   │   └── ma-bench/
│   │       └── mabench/environments/airline/
│   │           └── tools_strands/           # Strands-compatible tools
│   └── src/
│       ├── reAct_singleTurn.ipynb      # ReAct pattern
│       ├── reWoo_singleTurn.ipynb      # REWOO pattern
│       ├── reWoo-reAct_singleTurn.ipynb # Hybrid pattern
│       ├── reflexion-singleTurn.ipynb  # Reflexion pattern
│       ├── modifyToolsStrands.py       # Tool conversion script
│       └── createGT.py                 # Ground truth generator
|       |__ requirements.txt
└── README.md

### Dependencies

* Git
* Python 3.8 or higher
* pip (Python package installer)


## Setup Instructions for Taubench Dataset

### Step 1: Clone the Custom Orchestration Repository
```bash
# Clone the custom orchestration repository
git clone REPO NAME
```

### Step 2: Clone the Taubench and Mabench Repository
```bash
# Clone the taubench repository
git clone https://github.com/sierra-research/tau-bench.git
git clone https://github.com/hinthornw/mabench.git
```

### Step 3: Create Directory in Custom Orchestration Repository
```bash
# Navigate back to the custom orchestration repo
mkdir -p custom-orchestration-agents/taubench/data/tau-bench
mkdir -p custom-orchestration-agents/taubench/data/ma-bench
```

### Step 4: Copy Taubench and Mabench Content (Excluding Git Files)
```bash
# Copy all non-git related files to our repository
# Make sure to exclude .git, .github, .gitignore, etc.
rsync -av --exclude='.git*' --exclude='.github' tau-bench/ custom-orchestration-agents/taubench/data/tau-bench/
rsync -av --exclude='.git*' --exclude='.github' mabench/ custom-orchestration-agents/taubench/data/ma-bench/
```

### Step 5: Delete Taubench and Mabench Content (Excluding Git Files)
```bash
# Copy all non-git related files to our repository
# Make sure to exclude .git, .github, .gitignore, etc.
rm -rf tau-bench
rm -rf mabench
```

### Step 5: Install from source
```bash
# Navigate to the taubench data directory
cd custom-orchestration-agents/taubench/

# Install in development mode
pip install -e data/tau-bench
```

## Running Tools Modification Script

To prepare tool files for use with the Strands framework, you need to run the modifyToolsStrands.py script which adds the necessary imports, decorators, and data loading code:

```bash
# Navigate to the src directory
cd custom-orchestration-agents/taubench/src

# Run the script for the airline domain (default)
python modifyToolsStrands.py

# Or run for a different domain if needed
python modifyToolsStrands.py [domain_name]
```

This script will:
1. Add `from strands import tool` import if not present
2. Add `from mabench.environments.airline.data import load_data` import if needed
3. Add `@tool` decorator to tool functions if not present
4. Replace `data = get_data()` calls with `data = load_data()`

## Creating Ground Truth Data

To generate ground truth data for the airline tasks, you can run the createGT_airline.py script:

```bash
# Navigate to the src directory
cd custom-orchestration-agents/taubench/src

# Run the script to generate ground truth data
python createGT_airline.py
```

This script:
1. Converts task instructions into natural language questions using the Claude model via AWS Bedrock
2. Generates appropriate tool outputs for each action in the tasks
3. Saves the updated tasks with questions and action results to `tasks_singleturn.json`

Note: This script requires AWS credentials with access to Bedrock. Make sure your AWS credentials are properly configured before running this script.

## How to Run

1. **Start Jupyter**: Launch Jupyter notebook in the project directory
   ```bash
   jupyter notebook
   ```

2. **Open Pattern Notebook**: Select any orchestration pattern notebook (e.g., `awsStrands-reAct_singleTurn.ipynb`)

3. **Configure Question ID**: Change the question index in the final cell to test different scenarios:
   ```python
   # Modify this line to select different questions (0-based index)
      question_id = 43
      task = tasks[question_id]
      user_query = task["question"]
      print(user_query)
      
   ```

4. **Execute Cells**: Run all cells sequentially - each notebook uses:
   - **Dataset**: `tasks_singleturn.json` from tau-bench airline environment
   - **Tools**: 14 airline domain tools (booking, search, updates)
   - **Model**: Claude 3 Sonnet via AWS Bedrock

## Usage

Each notebook demonstrates a different orchestration pattern with the same airline domain tools, enabling direct comparison of approaches for complex multi-step flight booking scenarios.