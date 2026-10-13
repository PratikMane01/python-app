# Python Flask Application Deployment on AWS EC2 Using Gunicorn and Jenkins Webhook

## 1. Introduction

This project demonstrates the deployment of a small Python web
application on an **Amazon EC2 instance running Amazon Linux 2023**. The
application uses **Flask** and is served with **Gunicorn**, a
production-oriented WSGI application server for Python.

The application code is stored in GitHub and cloned onto the EC2
instance. A Jenkins webhook was also added as part of the deployment
workflow.

### Project objectives

-   Launch and access an Amazon EC2 Linux instance.
-   Install Python 3, pip, and Git.
-   Clone the application source code from GitHub.
-   Install Python dependencies from `requirements.txt`.
-   Run the Flask application through Gunicorn on port `5000`.
-   Configure a Jenkins webhook as part of the automation workflow.

## 2. Architecture Diagram

``` mermaid
flowchart TD
    A[Developer updates application code] --> B[GitHub repository]
    B --> C[Jenkins webhook trigger]
    C --> D[Jenkins deployment workflow]
    D --> E[Amazon EC2 instance<br/>Amazon Linux 2023]
    E --> F[Python application<br/>Flask]
    F --> G[Gunicorn WSGI server<br/>Port 5000]
    G --> H[Browser / HTTP client]
```

**Traffic flow:** A browser sends an HTTP request to the EC2 public IPv4
address on port `5000`. Gunicorn serves the Flask application, which
returns the page response.

> Note: The diagram represents the intended workflow with the Jenkins
> webhook. Keep the Jenkins job/pipeline configuration and webhook
> delivery settings documented separately if you want to demonstrate the
> full automated flow.

## 3. Input

The main inputs and prerequisites for this project are:

-   **Application source:** GitHub repository ---
    `https://github.com/PratikMane01/python-app`
-   **Cloud platform:** AWS EC2
-   **Operating system:** Amazon Linux 2023
-   **Application framework:** Flask
-   **WSGI server:** Gunicorn
-   **Dependency file:** `requirements.txt`
-   **Network configuration:** EC2 security group allowing inbound TCP
    traffic on port `5000` for testing
-   **Automation component:** Jenkins webhook configured for the
    repository/deployment workflow

The application response shown in the browser is:

``` text
successfully deployed python application through jenkins!!!!!!!!, added webhook
```

## 4. Background and Setup

### Step 1: Update packages and install prerequisites

Connect to the EC2 instance using SSH or EC2 Instance Connect, then run:

``` bash
sudo dnf update -y
sudo dnf install python3 python3-pip git -y
python3 --version
pip3 --version
git --version
```

### Step 2: Clone the GitHub repository

``` bash
git clone https://github.com/PratikMane01/python-app.git
cd python-app
ls
```

Expected project files include `app.py` and `requirements.txt`.

### Step 3: Create and activate a virtual environment

Run these commands as the normal EC2 login user; do not use `sudo` to
activate the environment.

``` bash
python3 -m venv pythonenv
source pythonenv/bin/activate
```

If virtual-environment creation fails because the required support
package is missing, install the appropriate Python venv package for the
operating system and retry.

### Step 4: Install Python dependencies

``` bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

Use package versions that are available on PyPI. If installation reports
that a pinned version cannot be found (for example, a `click` version in
`requirements.txt`), correct the version in `requirements.txt` and run
the install command again.

### Step 5: Start the application with Gunicorn

From the project directory, with the virtual environment activated:

``` bash
gunicorn --bind 0.0.0.0:5000 app:app
```

This command assumes that `app.py` defines a Flask application object
named `app`. For a temporary background process during testing, you can
use:

``` bash
gunicorn --bind 0.0.0.0:5000 app:app --daemon
```

To stop a daemonized test process, identify its process before
terminating it. For a persistent deployment, configure Gunicorn as a
`systemd` service rather than relying on a shell session or `--daemon`.

### Step 6: Configure EC2 networking

In the EC2 security group, allow inbound **TCP port 5000** from your own
public IP address for testing. Then open:

``` text
http://<EC2-PUBLIC-IP>:5000
```

Replace `<EC2-PUBLIC-IP>` with the instance's current public IPv4
address. Avoid publishing a live public IP address in a public README.
Do not leave port `5000` open to the entire internet longer than
necessary.

### Step 7: Jenkins webhook

A repository webhook can notify Jenkins when code changes, allowing a
configured job or pipeline to start. For a complete CI/CD demonstration,
verify that:

1.  The webhook delivery succeeds in GitHub.
2.  Jenkins receives the event and starts the intended job.
3.  The job checks out the latest code and deploys it to EC2.
4.  The application remains healthy after deployment.

The screenshots show the application message referencing Jenkins and
that a webhook was added. Document the Jenkins job/pipeline steps
separately if you want to evidence each automated stage.

## 5. Output

The application was accessible in a browser at the EC2 public IPv4
address on port `5000`. The page displayed:

> successfully deployed python application through jenkins!!!!!!!!,
> added webhook

The terminal output also showed Gunicorn being started with the
application module `app:app` and bound to `0.0.0.0:5000`.

## 6. Key Technologies and Skills Practiced

-   AWS EC2 and Amazon Linux 2023
-   Linux command-line administration
-   Python 3 and pip
-   Python virtual environments
-   Flask application basics
-   Gunicorn / WSGI application serving
-   Git and GitHub repository cloning
-   EC2 security groups and inbound ports
-   Jenkins webhook concepts and deployment workflow troubleshooting

## 7. Conclusion

This project provided practical experience in deploying a Python web
application on AWS EC2, installing dependencies, managing source code
with Git, and serving the Flask application through Gunicorn. It also
introduced the role of Jenkins webhooks in a deployment workflow.

**Possible next improvements:** run Gunicorn under `systemd`, put Nginx
in front of Gunicorn, use HTTPS, keep secrets out of source control,
restrict inbound access, and verify the complete Jenkins pipeline from
code push through successful deployment.
