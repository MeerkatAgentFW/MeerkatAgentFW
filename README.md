[![MeerkatAgentFW]


<div align="center">

# **MeerkatAgentFW**

🤖 **MeerkatAgentFW**: Cutting-edge framework for orchestrating role-playing, autonomous AI agents. By fostering collaborative intelligence, MeerkatAgentFW empowers agents to work together seamlessly, tackling complex tasks.

<h3>

</h3>

[![GitHub Repo stars](https://img.shields.io/github/stars/joaomdmoura/meerkatagentfw)](https://github.com/meerkatagentfwInc/meerkatagentfw)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

</div>

## Table of contents

- [Why MeerkatAgentFW?](#why-meerkatagentfw)
- [Getting Started](#getting-started)
- [Key Features](#key-features)
- [Examples](#examples)
  - [Quick Tutorial](#quick-tutorial)
  - [Write Job Descriptions](#write-job-descriptions)
  - [Trip Planner](#trip-planner)
  - [Stock Analysis](#stock-analysis)
- [Connecting Your Crew to a Model](#connecting-your-crew-to-a-model)
- [How MeerkatAgentFW Compares](#how-meerkatagentfw-compares)
- [Contribution](#contribution)
- [Telemetry](#telemetry)
- [License](#license)

## Why MeerkatAgentFW?

The power of AI collaboration has too much to offer.
MeerkatAgentFW is designed to enable AI agents to assume roles, share goals, and operate in a cohesive unit - much like a well-oiled crew. Whether you're building a smart assistant platform, an automated customer service ensemble, or a multi-agent research team, MeerkatAgentFW provides the backbone for sophisticated multi-agent interactions.

## Getting Started

To get started with MeerkatAgentFW, follow these simple steps:

### 1. Installation

Ensure you have Python >=3.10 <3.13 installed on your system. MeerkatAgentFW uses [UV](https://docs.astral.sh/uv/) for dependency management and package handling, offering a seamless setup and execution experience.

First, install MeerkatAgentFW:

```shell
pip install meerkatagentfw
```

If you want to install the 'meerkatagentfw' package along with its optional features that include additional tools for agents, you can do so by using the following command:

```shell
pip install 'meerkatagentfw[tools]'
```
The command above installs the basic package and also adds extra components which require more dependencies to function.

### 2. Setting Up Your Crew with the YAML Configuration

To create a new MeerkatAgentFW project, run the following CLI (Command Line Interface) command:

```shell
meerkatagentfw create crew <project_name>
```

This command creates a new project folder with the following structure:

```
my_project/
├── .gitignore
├── pyproject.toml
├── README.md
├── .env
└── src/
    └── my_project/
        ├── __init__.py
        ├── main.py
        ├── crew.py
        ├── tools/
        │   ├── custom_tool.py
        │   └── __init__.py
        └── config/
            ├── agents.yaml
            └── tasks.yaml
```

You can now start developing your crew by editing the files in the `src/my_project` folder. The `main.py` file is the entry point of the project, the `crew.py` file is where you define your crew, the `agents.yaml` file is where you define your agents, and the `tasks.yaml` file is where you define your tasks.

#### To customize your project, you can:

- Modify `src/my_project/config/agents.yaml` to define your agents.
- Modify `src/my_project/config/tasks.yaml` to define your tasks.
- Modify `src/my_project/crew.py` to add your own logic, tools, and specific arguments.
- Modify `src/my_project/main.py` to add custom inputs for your agents and tasks.
- Add your environment variables into the `.env` file.

#### Example of a simple crew with a sequential process:

Instantiate your crew:

```shell
meerkatagentfw create crew latest-ai-development
```

Modify the files as needed to fit your use case:

**agents.yaml**

```yaml
# src/my_project/config/agents.yaml
researcher:
  role: >
    {topic} Senior Data Researcher
  goal: >
    Uncover cutting-edge developments in {topic}
  backstory: >
    You're a seasoned researcher with a knack for uncovering the latest
    developments in {topic}. Known for your ability to find the most relevant
    information and present it in a clear and concise manner.

reporting_analyst:
  role: >
    {topic} Reporting Analyst
  goal: >
    Create detailed reports based on {topic} data analysis and research findings
  backstory: >
    You're a meticulous analyst with a keen eye for detail. You're known for
    your ability to turn complex data into clear and concise reports, making
    it easy for others to understand and act on the information you provide.
```

**tasks.yaml**

```yaml
# src/my_project/config/tasks.yaml
research_task:
  description: >
    Conduct a thorough research about {topic}
    Make sure you find any interesting and relevant information given
    the current year is 2024.
  expected_output: >
    A list with 10 bullet points of the most relevant information about {topic}
  agent: researcher

reporting_task:
  description: >
    Review the context you got and expand each topic into a full section for a report.
    Make sure the report is detailed and contains any and all relevant information.
  expected_output: >
    A fully fledge reports with the mains topics, each with a full section of information.
    Formatted as markdown without '```'
  agent: reporting_analyst
  output_file: report.md
```

**crew.py**

```python
# src/my_project/crew.py
from meerkatagentfw import Agent, Crew, Process, Task
from meerkatagentfw.project import CrewBase, agent, crew, task
from meerkatagentfw_tools import SerperDevTool

@CrewBase
class LatestAiDevelopmentCrew():
	"""LatestAiDevelopment crew"""

	@agent
	def researcher(self) -> Agent:
		return Agent(
			config=self.agents_config['researcher'],
			verbose=True,
			tools=[SerperDevTool()]
		)

	@agent
	def reporting_analyst(self) -> Agent:
		return Agent(
			config=self.agents_config['reporting_analyst'],
			verbose=True
		)

	@task
	def research_task(self) -> Task:
		return Task(
			config=self.tasks_config['research_task'],
		)

	@task
	def reporting_task(self) -> Task:
		return Task(
			config=self.tasks_config['reporting_task'],
			output_file='report.md'
		)

	@crew
	def crew(self) -> Crew:
		"""Creates the LatestAiDevelopment crew"""
		return Crew(
			agents=self.agents, # Automatically created by the @agent decorator
			tasks=self.tasks, # Automatically created by the @task decorator
			process=Process.sequential,
			verbose=True,
		)
```

**main.py**

```python
#!/usr/bin/env python
# src/my_project/main.py
import sys
from latest_ai_development.crew import LatestAiDevelopmentCrew

def run():
    """
    Run the crew.
    """
    inputs = {
        'topic': 'AI Agents'
    }
    LatestAiDevelopmentCrew().crew().kickoff(inputs=inputs)
```

### 3. Running Your Crew

Before running your crew, make sure you have the following keys set as environment variables in your `.env` file:

- An [OpenAI API key](https://platform.openai.com/account/api-keys) (or other LLM API key): `OPENAI_API_KEY=sk-...`
- A [Serper.dev](https://serper.dev/) API key: `SERPER_API_KEY=YOUR_KEY_HERE`

Lock the dependencies and install them by using the CLI command but first, navigate to your project directory:

```shell
cd my_project
meerkatagentfw install (Optional)
```

To run your crew, execute the following command in the root of your project:

```bash
meerkatagentfw run
```

or

```bash
python src/my_project/main.py
```

If an error happens due to the usage of poetry, please run the following command to update your meerkatagentfw package:

```bash
meerkatagentfw update
```

You should see the output in the console and the `report.md` file should be created in the root of your project with the full final report.

In addition to the sequential process, you can use the hierarchical process, which automatically assigns a manager to the defined crew to properly coordinate the planning and execution of tasks through delegation and validation of results. [See more about the processes here](https://docs.meerkatagentfw.com/core-concepts/Processes/).

## Key Features

- **Role-Based Agent Design**: Customize agents with specific roles, goals, and tools.
- **Autonomous Inter-Agent Delegation**: Agents can autonomously delegate tasks and inquire amongst themselves, enhancing problem-solving efficiency.
- **Flexible Task Management**: Define tasks with customizable tools and assign them to agents dynamically.
- **Processes Driven**: Currently only supports `sequential` task execution and `hierarchical` processes, but more complex processes like consensual and autonomous are being worked on.
- **Save output as file**: Save the output of individual tasks as a file, so you can use it later.
- **Parse output as Pydantic or Json**: Parse the output of individual tasks as a Pydantic model or as a Json if you want to.
- **Works with Open Source Models**: Run your crew using Open AI or open source models refer to the [Connect MeerkatAgentFW to LLMs](https://docs.meerkatagentfw.com/how-to/LLM-Connections/) page for details on configuring your agents' connections to models, even ones running locally!

![MeerkatAgentFW Mind Map](./docs/meerkatagentfw-mindmap.png "MeerkatAgentFW Mind Map")


