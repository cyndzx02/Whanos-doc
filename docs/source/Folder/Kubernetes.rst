Kubernetes
==========

Overview
--------

The Whanos infrastructure uses Kubernetes for automatic deployment of containerized applications. This section outlines the configuration and deployment requirements for Kubernetes clusters.

Cluster Requirements
---------------------

- At least **two nodes** must be present in the cluster.
- Applications requiring deployment must include a `whanos.yml` file at the root of the repository.

`whanos.yml` Specifications
---------------------------

The `whanos.yml` file contains the deployment configuration for the application. Supported properties:

- **replicas** (optional):
  - Number of pod replicas to deploy.
  - Default: 1.

- **resources** (optional):
  - Resource specifications for Kubernetes pods.
  - Syntax must align with Kubernetes resource request and limit standards.
  - Example: `limits: cpu: "500m", memory: "512Mi"`.

- **ports** (optional):
  - List of ports to expose.
  - Ports must be accessible from the outside world, though not necessarily on the same host port.

Deployment Steps
----------------

1. Detect the `whanos.yml` file in the repository.
2. Parse and apply the configurations:
   - Set replicas and resource limits.
   - Configure port forwarding.
3. Deploy the containerized application as a pod in the cluster.

Accessing Applications
-----------------------

- Applications with exposed ports must be accessible externally.
- Port bindings on the host can differ from the container's port mappings.

Best Practices
--------------

- Use deployment templates to standardize configurations.
- Follow Kubernetes' official guidelines for scalability and resource management.
- Document any custom configurations for reproducibility.

Example
=======

    .. code-block:: yaml

        # C deployment
        ---
        apiVersion: apps/v1
        kind: Deployment
        metadata:
        name: whanos-c
        labels:
        app: whanos-c
        spec:
        replicas: 1
        selector:
        matchLabels:
            app:  whanos-c
        template:
        metadata:
            labels:
            app:  whanos-c
        spec:
            containers:
            - name: whanos-c
            image: whanos-c
            ports:
            - containerPort:  8080
            resources:
                requests:
                memory: "64Mi"
                cpu:  "250m"
                limits:
                memory: "128Mi"
                cpu:  "500m"


        # Befunge Deployment
        ---
        apiVersion: apps/v1
        kind: Deployment
        metadata:
        name: whanos-befunge
        labels:
        app: whanos-befunge
        spec:
        replicas: 1
        selector:
        matchLabels:
            app:  whanos-befunge
        template:
        metadata:
            labels:
            app:  whanos-befunge
        spec:
            containers:
            - name: whanos-befunge
            image: whanos-befunge
            ports:
            - containerPort:  8081
            resources:
                requests:
                memory: "64Mi"
                cpu:  "250m"
                limits:
                memory: "128Mi"
                cpu:  "500m"


        # Python deployment
        ---
        apiVersion: apps/v1
        kind: Deployment
        metadata:
        name: whanos-python
        labels:
        app: whanos-python
        spec:
        replicas: 1
        selector:
        matchLabels:
            app:  whanos-python
        template:
        metadata:
            labels:
            app:  whanos-python
        spec:
            containers:
            - name: whanos-python
            image: whanos-python
            ports:
            - containerPort:  8082
            resources:
                requests:
                memory: "64Mi"
                cpu:  "250m"
                limits:
                memory: "128Mi"
                cpu:  "500m"

        # Java deployment
        ---
        apiVersion: apps/v1
        kind: Deployment
        metadata:
        name: whanos-java
        labels:
        app: whanos-java
        spec:
        replicas: 1
        selector:
        matchLabels:
            app:  whanos-java
        template:
        metadata:
            labels:
            app:  whanos-java
        spec:
            containers:
            - name: whanos-java
            image: whanos-java
            ports:
            - containerPort:  8083
            resources:
                requests:
                memory: "64Mi"
                cpu:  "250m"
                limits:
                memory: "128Mi"
                cpu:  "500m"



        # Javascript deployment
        ---
        apiVersion: apps/v1
        kind: Deployment
        metadata:
        name: whanos-javascript
        labels:
        app: whanos-javascript
        spec:
        replicas: 1
        selector:
        matchLabels:
            app:  whanos-javascript
        template:
        metadata:
            labels:
            app:  whanos-javascript
        spec:
            containers:
            - name: whanos-javascript
            image: whanos-javascript
            ports:
            - containerPort:  8084
            resources:
                requests:
                memory: "64Mi"
                cpu:  "250m"
                limits:
                memory: "128Mi"
                cpu:  "500m"
