# AWS Python Serverless

An easy, step-by-step guide to deploy Python code to AWS Lambda using Serverless Framework.

## A. Serverless Setup

## B. Lambda Function Setup

## C. How to Add Third-Party Packages

**_Windows_**

### 1. Setup virtual environment

Create a new virtual environment inside project folder

```none
python -m venv .\venv
```

Activate the virtual environment

```none
.\venv\Scripts\activate
```

Check whether `pip` is installed

```none
pip -V
```

Upgrade `pip` to the latest version

```none
python -m pip install --upgrade pip
```

If `pip` is accidentally removed, or not installed, it can be installed using the following command

```none
python -m ensurepip --upgrade
```

Check the list of installed Python packages

```none
pip list
```

The list probably does not contain the third-party packages that we need. Next, we will install it in the virtual environment.

### 2. Package Installation

Create `requirements.txt` file and list the needed third-party packages

**_Example_**

```none
requests
pyyaml
numpy
```

Install these packages by running the following command

```none
pip install -r requirements.txt
```

Check whether these packages are successfully installed

```none
pip list
```

**_Example output_**

If `requests` package is successfully installed, it will be listed on the output

```none
Package            Version
------------------ ---------
...                 ...
requests            2.26.0
...                 ...
```

### 3. Setup `serverless-python-requirements` Plugin

```none
npm init
```

```none
npm install --save serverless-python-requirements
```

After installation is complete, add these lines to our `serverless.yml`

```yaml
plugins:
  - serverless-python-requirements

custom:
  pythonRequirements:
    dockerizePip: non-linux
```

### 4. Deployment to AWS Lambda

If everything has been properly configured, we can deploy it to AWS

```none
serverless deploy
```

### 5. (Optional) Final Check

To make sure the third-party package has been installed and deployed, check the `.serverless` directory and it should have similar content as shown below.

```none
your-function-name
├── .serverless
│   ├── requirements
│   │    ├── ...
│   │    ├── ...
│   │    └── ...
│   ├── ...
│   ├── your-function-name.zip
│   ├── requirements.txt
│   └── serverless-state.json
├── node_modules
│   ├── ...
│   └── ...
├── venv
│   ├── ...
│   └── ...
├── ...
├── handler.py
├── ...
├── package.json
├── requirements.txt     # requirements that we created
└── serverless.yml
```
