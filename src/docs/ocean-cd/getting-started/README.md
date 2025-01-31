<meta name="robots" content="noindex">

# Get Started with Ocean CD

This page provides the detailed procedures to set up and get started with Ocean CD in your Spot console.

## Prerequisites

- Kubernetes cluster up and running
- Workstation in the Kubernetes cluster with kubectl installed
- A [Spot API token](https://docs.spot.io/administration/api/create-api-token)

## Get Started

To connect your first cluster to Ocean CD and define the configuration settings, you will complete the procedures in the order shown below.

1. Connect Cluster
2. Create Environment
3. Create Microservice
4. Create Notification Provider
5. Create Rollout Spec

These procedures are described in detail below. After you have completed the procedures, you can also set up External Verifications as needed.

To start the setup, do the following:

1. In the Spot console, go to Ocean CD in the left menu tree and click Settings.
2. Click Connect Cluster.

<img src="/ocean-cd/_media/a-getting-started-01.png" />

Ocean CD opens the Clusters tab.

## Connect Cluster

In the Clusters tab under Connect Cluster, do the following:

1. Fill in your cluster ID, an internal logical name that you provide (e.g., the name of the Kubernetes cluster you are connecting to) and your API token. The cluster ID must be unique, with up to 30 alphanumeric characters and should not contain spaces.
2. Copy the curl command and run it in the Kubernetes cluster you are connecting to.

<img src="/ocean-cd/_media/a-getting-started-02.png" />

The Ocean CD controller will be installed in the cluster, and a card will appear under the clusters tab with initial information about the cluster and the controller.

<img src="/ocean-cd/_media/getting-started-02.png" />

The card provides the following information:

- Clust Identifier: The name (cluster ID) you have provided.
- Last Heartbeat: Date and time of the last heartbeat sent by the controller.
- Cloud Service Provider: Your cloud provider where the underlying infrastructure is located.
- Microservices: The number of active microservices running on the cluster (set by the user and managed in Ocean CD).
- Successful Rollouts: The number and percentage of rollouts that have been successfully completed on this cluster.
- Kubernetes: Orchestrator and version
- Controller: The names of the pod and node on which the controller is installed and the version of the controller.

Later, after you have set up several CD clusters, you will have a list of several cards here, each identifying a separate Kubernetes cluster.

### Connect Additional Cluster

If you have already connected your first cluster to Ocean CD and would like to connect an additional cluster, click Connect Cluster on the upper right of the Clusters page.

## Create Environment

The environment is an internal Ocean CD object that tells where each microservice is deployed. In other words, the environment is the destination of an Ocean CD rollout.

Define the environment using the [Create Environment API](https://docs.spot.io/api/#operation/OceanCDEnvironmentCreate). In the API, you will define attributes that correspond to the following:

- Environment Name: The environment, e.g., Dev, Staging, Production, to which your microservice will be delivered. Must be a unique name.
- Description: A few words about the environment.
- Cluster ID: The name of the cluster where the environment is located (This must be a cluster ID of one of the clusters you connected to Ocean CD).
- Namespace: A Kubernetes logical definition of the part of the cluster to be used for the environment.

### Create Additional Environment

You can use the Create Environment API at any time to create additional environments. Cards for all the environments will appear under the Environments tab.

## Create Microservice

A microservice is an internal Ocean CD object that associates a Kubernetes deployment with an Ocean CD rollout spec. Information you provide in a microservice tells Ocean CD (based on label selectors) if it is managed or not.

Define the microservice using the [Create Microservice API](https://docs.spot.io/api/#operation/OceanCDMicroserviceCreate). In the API, you will define attributes that correspond to the following:

- Microservice name: The name of the app (i.e., microservice) you are delivering. Must be a unique name.
- Description: A few words about the microservice you are delivering.
- Label Selectors: A microservice uses label selectors to identify deployments that are managed by Ocean CD. These must be part of the deployment metadata labels. Ensure that each deployment that needs to be managed by Ocean CD includes the labels you define in the Ocean CD microservice, under the primary metadata section.
  Here is an example for the desired label location inside the deployment YAML:

<img src="/ocean-cd/_media/getting-started-06a.png" width="190" height="384" />

- Version Label Key: Ocean CD will search for this unique version label key to present as the microservice version. If the key does not exist, the version will be taken from the deployment manifest (pod template image).

### Create Additional Microservice

You can use the Create Microservice API at any time to create additional microservices. Cards for all the microservices will appear under the Microservices tab.

## Create Notification Provider

A notification provider is an external endpoint to which Ocean CD sends a webhook API with the detailed rollout process information and where your external test tools should listen.

Define the notification provider using the [Create Notification Provider API](https://docs.spot.io/api/#operation/OceanCDNotificationProviderCreate). In the API, you will define attributes that correspond to the following:

- Notification Provider Name: A name you provide indicating who the notification provider is. Must be unique.
- Description (optional): A few words indicating the purpose of these notifications.
- Endpoint URL: The address to which the API messages are sent.

The notifications will be enabled once you create a rollout spec or a cluster heartbeat notification that uses this webhook.

### Create Additional Notification Providers

You can use the Create Notification Provider API at any time to create additional notification providers.

## Create Rollout Spec

A rollout spec is an Ocean CD object that connects a microservice to its target environment and includes the logic that Ocean CD uses to manage the rollout process.

Define the rollout spec using the [Create Rollout Spec API](https://docs.spot.io/api/#operation/OceanCDRolloutSpecCreate). In the API, you will define attributes that correspond to the following:

- Rollout Spec Name: Give a name to the rollout spec. Must be unique.
- Environment: Enter the name of the environment you created above. Once you have created several environments, you can choose from any one of them.
- Microservice: Enter the name of the microservice you created above. Once you have created several microservices, you can choose from any one of them.
- Strategy: Rolling Update is the only valid value. You may add the [External Verification](ocean-cd/features/external-verifications) process as part of the rollout strategy implementation.
- Failure Policy:
  - For an automatic rollback, define its mode:
    - Immediate: Complete the failed rollout flow with an immediate deployment of the previous successful version. No need for additional phases or new rollouts.
    - New Rollout: Start a new rollout flow to deploy the previous successful version.
- Notification Provider: Enter one or more of the notification providers you defined above (use the name that you have provided).

Once you have filled in all the information for the rollout spec and received confirmation that the Create Rollout API was successful, then Ocean CD starts managing the continuous delivery of the microservice you specified according to the specifications you set up.

### Create Additional Rollout Specs

You can use the Create Rollout API at any time to create additional rollouts.

## Delete Ocean CD Controller

If you ever need to delete the Ocean CD Controller, you can do so safely with the procedure below.

From a workstation in your cluster:

1. Enter the command:

`kubectl delete mutatingwebhookconfiguration controller.oceancd.spot.io`

2. Then enter the command:

`curl -X POST 'https://api.spotinst.io/ocean/cd/clusterInstaller?clusterId=<CLUSTER_Id>' --header 'Authorization: Bearer <TOKEN>' | kubectl delete -f -`

## Documentation Map

- [Ocean CD Overview](ocean-cd/ocean-cd-overview)
- [Getting Started](ocean-cd/getting-started/)
- [Features](ocean-cd/features/)
  - [External Verifications](ocean-cd/features/external-verifications)
  - [Granular Visibility](ocean-cd/features/granular-visibility/)
    - [Detailed Rollout View](ocean-cd/features/granular-visibility/detailed-rollout-view)
  - [Rollback](ocean-cd/features/rollback)
  - [Webhook Notifications](ocean-cd/features/webhook-notifications)
