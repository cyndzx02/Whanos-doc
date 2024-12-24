Jenkins
=======

This documentation provides a complete guide to configuring and testing a Jenkins infrastructure capable of managing Docker builds and Kubernetes deployments.

Prerequisites
=============
- *Operating System*: Ubuntu 20.04+ or equivalent
- *Required Tools*:
  - Docker
  - Jenkins
  - Ansible
  - Kubernetes (Minikube or remote cluster)
  - Git

Tool Installation
+++++++++++++++++

Automate the installation of required tools using an Ansible playbook.

File `install_prereqs.yml`:

.. code-block:: yaml

    ---
    - name: Install Jenkins, Docker, Minikube, and Git
      hosts: all
      become: true
      tasks:
        - name: Install required dependencies
          apt:
            name: ["openjdk-11-jdk", "docker.io", "git", "ansible", "curl", "apt-transport-https"]
            state: present

        - name: Start Docker Service
          service:
            name: docker
            state: started
            enabled: yes

        - name: Install Jenkins
          shell: |
            wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | apt-key add -
            echo "deb http://pkg.jenkins.io/debian-stable binary/" > /etc/apt/sources.list.d/jenkins.list
            apt-get update && apt-get install -y jenkins

        - name: Start Jenkins Service
          service:
            name: jenkins
            state: started
            enabled: yes

        - name: Install Minikube
          shell: |
            curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
            chmod +x minikube && mv minikube /usr/local/bin/

        - name: Install kubectl
          shell: |
            curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
            chmod +x kubectl && mv kubectl /usr/local/bin/

Execute the playbook:

.. code-block:: bash

    ansible-playbook -i "localhost," -c local install_prereqs.yml

Jenkins Configuration
+++++++++++++++++++++

Initialize Jenkins and configure it using Jenkins Configuration as Code (JCasC).

File `jenkins-casc.yaml`:

.. code-block:: yaml

    jenkins:
      securityRealm:
        local:
          allowsSignup: false
          users:
            - id: "admin"
              password: "admin123"

      authorizationStrategy:
        loggedInUsersCanDoAnything:
          allowAnonymousRead: false

      jobs:
        - script: >
            folder('Whanos base images') {
              description('Folder for Whanos base images')
            }
            folder('Projects') {
              description('Folder for project jobs')
            }

        - script: >
            pipelineJob('link-project') {
              definition {
                cps {
                  script("""
                    pipeline {
                      agent any
                      parameters {
                        string(name: 'GIT_REPO', description: 'Git repository URL')
                      }
                      stages {
                        stage('Setup Project') {
                          steps {
                            echo "Setting up project: ${params.GIT_REPO}"
                          }
                        }
                      }
                    }
                  """.stripIndent())
                }
              }
            }

Docker Image Structure
+++++++++++++++++++++++

Create the necessary Dockerfiles for building images.

Example `Dockerfile.base` for Python:

    .. code-block:: dockerfile

        FROM python:3.12
        WORKDIR /app
        COPY requirements.txt .
        RUN pip install -r requirements.txt
        ENTRYPOINT ["python", "-m", "app"]

Directory structure:

.. code-block:: text

        images/
        ├── c/
        │   ├── Dockerfile.base
        │   ├── Dockerfile.standalone
        ├── java/
        │   ├── Dockerfile.base
        │   ├── Dockerfile.standalone
        ├── python/
        │   ├── Dockerfile.base
        │   ├── Dockerfile.standalone

Jenkins Pipeline for Projects
++++++++++++++++++++++++++++++

Create a `Jenkinsfile` for each project.

Example:

.. code-block:: groovy

    pipeline {
        agent any
        stages {
            stage('Build Docker Image') {
                steps {
                    sh 'docker build -t whanos-python -f images/python/Dockerfile.base .'
                }
            }
            stage('Push to Docker Registry') {
                steps {
                    sh 'docker tag whanos-python my-docker-registry/whanos-python'
                    sh 'docker push my-docker-registry/whanos-python'
                }
            }
            stage('Deploy to Kubernetes') {
                when {
                    fileExists 'whanos.yml'
                }
                steps {
                    sh 'kubectl apply -f whanos.yml'
                }
            }
        }
    }

Kubernetes Deployment
=====================
Create a `whanos.yml` file:

.. code-block:: yaml

    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: my-python-app
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: my-python-app
      template:
        metadata:
          labels:
            app: my-python-app
        spec:
          containers:
          - name: python-app
            image: my-docker-registry/whanos-python
            ports:
            - containerPort: 80

Testing and Validation
======================
1. *Access Jenkins* via http://<server-ip>:8080 and configure a job with link-project.
2. *Build Docker Images*:

   .. code-block:: bash

       docker build -t whanos-python -f images/python/Dockerfile.base .

3. *Push to Docker Registry*:

   .. code-block:: bash

       docker push my-docker-registry/whanos-python

4. *Kubernetes Deployment*:

   .. code-block:: bash

       kubectl apply -f whanos.yml

5. *Check Pods and Services*:

   .. code-block:: bash

       kubectl get pods
       kubectl get services

6. *Access the Application*:

   .. code-block:: bash

       minikube service <service-name> --url

Conclusion
==========
This guide covers all steps to configure, deploy, and test a Jenkins infrastructure in compliance with the Whanos project. Each team member can reproduce this setup by following the instructions provided here