# Jenkins Workflow Demo

This repository demonstrates a CI/CD workflow using Jenkins for a simple Flask application. The pipeline automatically tests, merges pull requests, and deploys code to the main branch.

## Features

- **Automated Testing:** Runs unit tests on every pull request.
- **Auto Merge:** Automatically merges PRs to `main` if tests pass.
- **Deployment:** Deploys the Flask app when changes are pushed to `main`.

## Project Structure

```
jenkins_workflow_demo/
├── app.py
├── requirements.txt
├── tests/
│   └── test_app.py
└── Jenkinsfile
```

## Getting Started

### Prerequisites

- Python 3.9+
- Jenkins server (with Pipeline and GitHub plugins)
- GitHub personal access token (with `repo` and `workflow` permissions)

### Setup

1. **Clone the repository:**
   ```sh
   git clone https://github.com/Vasanth1602/jenkins_workflow_demo.git
   cd jenkins_workflow_demo
   ```

2. **Install dependencies:**
   ```sh
   python -m venv venv
   venv\Scripts\pip install -r requirements.txt
   ```

3. **Run the app locally:**
   ```sh
   venv\Scripts\python app.py
   ```
   Visit [http://localhost:5000](http://localhost:5000) to see the app.

### Jenkins Configuration

1. **Add GitHub Token to Jenkins:**
   - Go to **Manage Jenkins > Manage Credentials**.
   - Add a new **Secret Text** credential with your GitHub token.
   - Set the ID to `github-creds`.

2. **Create a Jenkins Pipeline:**
   - Point Jenkins to this repository.
   - Jenkins will automatically use the `Jenkinsfile` for builds.

## Workflow

- **Feature Branch:**  
  Push changes to a feature branch and create a PR. Jenkins will run tests and auto-merge if successful.
- **Main Branch:**  
  After merging, Jenkins will deploy the app from the `main` branch.

## Example Flask App

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello, Jenkins!'

if __name__ == '__main__':
    app.run(debug=True)
```

## Example Test

```python
import unittest
from app import app

class AppTestCase(unittest.TestCase):
    def setUp(self):
        self.client = app.test_client()

    def test_hello(self):
        response = self.client.get('/')
        self.assertEqual(response.data.decode(), 'Hello, Jenkins!')

if __name__ == '__main__':
    unittest.main()
```

## Jenkinsfile Highlights

- **Setup:** Creates Python virtual environment and installs dependencies.
- **Test:** Runs unit tests.
- **Merge PR:** Uses GitHub API to auto-merge PRs if tests pass.
- **Deploy:** Runs the Flask app when building `main`.

## License

This project is licensed under the MIT