# AWS Python Serverless

An easy, step-by-step guide to deploy Python code to AWS Lambda using Serverless Framework.

## 1. Serverless Setup

1. Make sure _Node_ has been installed before installing _Serverless_. Otherwise, go to [Node official page](https://nodejs.org/en/) and follow the instructions on how to install Node.

2. Verify that both _Node_ and _npm_ have been installed on your computer.

   ```none
   node --version
   ```

   **_Example Output_**

   ```none
   v16.15.0
   ```

   ```none
   npm --version
   ```

   **_Example Output_**

   ```none
   8.5.5
   ```

3. Install _Serverless_ globally

   ```none
   npm install -g serverless
   ```

4. Verify that _Serverless_ has been successfully installed

   ```none
   serverless --version
   ```

   **_Example Output_**

   ```none
   Framework Core: 3.0.1 (standalone)
   Plugin: 6.0.0
   SDK: 4.3.0
   ```

5. Initialize Serverless Python project

   ```none
   serverless
   ```

6. Select any Python template that you are interested in. If you are new to AWS and Serverless, _"AWS - Python - Starter"_ may be a good starting point.

7. Next, name your project _(ideally no whitespace between words)_. Then, _Serverless_ will automatically create a directory with given name and include necessary boilerplate and template files.

8. Select _no_ when you are prompted to login or deploy the project.

9. Change to project directory and start developing your Serverless project.

   ```none
   cd <PROJECT_DIR>
   ```

## 2. Lambda Function Setup

## 3. How to Add Third-Party Packages

Two of the most common ways to add third-party packages to Serverless Python project.

1. pip + virtualenv + serverless-python-requirements
2. pipenv + serverless-python-requirements **_(recommended)_**

I personally prefer the second option for the following reasons.

1. _pipenv_ allows developer to split development and production dependencies (similar to _package.json_ in Node project)
2. Similar to _package.json_, _pipenv_ will store the project configuration in _Pipfile_. The file allows granular configuration and control, which is apparently not available in _requirements.txt_ file.
3. _pipenv_ locks the version of the dependencies and store the details in _Pipfile.lock_ to guarantee consistent, manageable dependencies.
4. _serverless-python-requirements_ supports _pipenv_ and will only pick up the production dependencies upon packaging and deployment. Hence, it significantly reduces the size of Lambda functions and layers.
5. The dependencies are installed in _$HOME/.cache/pip/wheel/_ directory, outside project directory. It means your project directory size remains small and is not bloated with dependencies.

However, feel free to choose any option that works best for you.

### 3.1. Option 1: pip + virtualenv + serverless-python-requirements

#### 3.1.1. Windows

1. Create a new virtual environment inside project folder, e.g. _venv_

   **_Command_**

   ```none
   python -m venv <VIRTUAL_ENVIRONMENT_NAME>
   ```

   **_Example_**

   Create a new virtual environment, _venv_ inside project folder

   ```none
   python -m venv .\venv
   ```

2. Activate the virtual environment

   **_Command_**

   ```none
   .\<VIRTUAL_ENVIRONMENT_NAME>\Scripts\activate
   ```

   Activate virtual environment named _venv_

   **_Example_**

   ```none
   .\venv\Scripts\activate
   ```

3. Check whether _pip_ is installed

   **_Command_**

   ```none
   pip -V
   ```

4. **_(Optional)_** Upgrade _pip_ to the latest version

   ```none
   python -m pip install --upgrade pip
   ```

   **_Note_**

   If _pip_ is accidentally removed, or not installed, it can be re-installed using the following command

   ```none
   python -m ensurepip --upgrade
   ```

5. Check the list of installed Python packages

   ```none
   pip list
   ```

   **_Note_**

   The list should only contain minimum installation, most likely _pip_ and _setuptools_. If it contains any third-party packages, it indicates that the virtual environment has not been activated.

6. Create _requirements.txt_ file and list the needed third-party packages

   **_Example_**

   ```none
   requests
   pyyaml
   numpy
   ```

   **_Note_**

   The file may also contain [requirement specifiers](https://pip.pypa.io/en/stable/reference/requirement-specifiers/) if you want to narrow down or select specific version to be installed in the virtual environment.

7. Install these packages by running the following command

   ```none
   pip install -r requirements.txt
   ```

   **_Note_**

   `-r` flag tells _pip_ to install recursively (in case of listing multi packages in the requirements)

8. Check whether these packages are successfully installed

   ```none
   pip list
   ```

   **_Example output_**

   If _requests_ package is successfully installed, it will be listed on the output

   ```none
   Package            Version
   ------------------ ---------
   ...                 ...
   requests            2.26.0
   ...                 ...
   ```

9. Initialize _Node_ application

   ```none
   npm init
   ```

   **_Note_**

   _package.json_ should exist in the project directory if the initialization is successful.

10. Install `serverless-python-requirements` plugin
    [learn more](https://github.com/serverless/serverless-python-requirements#readme)

    ```none
    npm install --save serverless-python-requirements
    ```

11. Add these lines to our `serverless.yml` after installation is complete

    ```yaml
    plugins:
      - serverless-python-requirements

    custom:
      pythonRequirements:
        dockerizePip: true
    ```

### 3.2. Option 2: pipenv + serverless-python-requirements **_(recommended)_**

1. Install _pipenv_

   ```none
   pip install pipenv
   ```

2. Check for _pipenv_ version to ensure the package is successfully installed.

   ```none
   python -m pipenv --version
   ```

   **_Example Output_**

   ```none
   pipenv, version 2022.5.2
   ```

3. **_(Optional)_** Install _pipenv_ inside virtual environment to enable _pipenv_ command

   ```none
   python -m pipenv install pipenv --dev
   ```

   **_Note_**

   As this is the first third-party package installation, _pipenv_ will automatically generate _Pipfile_ and _Pipfile.lock_. The `--dev` option tells _pipenv_ to install the local _pipenv_ as _development_ dependency. It will be excluded during deployment.

4. Activate virtual environment

   ```none
   python -m pipenv shell
   ```

5. Check the packages installed inside the virtual environment

   ```none
   pip list
   ```

   Alternatively, _pipenv_ also comes with a feature to check dependency graph.

   ```none
   pipenv graph
   ```

   If the virtual environment is configured properly, it should only contain _pip_, _setuptools_, _pipenv_ and their dependencies.

6. Install all necessary dependencies.

   **_Production dependency_**

   ```none
   pipenv install <PACKAGE_NAME>
   ```

   **_Development dependency_**

   ```none
   pipenv install <PACKAGE_NAME> --dev
   ```

7. Initialize _Node_ application

   ```none
   npm init
   ```

   **_Note_**

   _package.json_ should exist in the project directory if the initialization is successful.

8. Install `serverless-python-requirements` plugin
   [learn more](https://github.com/serverless/serverless-python-requirements#readme)

   ```none
   npm install --save serverless-python-requirements
   ```

9. Add the following config to _serverless.yml_ after installation is complete

   ```yaml
   plugins:
     - serverless-python-requirements

   custom:
     pythonRequirements:
       dockerizePip: true
   ```

### 4. (Optional) Lambda Layer Configuration

If the Lambda functions of Serverless application use the same dependencies, you can take full advantage of Lambda layer to further reduce the function size by packing it as centralized dependencies.

Before

```none
┌─────────────────────┐         ┌─────────────────────┐
│ Function 1          │         │ Function n          │
├─────────────────────┤         ├─────────────────────┤
│ Main function  5kB  │         │ Main function 10kB  │
├─────────────────────┤   ...   ├─────────────────────┤
│ Dependencies   2MB  │         │ Dependencies   2MB  │
└─────────────────────┘         └─────────────────────┘
 Total size 2.005MB               Total size 2.010MB
```

After

```none
┌─────────────────────┐         ┌─────────────────────┐
│ Function 1          │         │ Function n          │
├─────────────────────┤         ├─────────────────────┤
│ Main function  5kB  │   ...   │ Main function 10kB  │
└─────────────────────┘         └─────────────────────┘
 Total size 5kB                   Total size 10kB
         │                                 │
         └─────────────────┬───────────────┘
                           │
                ┌─────────────────────┐
                | Lambda Layer        |
                ├─────────────────────┤
                │ Dependencies   2MB  │
                └─────────────────────┘

```

Layer configuration in _serverless.yml_

```yaml
plugins:
  - serverless-python-requirements

custom:
  pythonRequirements:
    dockerizePip: true
    layer:
      name: ${self:provider.stage}-pythonLambdaLayer
      description: Python requirements lambda layer
      #...

# explicitly filter and select necessary files and directories
package:
  individually: true
  excludeDevDependencies: true
  patterns:
    - "!./**" # exclude all files and directories
    - "./src/**" # select only 'src' directory and its files and subdirectories

# link lambda function to dependency layer
functions:
  myhandler:
    handler: src/path/to/my.handler
    #..
    layers:
      - Ref: PythonRequirementsLambdaLayer
```

### 5. Deployment to AWS

**_Command_**

```none
serverless deploy [--aws-profile <AWS_PROFILE_NAME>] [--stage <DEPLOYMENT_STAGE>]
```

**_Note_**

Both _aws-profile_ and _stage_ are optional, however, you are expected to have at least 1 profile (default profile) in your machine. Otherwise, _Serverless_ will never know where to deploy nor gain access to your AWS account programmatically.

Below is how _Serverless_ looks for the AWS profile, sorted by priority (high to low):

1. Command line arguments (`--aws-profile <AWS_PROFILE_NAME>`)
2. Deployment script (typically _serverless.yml_)
3. Default AWS profile

   - Linux & MacOS: `$HOME/.aws/{credentials, config}`
   - Windows: `C:\Users\<USER_NAME>\.aws\{credentials, config}`

### 6. (Optional) Final Check

To make sure the third-party package has been installed and deployed, check the `.serverless` directory and it should have similar content as shown below.

```none
your-function-name
├── .serverless
│   ├── requirements
│   │    ├── ...
│   │    └── ...
│   ├── cloudformation-template-create-stack.json
│   ├── cloudformation-template-update-stack.json
│   ├── pythonRequirements.zip
│   ├── function_1.zip
│   ├── ...
│   ├── function_n.zip
│   ├── requirements.txt
│   └── serverless-state.json
├── node_modules
│   ├── ...
│   └── ...
├── venv                 # virtual environment config + local dependencies
│   ├── ...              # (not exist if configuration uses pipenv)
│   └── ...
├── ...
├── src
│   ├── function_1
│   │    ├── ...
│   │    └── handler.py
│   ├── ...
│   └── function_n
│        ├── ...
│        └── handler.py
├── handler.py
├── ...
├── package.json
├── Pipfile              # (not exist if configuration uses pip + venv)
├── Pipfile.lock         # (not exist if configuration uses pip + venv)
├── requirements.txt     # (not exist if configuration uses pipenv)
└── serverless.yml
```
