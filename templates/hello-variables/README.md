# Variables & Conditions

The smallest useful tour of how data moves through a workflow: global, variable and input values are combined into a message, and a Condition node picks a branch. Start here if you are new to DeskStride.

## What happens

1. A Condition checks `system.platform == "windows"`.
2. The matching branch runs an echo command that prints `hello-world!`. It is built from three places:
   - `globals.greeting` (`hello`), shared by every workflow
   - `vars.name` (`world`), set on this workflow
   - `inputs.suffix` (`!`), supplied for this run
3. A second Condition checks that the command exited with code 0 and ends the run as success or failure.

## Before you run it

- Nothing to set up. It works offline on Windows, macOS and Linux and changes nothing on your machine.

## Make it yours

- Change the values under **Variables** and **Inputs** and run it again to see the output change.
- Use `{{ globals.x }}`, `{{ vars.x }}` and `{{ inputs.x }}` in any text field of any node.
- Copy the Condition pattern for your own branches. Rules are plain expressions such as `steps.<id>.exit_code == 0`.
