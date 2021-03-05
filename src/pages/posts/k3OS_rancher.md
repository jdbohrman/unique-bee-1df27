---
title: >-
  Automate k3OS Cluster Registration to Rancher with Argo Workflows and
  Scripting Magic
date: '2020-07-20'
thumb_img_path: >-
  https://images.ctfassets.net/ocdgldwpic0i/3YqVditU6WSEQxsGJxmJJn/535a3a952be02c9f36fb3e0e58809a70/fd0b27004fc2a483b971fc7d606e97ca44958c64-1067x400.png
thumb_img_alt: edge-native-k3OS
content_img_path: >-
  https://images.ctfassets.net/ocdgldwpic0i/7ojowjh3zRxIrhg9AD71uK/2ffaf1e2c9e0ff6be5110acf92b63423/fd0b27004fc2a483b971fc7d606e97ca44958c64-1067x400.png
content_img_alt: edge-native-k3OS
excerpt: >-
  As the Kubernetes ecosystem grows, new technologies are being developed that
  enable a wider range of applications and use cases
seo:
  title: >-
    Automate k3OS Cluster Registration to Rancher with Argo Workflows and
    Scripting Magic
  description: A blog post about edge-native kubernetes
  extra:
    - name: 'og:type'
      value: article
      keyName: property
      type: stackbit_page_meta_extra
    - name: 'og:title'
      value: >-
        Automate k3OS Cluster Registration to Rancher with Argo Workflows and
        Scripting Magic
      keyName: property
      type: stackbit_page_meta_extra
    - name: 'og:description'
      value: Interior design is “the art or process of designing the interior
      keyName: property
      type: stackbit_page_meta_extra
    - name: 'og:image'
      value: images/1.jpg
      keyName: property
      relativeUrl: true
      type: stackbit_page_meta_extra
    - name: 'twitter:card'
      value: summary_large_image
      type: stackbit_page_meta_extra
    - name: 'twitter:title'
      value: How To Choose An Interior Designer
      type: stackbit_page_meta_extra
    - name: 'twitter:description'
      value: Interior design is “the art or process of designing the interior
      type: stackbit_page_meta_extra
    - name: 'twitter:image'
      value: images/1.jpg
      relativeUrl: true
      type: stackbit_page_meta_extra
  type: stackbit_page_meta
template: post
stackbit_url_path: /posts/k3OS_rancher
---
## Introduction

As the Kubernetes ecosystem grows, new technologies are being developed that enable a wider range of applications and use cases. The growth of edge computing has driven a need for some of these technologies to enable the deployment of Kubernetes to low-resource infrastructure to the network edge. In this blog post, we are going to introduce you to one method of deploying k3OS to the edge. You can use this method to automatically register your edge machine to a Rancher instance as a control plane. We will also discuss some of the benefits of automating deployments to physical machines.

Rancher Labs developed [k3OS](https://github.com/rancher/k3os) to address this exact use case. The goal of the k3OS project is to create a compact and edge-focused Kubernetes OS packaged with Rancher Labs’ [K3s](https://k3s.io/?\__hstc=263286291.fe53148920ef5698b8d62e88ce7e8ceb.1613427780546.1613427780546.1614222922740.2&\__hssc=263286291.1.1614222922740&\__hsfp=2289783843), the certified Kubernetes distribution built for IoT and edge computing. This enables easy deployment of applications to resource-constrained environments such as devices deployed at the edge.

While still in its infancy, k3OS is battle tested and is being used in production in a variety of environments. To fully grasp the full benefits of edge computing, you need to conserve as much space as possible on the infrastructure you are deploying to.

## Introducing the Argo Project

[Argo](https://argoproj.github.io/) is a Cloud Native Computing Foundation project designed to alleviate some of the pain of running compute-intensive workloads in container-native environments. The [Argo Workflows](https://argoproj.github.io/projects/argo) sub-project is an open source container-native workflow engine for orchestrating parallel jobs in Kubernetes. It is implemented as a Kubernetes Custom Resource Definition (CRD), which is essentially an extention of the Kubernetes API.

With Argo Workflows, we can define workflows where every step in the workflows is a container, and model multi-step workflows as a sequence of tasks or capture the dependencies between tasks using a directed acyclic graph (DAG). This is useful when automating the deployment and configuration of edge-native services. We will see many of these aspects of Argo Workflows come into play later on in this demo.

## Step 1. Setting Up a Demo Environment

To simulate a working *edge* site, we will need to spin up k3OS on a local VM and then use an Argo Workflow to phone into a remote Rancher instance. In this section we will:

*   Download the k3OS **iso** [here](https://k3os.io/?\__hstc=263286291.fe53148920ef5698b8d62e88ce7e8ceb.1613427780546.1613427780546.1614222922740.2&\__hssc=263286291.1.1614222922740&\__hsfp=2289783843)

*   Deploy Rancher

*   Install Argo Workflows

### Set Up Local VM (Edge)

The steps for installing [VirtualBox](https://www.virtualbox.org/wiki/Downloads) are outside the scope of this demonstration. Once we have VirtualBox installed, we’ll start it up and go through the initial process of setting up the VM and attaching the k3OS iso. Once we’ve done that, we will start up the machine and this intro screen will greet us:

![](https://rancher.com/img/blog/2020/k3os/k3os-01.png)

At this point, we will open up a terminal and add the **k3OS** VM to our **config.yaml** file. We can use this handy helper script:

**Note**: Need to port forward 3022 and 4443

Once we’ve successfuly pulled the **.kubeconfig** file, we should be ready to deploy the control plane.

### Deploy Rancher (Cloud)

To deploy Rancher to a cloud environment, perform the following steps:

1.  Clone or download [this](https://github.com/rancher/quickstart) repository to a local folder

2.  Choose a cloud provider and navigate into the provider’s folder

3.  Copy or rename **terraform.tfvars.example** to **terraform.tfvars** and fill in all required variables

4.  Run **terraform init**

5.  Run **terraform apply**

When provisioning has finished, Terraform will output the URL to connect to the Rancher server. Two sets of Kubernetes configurations will also be generated:

**kube_config_server.yaml** contains credentials to access the RKE cluster supporting the Rancher server **kube_config_workload.yaml** contains credentials to access the provisioned workload cluster

For more details on each cloud provider, refer to the documentation in their respective folders in the repo.

### Step 2. Install Argo Workflows

**Install Argo CLI**:

Download the latest Argo CLI from the [Argo releases page](https://github.com/argoproj/argo/releases).

**Install the controller**:

In this step, we’ll install Argo Workflows to extend the Kubernetes API with the **Workflows** CRD. This will allow us to chain multiple “jobs” together in sequence. Installing Argo Workflows is as easy as switching to your k3OS cluster and running:

**Note**: On Google Kubernetes Engine (GKE), you may need to grant your account the ability to create new clusterroles.

### Step 3. Configure the Service Account to Run Workflows

**Roles, RoleBindings and ServiceAccounts**

In order for Argo to support features such as artifacts, outputs, access to secrets, etc. it needs to communicate with Kubernetes resources using the Kubernetes API. To do that, Argo uses a ServiceAccount to authenticate itself to the Kubernetes API. You can specify which Role (i.e. which permissions) the ServiceAccount that Argo uses by binding a Role to a ServiceAccount using a RoleBinding.

Then, when submitting Workflows, specify which ServiceAccount Argo uses:

When no ServiceAccount is provided, Argo will use the default ServiceAccount from the namespace from which runs, which will almost always have insufficient privileges by default.

**Granting admin privileges**

In this demo, we will grant the **default** **ServiceAccount** admin privileges (i.e., we will bind the **admin** **Role** to the **default** **ServiceAccoun** of the current namespace):

**Note**: This will grant admin privileges to the **default** **ServiceAccount** in the namespace that the command is run from, so you will only be able to run Workflows in the namespace where the **RoleBinding** was made.

### Step 4. Running the Workflow

From here, you can submit Argo Workflows via the CLI in a variety of ways:

You can also create Workflows directly with kubectl. However, the Argo CLI offers additional features, such as YAML validation, workflow visualization, parameter passing, retries and resubmits and suspend and resume.

So let’s create a **workflow.yaml** file and add all of the content here into it:

At a high level, this workflow is essentially taking a script and running as a pod in our cluster with certain variables that allow it to:

*   Log in to the Rancher API

*   **cURL** a Rancher API token with **TinyTools**

*   Set the URL of the Rancher Server as a variable

*   Extract the cluster ID

*   Retrieve and apply the manifests

Next we’ll want to cd into the directory with our workflow and run:

You can then watch the workflow provision a pod called **cluster-up** in your cluster that will connect to Rancher:

![](https://rancher.com/img/blog/2020/k3os/k3os-02.png)

### Conclusion: Why Automate Tasks at the Edge

Now that you’ve gotten an introduction to automating edge deployments with k3OS and Argo, let’s discuss some of the reasons why this type of automation is important. Automating these tasks is beneficial when spinning up physical machines for operations such as Industrial Internet of Things (IIOT), where the people interacting with the machines are hardware technicians as opposed to cloud engineers.

At the edge, often the provisioning of physical machines is laborious – but shouldn’t have to be done one at a time. With this approach, technicians can simply insert something like a USB that will kick off an ISO and run a script to kick off the provisioning of the machines, along with the registration to the control plane that you see here.

We hope you enjoyed this post. Stay tuned for for more topics about edge computing coming soon.

***This content was originally posted on the Rancher Labs blog.***
