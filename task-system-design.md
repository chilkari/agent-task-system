# Task System Design

You are an expert in using the [pi coding
harness](https://shittycodingagent.ai/) -- [github and source
code](https://github.com/badlogic/pi-mono/tree/main/packages/coding-agent) and
agentic systems in general.

I would like your help in designing a "task" system to be used from within the
"pi" coding harness that allows PI and AI agents to follow a "spec-driven"
development style for a given task.

Unless you recommend a different approach, I imagine this working as an
[agentskill](https://github.com/agentskills/agentskills) which works through
concrete high-level phases of definition, understanding, planning and
implementation for any task based on a repeatable conversation between the agent
and the user. I would like the output of this (skill definition, prompts) to be
**portable** so that I can use it in other codebases and with other agents.

The high-level phases of working through a task:

1. **Task Definition** - Prompt the user to describe the task itself. What is the
   problem? What are the expectations for when the solution has been provided?
   Is this task complex enough to require research, or is it small enough to get
   the necessary context during the planning phase?
2. **Research** - Only needed for complex large tasks, or when working in a
   large messy codebase. In this phase, the agent learns as much as it needs to
   learn in order to plan the implementation of the task.
3. **Plan** - Identify a concrete and detailed step-by-step implementation plan
   which will be used to implement the task to the user's specifications.
4. **Review/Revise with User** - In this interactive phase, work with the user
   to review, revise and reword the plan to ensure that the plan is sounds,
   covers all requirements, and accounts for any and all concerns related to the
   task. This is expected to be a loop of review/revision/repeat until the plan
   is finalized.
5. **Implement** - Once the plan is sound, this should require no more user
   attention. The agent implements the plan.
6. **Code Review** - Once the task has been implemented and the code
   appears to work. Change roles to become a thorough, experienced code reviewer
   and offer a deep code review of the generated output.
7. **Respond to Code Review Suggestions** - Take the output from the code review
   and address critical and important issues. For suggestions or lower-priority
   suggestions, interact with the user to decide which code review suggestions
   to implement. Once any changes have been made, we will loop back to step 6
   and have the code reviewer review _again_ until the user is satisfied that no
   more changes are needed.
8. **Final Polish** - To a last pass to ensure that all formatting and linting
   are clean. Search specifically for any remaining indicators of AI-generated
   code and fix last issues.

Given the 8 phases above, I would like your help in creating a set of prompts
for each phase. We will have a "tasks" directory, and each new task should have
its own subdirectory. Each phase above should write markdown files into this
directory with the output of that phase.

DO NOT IMPLEMENT YET. With the understanding above, the goal is to generate a
"task-system-design-plan.md" file with a plan proposal for how to implement the
above. I expect that for each of the 8 phases, there will be interaction between
you and the user to put more definition around the prompts and specifics of each
phase. Please interactively work with the user to define each phase above
correctly.
