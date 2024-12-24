Images
======

Overview
--------

Whanos requires Docker images to containerize and deploy applications. These images must adhere to the following principles:

- **Base Images**: Used to configure runtime environments and referenced in custom Dockerfiles.
- **Standalone Images**: Self-contained images that require no further configuration.

Specifications
--------------

Base Image Requirements
~~~~~~~~~~~~~~~~~~~~~~~~

- Must be based on an official Docker image.
- Use the Bourne-Again shell (Bash) for build instructions.
- Operate in an `app` directory at the root.
- Install dependencies, if applicable.
- Remove unnecessary files, leaving only execution requirements.

Standalone Image Requirements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Allow smooth execution without external modifications.
- Do not include a `Dockerfile` in the repository.

Supported Languages
-------------------

The following languages are supported, each with specific requirements for detection, execution, and image base names:

- **C**:
  
  a. Detection: `Makefile` at the root.
  b. Execution: Compiled binary.
  c. Base Image: `whanos-c`.

- **Java**:
  
a. We must detect the `pom.xml` in the `app` directory.
b. Execute the `java -jar app.jar`.
c. And the base Image: `whanos-java`.

- **JavaScript**:
a. We must detect the: `package.json` at the root.
b. Execute the  `node .`.
c. And the base Image:  `whanos-javascript`.

- **Python**:
a. We must detect the: `requirements.txt` at the root.
b. Execute the  `node .`. `python -m app`.
c. And the base Image:  `whanos-python`.

- **Befunge**:
a. We must detect the: `main.bf` in the `app` directory.
b. Execute the  `node .`. Free choice.
c. And the base Image:  `whanos-befunge`.

.. note::
   Ensure that images only contain what is necessary for application execution to reduce image size and complexity.

Example
+++++++


    .. code-block:: yaml

            // Befunge Dockerfile.base
        FROM node:14.17.5-alpine // Base image Node.js version 14.17.5 with Alpine Linux
        RUN apk add --no-cache bash curl // Install bash and curl
        SHELL ["/bin/bash", "-c"] // Set bash as the default shell
        RUN curl -L https://gist.githubusercontent.com/Octopus773/af90e3164cbb5a2cfeb786f0590a89a6/raw/e0ae92fd1ea8fa8e5030605f3797f43837c3430f/befunge93-cli.js \ 
            -o /usr/local/bin/b93.js && chmod +x /usr/local/bin/b93.js // Download Befunge-93 CLI script and make it executable
        ONBUILD COPY . /app // Copy source code to /app (only for derived builds)
        ONBUILD WORKDIR /app // Set /app as the working directory (only for derived builds)
        ONBUILD CMD ["node", "/usr/local/bin/b93.js", "app/main.bf"] // Execute Befunge-93 app/main.bf (only for derived builds)
    
    .. code-block:: yaml

             // Befunge Dockerfile.standalone
        FROM node:14.17.5-alpine // Base image Node.js version 14.17.5 with Alpine Linux
        RUN apk add --no-cache bash curl // Install bash and curl
        SHELL ["/bin/bash", "-c"] // Set bash as the default shell

        RUN npm install -g befunge93@^1.0.5 && \ // Install Befunge-93 package globally via npm
            curl -L https://gist.githubusercontent.com/Octopus773/af90e3164cbb5a2cfeb786f0590a89a6/raw/e0ae92fd1ea8fa8e5030605f3797f43837c3430f/befunge93-cli.js \ 
            -o /usr/local/bin/b93.js && chmod +x /usr/local/bin/b93.js // Download CLI script for Befunge-93 and make it executable

        COPY app/ /app/ // Copy the application source code to /app
        WORKDIR /app // Set the working directory to /app
        CMD ["node", "/usr/local/bin/b93.js", "-f", "main.bf"] // Execute the Befunge-93 script with the specified file