# Github Copilot Research Notes

These are notes describing various aspects of GitHub Copilot usage and instructions
for different types of projects.

## copilot-setup=steps.yml
- defines any setup steps you want to run before every agent task.
- located in `.github/copilot-setup/steps.yml`
- This helps save some cycles so the agent doesn't have to continually 
  identify dependencies that may be more complicated or hefty to install.
- I was able to use this to install playwright ahead of my agent tasks so 
  that the agent could easily run my image snapshot tests without having to
  figure out how to install playwright each time.

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

## Custom Agents

- This is a little misleading in my option. It's not really defining a 
  persona as much as it's defining a set of instructions or a specific 
  workflow for the agent to follow.
- You can define custom agents in the `.github/agents/` folder.
- Each agent has a header that includes a name, description, and the mcp 
  tools you want this agent to use. 
- Still trying to find good use case for this. I can tell it will be I just 
  need to experiment more.
- some examples [here](https://github.com/github/awesome-copilot/tree/main/agents?utm_source=docs-web-copilot-coding-agent)

## Prompts
- You can define custom prompts in the `.github/prompts/` folder.
- These are more for local use in your IDE.

## Github.com Copilot UI
- You can select different agents from the Copilot UI on github.com
- You can view the whole agent session for any agent task triggered in 
  github.com.
- You can have a conversation with the agent about the task they are working 
  on and the agent will adjust its solution.
- You can @ mention copilot in a PR and they will respond to the comment and 
  take action in the code if necessary. 

## AGENTS.md
- Allows you to define context that can be used by all agents.
- This is scoped to the folder it is in and all subfolders.
- If you want to define context for specific file types you can create another AGENTS.md
  file in a subfolder.
- I think something is missing with this right now. I think the file masking 
  in the copilot instructions should also apply to AGENTS.md files. The way 
  AGENTS.md files work right now you are required to strucure your project 
  by file type instead of potentially structuring by feature.


## Add MCP to agents
- You can add MCP tools to your agents to help them perform specific tasks.
- Locally this is configured based on your IDE and its copilot extension. 
  Usually via some mcp.json file.
- You can defined mcp servers that you can connect to through the copilot 
  settings per your repository in github.com.
- Here's an example of config for the Chrome DevTools MCP server:
```json
{
  "chrome-devtools": {
    "type": "local",
    "command": "npx",
    "args": [
      "-y",
      "chrome-devtools-mcp@latest"
    ],
    "tools": ["*"]
  }
}
```
  - This defines a local MCP server that will be started with `npx -y chrome-devtools-mcp@latest`
  - The `tools` array defines which tools the agent can use from this MCP 
    server. This example allows all tools.

