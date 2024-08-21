# Azure Machine Learning: Clustering Model Creation and Deployment

This project involved the creation, training, and deployment of a clustering model using Azure Machine Learning Studio. The process included setting up necessary resources, preparing data, training the model, and deploying it for real-time inference.

## Project Execution

### 1. Resource Group Creation

A resource group was created in the Azure portal. The process involved navigating to the **Resource groups** section, selecting **+ Create**, and entering the required details, including the resource group name, subscription, and region. The resource group was then created by selecting **Review + create** and confirming the settings with **Create**.

### 2. Machine Learning Workspace Setup

A new Machine Learning Workspace was set up in the Azure portal by navigating to the **Machine Learning** section and selecting **+ Create**. The workspace name, subscription, resource group, and region were specified, and the workspace was created after reviewing and confirming the details.

### 3. Access to Azure Machine Learning Studio

Access to Azure Machine Learning Studio was achieved by going to the **Overview** tab of the newly created workspace in the Azure portal and selecting **Launch Studio**.

<img src="https://i.imgur.com/0hSe9qF.png"/>
### 4. Compute Instance Creation

In Azure Machine Learning Studio, a compute instance was created by navigating to **Compute > Compute instances** and selecting **+ New**. A virtual machine configuration was selected, and the compute instance was created with the necessary parameters.

### 5. Compute Cluster Configuration

A compute cluster was configured by navigating to **Compute > Compute clusters** in Azure Machine Learning Studio and selecting **+ New**. The virtual machine was configured with standard parameters:
- **Type**: Dedicated
- **VM size**: CPU, DS11
- **Nodes**: 0 - 2
- **Idle seconds before scale down**: 120 seconds

The cluster was created after confirming these settings.
<img src="https://i.imgur.com/FzKpMIg.png"/>
### 6. Dataset Creation

A dataset was created and registered in Azure Machine Learning Studio by navigating to **Datasets** and selecting **+ Create dataset**. The dataset settings, including type and location, were configured, and the dataset was registered for use in experiments.
<img src="https://i.imgur.com/07bOEws.png"/>
### 7. Pipeline Creation

A new pipeline was created in the **Designer** tab of Azure Machine Learning Studio by selecting **+ New pipeline**. The dataset was added to the pipeline by dragging it onto the canvas.

### 8. Data Transformation

Data transformation was performed using several modules:
- **Select Columns in Dataset** was used to select relevant columns.
- **Clean Missing Data** was applied to handle any missing values.
- **Normalize Data** was used to standardize the dataset.
- **Split Data** was employed to divide the data into training and testing sets.
<img src="https://i.imgur.com/0FcY4PK.png"/>
### 9. Pipeline Execution as an Experiment

The pipeline was submitted and run as an experiment. Progress was monitored until the experiment was completed successfully.

### 10. Model Training

The **Train Clustering Model** module was attached to the pipeline for model training. A pre-built algorithm, K-Means Clustering, was selected, and the prepared data was connected to the clustering model for training.

### 11. Model Evaluation

Model evaluation was conducted by adding the **Assign Data to Clusters** module to compare the trained data with the original data. The **Evaluate Model** module was connected to assess the performance of the clustering model, and the pipeline was run to generate evaluation metrics.
<img src="https://i.imgur.com/5ji4LVv.png"/>
### 12. Real-Time Inference Pipeline Implementation

A Real-Time Inference Pipeline was created to allow input of new data and obtain real-time predictions. The pipeline was tested with new data to ensure accuracy.

### 13. Model Publishing

The model was published by naming it and selecting the compute type (e.g., Azure Container Instance). The model was then deployed for real-time use.

### 14. Python Script for Inference

A Python script was added in Azure Machine Learning Studio by navigating to **Notebooks** and creating a new notebook. The script was updated with the specific endpoint and key to connect it to the deployed model.

### 15. Python Script Functionality

The Python script was designed to interact with the Azure Machine Learning web service endpoint to make predictions based on new data. It sends data to the model, receives the prediction, and handles any errors that occur during the process.

```python
endpoint = 'YOUR_ENDPOINT' #Replace with your endpoint
key = 'YOUR_KEY' #Replace with your key

import urllib.request
import json
import os

data = {
    "Inputs": {
        "WebServiceInput0":
        [
            {
                    'CulmenLength': 49.1,
                    'CulmenDepth': 4.8,
                    'FlipperLength': 1220,
                    'BodyMass': 5150,
            },
        ],
    },
    "GlobalParameters":  {
    }
}

body = str.encode(json.dumps(data))


headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ key)}

req = urllib.request.Request(endpoint, body, headers)

try:
    response = urllib.request.urlopen(req)
    result = response.read()
    json_result = json.loads(result)
    output = json_result["Results"]["WebServiceOutput0"][0]
    print('Cluster: {}'.format(output["Assignments"]))

except urllib.error.HTTPError as error:
    print("The request failed with status code: " + str(error.code))

    # Print the headers to help debug
    print(error.info())
    print(json.loads(error.read().decode("utf8", 'ignore')))
```
