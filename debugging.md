# Debugging

First be sure you can run rotki locally using the install directions

## Setting up the python backend for debugging

- Open the root rotki folder in a vscode instance
- Install the `Python` vscode extension
- Create a new launch configuration (`Run -> Add configuration...`) of type `Python`
- Update the `launch.json`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Python Backend",
      "type": "python",
      "request": "launch",
      "module": "rotkehlchen",
      "env": {
        "GEVENT_SUPPORT": "True"
      },
      "args": [
        "--api-port",
        "4242",
        "--api-cors",
        "http://localhost:8080",
        // "--data-dir",
        // "${workspaceFolder}\\data-dir"
      ]
    }
  ]
}
```

- Press `Ctrl+Shift+P` and select `Python:Select Interpreter`
- Select your phython virtual environment for the rotki app
- Verify in the status bar at the bottom of vscode that the virtual env and correct debug configuration is selected
- Set a breakpoint in the unlock_user function in `rotkehlchen.py` (Press Ctrl+P to search for files)
- Click on the `Python Backend` text on the bottom status bar to start debugging
- Vscode should open a new "Python Debug Console" terminal window and you should see the message `Rotki API server is running at: 127.0.0.1:4242`


## Debugging the backend using the frontend in a debugger process

- Open a new instance of vscode and open the `electron-app` directory
- Follow the instructions [here](https://nklayman.github.io/vue-cli-plugin-electron-builder/guide/recipes.html#debugging-with-vscode)
- Add the env section to the "Electron:Main" configuration (updating the `VIRTUAL_ENV` directory as necessary):
```json
      "env": {
        //"IS_TEST": "true",
        "VIRTUAL_ENV": "C:\\Users\\Charlie\\Envs\\rotki",
        "SKIP_PYTHON_BACKEND": "True"
      },
```
>NOTE: The `Electron: Renderer` task doesn't seem to work so you can delete that section and also delete the `compounds` section
- Update your vue.config to point to the `dist_electron` directory -OR- update your launch.json args to point to the `./dist` directory
- Set a breakpoint at the first line of the `createPyProc` function `py_handler.ts`
>TIP: Be sure to shutdown any Chrome instances that you have running that were started in debug mode
- Click the `Electron: Main` in the bottom status bar to start debugging or press `F5`
- Vscode will start an `electron-debug` task terminal and you should see the app building
>TIP: If you update any of the electron-app `.ts` files that run outside of the packaged vue app (e.g. `py-handler.ts`) you should press `Ctrl+P` then `spacebar` in the `electron-debug` task terminal so that the electron app is rebuilt
- Your breakpoint should be hit in `py-handler.ts` and if you step through you should see it skipping starting the python child process
- Press `F5` to continue running the electron app then you should see the Rotki app start up and present the login with Developer Tools window started as well
- Log in to Rotki and you should see the breakpoint hit in the python backend vscode debugger process
