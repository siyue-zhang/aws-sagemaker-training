# aws-sagemaker-training

Reference: https://github.com/aws/sagemaker-training-toolkit

Amazon SageMaker is a fully managed service for data science and machine learning (ML) workflows. You can use Amazon SageMaker to simplify the process of building, training, and deploying ML models.

To train a model, you can include your **training script** and **dependencies** in a **Docker container** that runs your training code. A container provides an effectively isolated environment, ensuring a consistent runtime and reliable training process.

The SageMaker Training Toolkit can be easily added to any Docker container, making it compatible with SageMaker for training models. If you use a prebuilt SageMaker Docker image for training, this library may already be included.

For more information, see the Amazon SageMaker Developer Guide sections on using Docker containers for training.


## Installation

To install this library in your Docker image, add the following line to your Dockerfile:
```dockerfile
RUN pip3 install sagemaker-training
```

## Usage

The following are brief how-to guides. For complete, working examples of custom training containers built with the SageMaker Training Toolkit, please see the example notebooks.

### Create a Docker image and train a model

1. Write a training script (eg. train.py).

2. Define a container with a Dockerfile that includes the training script and any dependencies.

The training script must be located in the /opt/ml/code directory. The environment variable SAGEMAKER_PROGRAM defines which file inside the /opt/ml/code directory to use as the training entry point. When training starts, the interpreter executes the entry point defined by SAGEMAKER_PROGRAM. Python and shell scripts are both supported.

```dockerfile
FROM yourbaseimage:tag

# install the SageMaker Training Toolkit 
RUN pip3 install sagemaker-training

# copy the training script inside the container
COPY train.py /opt/ml/code/train.py

# define train.py as the script entry point
ENV SAGEMAKER_PROGRAM train.py
```

3. Build and tag the Docker image.
```
docker build -t custom-training-container .
```

4. Use the Docker image to start a training job using the SageMaker Python SDK.

```python
from sagemaker.estimator import Estimator
estimator = Estimator(image_name="custom-training-container",
                      role="SageMakerRole",
                      train_instance_count=1,
                      train_instance_type="local")

estimator.fit()
```
To train a model using the image on SageMaker, [push the image to ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/docker-push-ecr-image.html) and start a SageMaker training job with the image URI.


## Docker

![image](./docker.PNG)

