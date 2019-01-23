# Node.js

## Remote Debugging in VS Code

The key is to set `cwd` to the source location (e.g. TypeScript), and then use `localRoot` and `remoteRoot` to map the compiled JavaScript.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Remote debug",
      "type": "node",
      "request": "attach",
      "protocol": "inspector",
      "address": "127.0.0.1",
      "port": 9229,
      "cwd": "${workspaceFolder}/src",
      "localRoot": "${workspaceFolder}/dist",
      "remoteRoot": "/home/dist",
      "sourceMaps": true,
      "restart": true,
      "outFiles": ["${workspaceFolder}/dist/**/*.js"],
      "skipFiles": ["<node_internals>/**"]
    }
  ]
}
```
