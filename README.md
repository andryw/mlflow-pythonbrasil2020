# MLFlow tutorials

Some examples of how to use MLFlow. Those examples were used in my presentation at 2020's Python Brasil conference.

## MLFlow

MLflow is a platform to manage the ML lifecycle, including experimentation, reproducibility, deployment, and a central model registry. MLflow currently offers four components:

- Tracking:

Record experiment configs: code, data, parameters, metrics.
MLfow provides a UI to access those configs and compare many experiments.

- Projects

Package data science code in a format to reproduce experiments on any platform

- Model

Package machine learning models to be deployed in diverse serving environments.
It's possible to serve models as APIs with only 1 command-line

- Model Registry

A central repository of models, with versioning.


## Tracking

The folder xgboost has an example of how to use MLFlow Tracking to log the parameters, metrics, and models of an experiment using XGBoost. To run this example:

``` properties
cd xgboost
python train.py
```

To see the results in the MLFlow UI dashboard:

``` properties
mlflow ui
```

## Project

To run the xgboost example as Project (inside a Conda Environment):

``` properties
 mlflow run xgboost -P colsample-bytree=0.7
``` 

To run the xgboost example as Project (inside a Docker):

``` properties
docker build -t mlflow-iris-xgboost -f xgboost_docker/Dockerfile xgboost_docker/

mlflow run xgboost_docker -P colsample-bytree=0.7
``` 


## Model

To serve a trained model as an API:

``` properties
mlflow models serve -m runs:/<RUN-ID>/<MODEL-PATH>
```

Accesing the API:
``` bash
curl http://127.0.0.1:5001/invocations -H 'Content-Type: application/json' -d '{         
	"columns": ["0", "1", "2", "3"], 
	"data": [ [5.1, 3.5, 1.4, 0.2], [5.9, 3. , 5.1, 1.8] ]     
	}'


[[0.95, 0.02, 0.02], [0.03, 0.12, 0.83]]

```


## Registry Model

To run a server to track the experiment configs (on a database, mandatory to a Registry Model Server), save the artifacts (on an S3 bucket) and register the models:

``` properties

mlflow server --backend-store-uri sqlite:///mlflow.db --default-artifact-root s3://<bucket>/<path>/ --host 0.0.0.0

```

To run your experiment in your machine and use the server to save the experiment configs and the artifacts, you need to define the MLFLOW_TRACKING_URI environment variable. You need the permissions to access the server and/or the S3 bucket.

``` properties
export MLFLOW_TRACKING_URI='http://127.0.0.1:50000' 
mlflow run xgboost
```

To serve a registered trained model of the Production stage as an API. You can register a model and put it in the Production stage using the UI or via code.

``` properties
export MLFLOW_TRACKING_URI=http://127.0.0.1:50000 
mlflow models serve -m "models:/Iris/Production"
```

Accesing the API:

``` properties
curl http://127.0.0.1:5001/invocations -H 'Content-Type: application/json' -d '{         
	"columns": ["0", "1", "2", "3"], 
	"data": [ [5.1, 3.5, 1.4, 0.2], [5.9, 3. , 5.1, 1.8] ]     
	}'


[[0.95, 0.02, 0.02], [0.03, 0.12, 0.83]]
```
