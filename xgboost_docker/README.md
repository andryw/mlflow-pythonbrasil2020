Dockerized Model Training with MLflow
-------------------------------------
This directory contains an MLflow project that trains a linear regression model on the UC Irvine
Wine Quality Dataset. The project uses a Docker image to capture the dependencies needed to run
training code. Running a project in a Docker environment (as opposed to Conda) allows for capturing
non-Python dependencies, e.g. Java libraries. In the future, we also hope to add tools to MLflow
for running Dockerized projects e.g. on a Kubernetes cluster for scale out.

Structure of this MLflow Project
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This MLflow project contains a ``train.py`` file that trains a scikit-learn model and uses
MLflow Tracking APIs to log the model and its metadata (e.g., hyperparameters and metrics)
for later use and reference. ``train.py`` operates on the Wine Quality Dataset, which is included
in ``wine-quality.csv``.

Most importantly, the project also includes an ``MLproject`` file, which specifies the Docker 
container environment in which to run the project using the ``docker_env`` field:

Running this Example
^^^^^^^^^^^^^^^^^^^^

First, install MLflow (via ``pip install mlflow``) and install 
`Docker <https://www.docker.com/get-started>`_.

Then, build the image for the project's Docker container environment. You must use the same image
name that is given by the ``docker_env.image`` field of the MLproject file. In this example, the
image name is ``mlflow-docker-example``. Issue the following command to build an image with this
name:

  docker build -t mlflow-docker-example -f Dockerfile .


Finally, run the example project using ``mlflow run examples/docker -P alpha=0.5``.

What happens when the project is run?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Running ``mlflow run examples/docker`` builds a new Docker image based on ``mlflow-docker-example``
that also contains our project code. The resulting image is tagged as 
``mlflow-docker-example-<git-version>`` where ``<git-version>`` is the git commit ID. After the image is
built, MLflow executes the default (main) project entry point within the container using ``docker run``.

Environment variables, such as ``MLFLOW_TRACKING_URI``, are propagated inside the container during 
project execution. When running against a local tracking URI, MLflow mounts the host system's 
tracking directory (e.g., a local ``mlruns`` directory) inside the container so that metrics and 
params logged during project execution are accessible afterwards.
