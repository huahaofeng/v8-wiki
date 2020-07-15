[[_TOC_]]

## Cross compiling RISC-V header file location

```json
[ 
  "/opt/riscv/sysroot/usr/include",
  "/opt/riscv/riscv64-unknown-linux-gnu/include/c++/9.2.0"
]
```

## VSCode Setup

VS Code will be the standard IDE for developers on this project. Install VS Code to your Windows machine via the download link at https://code.visualstudio.com/. It is recommended to connect VS Code to a directory setup on a remote linux machine via SSH. To do this, install the "Remote - SSH" extension for VS Code and configure it to connect to your server (see [here](https://code.visualstudio.com/docs/remote/ssh) for detailed instructions).

VS Code uses configuration files in the `.vscode` directory at the root of your project. These files are not checked into the repository, but setting that we have found useful are described below. Add these files to your project's `.vscode/` directory as desired.

### Code Formatting

In order to enforce consistency and avoid issues with formatting in merges and reviews, we will all format our code using `clang-format`. Install the `Clang-Format` extension in VS Code, then use the "Format Document" command (`Alt-Shift-F` by default) to format all files before commiting. We will be using the `LLVM` format settings.

You may need to add the following to your settings to ensure that the `LLVM` format settings are used:

```json
    "C_Cpp.clang_format_style": "LLVM",
```
You can add the setting by search for "C_Cpp.clang_format_style" in settings and put in "LLVM".

### .vscode/c_cpp_properties.json

This file defines setting specific to the C/C++ configuration. To get the best results from Intellisense, the settings here have been found to work well.

- intelliSenseMode: If I leave it as _${default}_ or set to _gcc-x64_ it will define `__x86_64__` and mess up a lot of following macro definition. Setting to _gcc-x86_ seems ok.

```json
{
  "configurations": [
    {
      "name": "RISCV64GC",
      "defines": ["__riscv", "__riscv_xlen=64", "USE_SIGNALS"],
      "intelliSenseMode": "gcc-x86",
      "compilerPath": "/opt/riscv/bin/riscv64-unknown-linux-gnu-g++",
      "cStandard": "c11",
      "cppStandard": "c++17",
      "includePath": ["${default}", "${workspaceFolder}"]
    }
  ],
  "version": 4
}
```

### .vscode/launch.json

This file allows us to define launch configurations, to trigger a debug session. I have defined two configurations, one for debugging the `mksnapshot` and one for debugging unit tests. For unit test debugging, change the argument as needed, depending on which test you would like to debug. These can be launched via the Debug menu or the "Run and Debug" side bar.

```json
{
  "version": "0.2.0",
  "configurations": [
      {
          "name": "(gdb) mksnapshot",
          "type": "cppdbg",
          "request": "launch",
          "program": "${workspaceFolder}/out.gn/riscv64_debug/mksnapshot",
          "args": ["--turbo_instruction_scheduling", "--target_os=linux", "--target_arch=x64", "--embedded_src", "gen/embedded.S", "--target_is_simulator", "--embedded_variant", "Default", "--random-seed", "314159265", "--startup_blob", "snapshot_blob.bin", "--native-code-counters", "--verify-heap"],
          "stopAtEntry": false,
          "cwd": "${workspaceFolder}/out.gn/riscv64_debug",
          "environment": [],
          "externalConsole": false,
          "MIMode": "gdb",
          "setupCommands": [
              {
                  "description": "Enable pretty-printing for gdb",
                  "text": "-enable-pretty-printing",
                  "ignoreFailures": true
              }
          ]
      },
      {
          "name": "(gdb) cctest",
          "type": "cppdbg",
          "request": "launch",
          "program": "${workspaceFolder}/out.gn/riscv64_debug/cctest",
          "args": [
              "test-assembler-riscv/RISCV5"
          ],
          "stopAtEntry": false,
          "cwd": "${workspaceFolder}/out.gn/riscv64_debug",
          "environment": [],
          "externalConsole": false,
          "MIMode": "gdb",
          "setupCommands": [
              {
                  "description": "Enable pretty-printing for gdb",
                  "text": "-enable-pretty-printing",
                  "ignoreFailures": true
              }
          ]
      }
  ]
}
```

### .vscode/tasks.json

This file configures the build task and test task which can be triggered with keyboard shortcuts to build and test the project. The configuration shown here builds everything with the `ninja -j16` command. Errors from the build show in the terminal and can be Ctrl-clicked to jump to the location in the source code. The test task runs the `test-simple-riscv` and `test-assembler-riscv` cctests.

```json
{
  // See https://go.microsoft.com/fwlink/?LinkId=733558
  // for the documentation about the tasks.json format
  "version": "2.0.0",
  "tasks": [
    {
      "label": "build all",
      "type": "shell",
      "command": "ninja -j16",
      "group": {
        "kind": "build",
        "isDefault": true
      },
      "problemMatcher": {
        "base": "$gcc",
        "fileLocation": [
          "relative",
          "${workspaceRoot}/out.gn/riscv64_debug"
        ]
      },
      "options": {
        "cwd": "${workspaceRoot}/out.gn/riscv64_debug"
      }
    },
    {
      "label": "cctests",
      "type": "shell",
      "command": "tools/run-tests.py --outdir=out.gn/riscv64_debug cctest/test-simple-riscv/* cctest/test-assembler-riscv/*",
      "group": {
        "kind": "test",
        "isDefault": true
      },
      "problemMatcher": {
        "base": "$gcc",
        "fileLocation": [
          "relative",
          "${workspaceRoot}/out.gn/riscv64_debug"
        ]
      },
    }
  ]
}
```
