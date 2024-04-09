# Visual Studio Code in an Apptainer Container

Apptainer build of Visual Studio Code

## Steps to build

Need to automate the following!

* build draft container from definition file: `apptainer build --sandbox vscode_test vscode.def`
* test application as regular user:
`apptainer exec --bind ~/vscode_data:/opt/VSCode-linux-x64/data --writeable-tmpfs vscode_test code`
* In my case, location where I want to run the container has no public Internet access, so I need to open code and run it to sync all plugins - takes ~20 minutes.
* If need to fix something as root - changes must be made to definition file to be permanent
`apptainer shell --writable --fakeroot vscode_test`
* when done testing convert sandbox directory into container file: `apptainer build vscode.sif vscode_test`
* Move file(s) to Google Cloud Storage
  * `cd .. & tar -czf ./vscode_app.tgz ./vscode_data ./vscode_apptainer`
  * `gsutil cp ./vscode_app.tgz gs://gcproject/Downloads/`
  * `rm ./vscode_app.tgz`
* Then on destination system:
  * `gsutil cp gs://gcproject/Downloads/vscode_app.tgz ./`
  * `tar -xzf ./vscode_app.tgz`
  * `rm ./vscode_app.tgz`
* Run Visual Studio Code with: `apptainer exec --bind ~/vscode_data:/opt/VSCode-linux-x64/data ~/vscode_apptainer/vscode.sif code`

Can also setup alias as something like this:

```bash
alias code='apptainer exec --bind ~/vscode_data:/opt/VSCode-linux-x64/data --writable-tmpfs ~/vscode_apptainer/vscode.sif code'
```

Note: `~/vscode_data` should be the location of your extensions and other userdata for VS Code."
