In this task, you'll turn a real repo analysis task into a production-ready MCP tool. 

You'll implement `analyze_hotspots` as a thin handler and make it fail cleanly with structured errors.

## Workflow at a glance

👩‍💻
1. Set up your environment
2. Implement `analyze_hotspots` as a thin MCP handler
3. Add structured error handling
4. Run tests and fix failures
5. Check your work
6. Submit

## 1. Set up your environment

Once you log in to your GitHub account, the repository for this task will be added automatically.

1. Confirm that `mcp-hotspots-handler` appears in your account. 
2. Clone the repo locally and open it in your editor (Cursor, VS Code, etc.)

## 2. Implement `analyze_hotspots` as a thin handler

Implement the MCP tool handler `analyze_hotspots`.

**Requirements:**

- Implement it in `tools.py`
- Register it with `@server.tool()`
- It must be `async`
- Parameters:
    - `repo_path: str`
    - `days: int = 30`
    - `limit: int = 10`

**Inside the handler:**

1. Instantiate the adapter: `repo = GitRepository(repo_path)`
2. Delegate analysis: `results = analyzer.analyze_hotspots(repo, days=days, limit=limit)`
3. Return structured JSON as MCP `TextContent`.

## 3. Add structured error handling

Your handler must fail cleanly with MCP structured errors:

### Input validation errors → `InvalidParams`

Validate business rules (not types):

- If `repo_path` doesn't exist → `InvalidParams`
- If `repo_path` exists but isn't a Git repo (no `.git`) → `InvalidParams`

### Runtime errors → `InternalError`

Wrap the analysis call:

- If analysis raises a Git operation error → `InternalError`
- Any other exception → `InternalError` with a message like "Analysis failed"

## 4. Make the tool discoverable

Make the tool discoverable and callable via [MCP Inspector](https://modelcontextprotocol.io/docs/tools/inspector).

- **🤔 What's MCP Inspector?**
    
    **MCP Inspector** is an interactive tool for testing and debugging MCP servers. It lets you connect to your server, see which tools are exposed, inspect their schemas, and try calling them manually.
    
    You can use it to check whether:
    
    - `analyze_hotspots` is discoverable as a tool
    - Its parameters show up correctly
    - The server returns the expected result or a structured error
    
    It's not required for passing the task, but it's a useful way to confirm that your tool works from an MCP client's point of view.
    
    **Requirements**
    
    <aside>
    ⌨️
    
    Node.js: ^22.7.5
    
    </aside>
    
    **Quick Start (UI mode)**
    
    To get up and running right away with the UI, just execute the following:
    
    <aside>
    ⌨️
    
    `npx @modelcontextprotocol/inspector`
    
    </aside>
    

## **🚀 Speed up with a prompt (optional)**

You can use AI to generate a first implementation of the handler. Do this only **after** reading the task requirements and understanding what the handler is supposed to do.

You still need to review the code, run the tests locally, and make sure the final implementation satisfies the full specification and submission checklist.

#### Prompt

```markdown
You are working in a repository called `mcp-hotspots-handler`.

Implement the MCP tool handler `analyze_hotspots` in `tools.py` and make it satisfy these exact requirements.

Tool requirements:
- Register it with `@server.tool()`
- The function must be `async`
- It must accept:
  - `repo_path: str`
  - `days: int = 30`
  - `limit: int = 10`

Implementation requirements:
- `tools.py` must import the server with exactly:
  - `from app import server`
- Keep the handler thin
- Do not implement hotspot analysis logic inside `tools.py`
- Use `GitRepository` from `git_utils.py`
- Inside the handler:
  1. instantiate the adapter with `repo = GitRepository(repo_path)`
  2. call `analysis.analyze_hotspots(repo, days=days, limit=limit)`
  3. return the result as JSON using MCP `TextContent`
- Use `json.dumps(...)`

Error handling requirements:
- Import:
  - `McpError` from `mcp.shared.exceptions`
  - `INVALID_PARAMS`
  - `INTERNAL_ERROR`
- If `repo_path` does not exist, raise `McpError(code=INVALID_PARAMS, ...)`
- If `repo_path` exists but is not a git repo, raise `McpError(code=INVALID_PARAMS, ...)`
- Check that the repo is a git repo by verifying that the `.git` directory exists
- Wrap the analysis call in `try/except`
- Catch `GitCommandError` explicitly
- If a git operation fails, raise `McpError(code=INTERNAL_ERROR, ...)`
- If any other exception occurs, raise `McpError(code=INTERNAL_ERROR, ...)` with a message containing:
  - `"Analysis failed"`

Analysis requirements:
- `analysis.analyze_hotspots(...)` must return a non-empty list
- Each item must contain:
  - `file`
  - `authors`
  - `changes`
  - `risk_score`
- Results must be sorted by `risk_score` in descending order
- The `limit` parameter must be respected

Make `pytest` pass.

Build the simplest implementation that satisfies these requirements.
```

## 5. Check your work

Before submitting, test your functionality locally and review the submission checklist. 

### ✅ Submission checklist

**Handler behavior**

- [ ]  `analyze_hotspots` exists in `tools.py`  , is `async`, and is registered with `@server.tool()`
- [ ]  Accepts `repo_path`, `days=30`, and `limit=10`
- [ ]  Is thin: only orchestrates and doesn't contain hotspot-analysis logic
- [ ]  Creates a repository adapter with `GitRepository(repo_path)`
- [ ]  Delegates to `analysis.analyze_hotspots(...)`
- [ ]  Returns MCP `TextContent`
- [ ]  Returns a valid JSON string
- [ ]  Uses `json.dumps(...)`
- [ ]  Checks for a `.git` directory
- [ ]  Catches `GitCommandError` explicitly

**Error handling**

- [ ]  If `repo_path` doesn't exist or isn't a Git repo, the handler raises an `McpError` with code `INVALID_PARAMS`
- [ ]  If a Git operation or any other runtime error happens during analysis, the handler raises `McpError` with code `INTERNAL_ERROR`
- [ ]  Generic internal errors include the message `"Analysis failed"`

## **6. Submit your task**

1. Commit your changes.
2. Push to GitHub.
3. Return to the lesson and click "Submit."
