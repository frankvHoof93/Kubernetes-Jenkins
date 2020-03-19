# Setup-Instructions

**These instructions are based around the Kubernetes-environment set up by [Fontys](https://fontys.nl/)**

**They sometimes revolve around using Rancher to set some configurations (where these are not possible due to permissions)**

**Instructions which should be customized in other environments are *italicized* **

## Step 1: Setting up Kubectl Locally

- Get [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- Add it to the system's PATH
- Create a new folder called “.kube” in the user home folder. ($HOME in shell)
- Create a file called “config” inside the newly created .kube folder.
- *Go to rancher and copy the contents from the Kubeconfig File on the homepage (main cluster -> Kubeconfig file) into the config file.*

## Step 2: Create a namespace

- *Create a new Namespace in Rancher (Click Namespaces in the top Menu, then __Add__ __Namespace__ )*
  - Enter a name for your namespace, and set the resource quotas (Required: 2 pods, 4GB storage)
  - If desired you can run *kubectl config set-context --current --namespace=your-name-space-name* to change the current namespace context so you won’t have to run *-n your-name-space-name* on every command
 
## Step 3: Add Secret

- *Create a new Secret in Rancher (Click Resources->Secrets in the top Menu, then __Add__ __Secret__ )*
  - Scope: Single namespace (Choose the namespace you just created)
  - Name: Jenkins
  - Secrets Values:
    - Key   --						Value
	- jenkins-admin-user -- admin
	- jenkins-admin-password -- **ENTER_PASS_HERE** *pick a password for usage with the Jenkins Admin-account*

## Step 4: Edit .Yaml's

- Download the .yaml-files in this repo
- Open the [Jenkins-Ingress.yaml](../Deployment/Jenkins-Ingress.yaml) in a text editor
- Replace the *YOUR_HOST_NAME_HERE*-text with the (full) host for the deployment (e.g. jenkins.mycompany.com)
  - *For the Fontys-Deployment, follow the same naming scheme that rancher uses, replacing the word rancher in the url with a UNIQUE name (globally unique, so don't use 'jenkins').*
- Save the file

## Step 4: Deploy Jenkins

- Open a shell in the [Deployment-Folder](../Deployment)
- Run the following command: *kubectl apply -f .* 
  - Add -n *YOUR_NAMESPACE_NAME* if you did not set the context previously
- Wait for 3-5 minutes for Jenkins to boot up (you can check it's progress through Rancher (Pod->View Log))

## Step 5: Open Jenkins

- Open a browser, and navigate to the host that you set up in [step 4](##-step-4:-deploy-jenkins)
- Jenkins should now load, and forward you to the login-screen
- You can log in using the username and password that you set up in [step 3](##-step-3:-add-secret)

- *Alternatively, you can go to the website by clicking the 80/http-link under your Workload-name (jenkins) in Rancher*