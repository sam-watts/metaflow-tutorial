# Using metaflow

```
source env/bin/activate
python -m pip install metaflow
```

### 1. Running Locally

In it's simplest mode, metaflow can run a DAG or `Flow` entirely on a local machine

```
env/bin/python metaflow-tutorials/01-playlist/playlist.py run
```

We can then use the metaflow client to inspect artifacts from this run

```python
from metaflow import Flow

# get the runs of the flow we just ran
flow = Flow("PlayListFlow")
runs = list(flow)
print(runs)

# inspect the data of the latest run
print(flow.latest_successful_run.data)

# get the bonus recommendation
print(flow.latest_successful_run.data.bonus)
```

### 2. Configuring cloud resources - demo version

Metaflow can use cloud resources instead of running entirely locally. Here we will setup:
* Metaflow Datastore: Store artifacts of `Run`s in S3. This enhances reproducibility, collaboration and tracibility
* Metaflow Service: Store pointers to artifacts. This also means we can use the UI to track `Runs`. For this demo, we make sure the metaflow service is running locally on port 8000 - see https://github.com/Netflix/metaflow-service

For now we won't setup these components, as they are more difficult:
* AWS Batch: Runs `Step`s in isolated environments in the cloud, with scalable resources for each `Step`
* AWS Step Functions: Export an entire `Flow` to the cloud, to enable full automation of `Run`s with scheduling and triggering

To configure these AWS settings, we run:

```
metaflow configure aws --profile=remote-mode
```

This launches an interactive workflow where we can set some values based on our mock environment:

```
METAFLOW_DATASTORE_SYSROOT_S3=s3://tgtg-sagemaker-experiments/metaflow-testing
METAFLOW_SERVICE_URL=http://localhost:8000
```

### 3. Running against cloud resources

```
METAFLOW_PROFILE=remote-mode \
    env/bin/python metaflow-tutorials/01-playlist/playlist.py run
```

