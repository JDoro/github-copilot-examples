# Github Copilot Research Notes
These are notes describing various aspects of GitHub Copilot usage and instructions
for different types of projects.

## Instructions

- This seems to be core to using the copilot agents.
- It's a bit of a moving target. I feel like there's a new feature released 
  almost every day. Which is very cool but also hard to observe if my 
  changes are helping or they just released some update that improved things.
- The main file is `.github/copilot-instructions.md`
- You can also have more specific instructions for certain file types.
  For example, I have `.github/instructions/react.instructions.md`
- The more specific instructions extend the main instructions file. There 
  are examples in the `.github/instructions/` folder.
- The instructions can cover coding standards, workflows, folder structure,
  naming conventions, tools used, etc.
- These seem to be the best way to try to sway the agent to start using new 
  or better patterns even if there are a lot of examples of the old patterns
  in the codebase.