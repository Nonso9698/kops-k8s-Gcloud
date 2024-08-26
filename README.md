## kops-kubernetes-cluster-configuration

## Setting up a Kubernetes cluster on Google Cloud using kops involves several steps. Here's a step-by-step guide along with the code:

## Prerequisites:
#  Google Cloud SDK (gcloud)
#  kops
#  kubectl
#  Make sure you have these tools installed and configured.


## Step 1: Set Up Google Cloud Storage for kops State Store
   kops requires a state store to store its cluster configuration. We'll use a Google Cloud Storage (GCS) bucket for this purpose.

 
#  Create a GCS bucket for kops state store
   export KOPS_STATE_STORE=gs://<your-kops-bucket-name>
   gsutil mb $KOPS_STATE_STORE

## Step 2: Create a Kubernetes Cluster with kops

#  Define environment variables
   export PROJECT=<your-gcp-project-id>
   export ZONE=<your-gcp-zone>
   export CLUSTER_NAME=<your-cluster-name>
   export KOPS_FEATURE_FLAGS=AlphaAllowGCE # Enable GCE support in kops

#  Set up gcloud project and zone
   gcloud config set project $PROJECT
   gcloud config set compute/zone $ZONE

#  Create a new kops cluster
   kops create cluster \
    --cloud gce \
    --name $CLUSTER_NAME \
    --state $KOPS_STATE_STORE \
    --zones $ZONE \
    --project $PROJECT \
    --node-count 3 \
    --node-size n1-standard-1 \
    --master-size n1-standard-2 \
    --yes


## Step 3: Verify the Cluster
   Once the cluster is created, verify that it is up and running.


#  Export the cluster configuration
   kops export kubecfg --name $CLUSTER_NAME --state $KOPS_STATE_STORE

#  Check the cluster nodes
   kubectl get nodes

## Step 4: Manage the Cluster
   You can manage your kops cluster by editing the cluster or instance group configuration and then updating the cluster.


#  Edit the cluster configuration
   kops edit cluster --name $CLUSTER_NAME --state $KOPS_STATE_STORE

#  Edit the instance group (e.g., nodes or master)
   kops edit ig --name $CLUSTER_NAME nodes --state $KOPS_STATE_STORE

#  Apply the changes
   kops update cluster --name $CLUSTER_NAME --state $KOPS_STATE_STORE --yes

## Step 5: Deleting the Cluster
   If you need to delete the cluster, use the following command:

   kops delete cluster --name $CLUSTER_NAME --state $KOPS_STATE_STORE --yes


## Important Notes:

#1. The AlphaAllowGCE  feature flag is required since kops support for Google Cloud is still considered alpha.
#2. Ensure your Google Cloud account has the necessary permissions to create resources (e.g., VMs, firewalls, networking components).
#3. This guide assumes you are using a default network; custom networks may require additional configuration.

