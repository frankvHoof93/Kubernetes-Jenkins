# Configuration-Instructions

**These instructions are based around the Kubernetes-environment set up by [Fontys](https://fontys.nl/)**

**They sometimes revolve around using Rancher to set some configurations (where these are not possible due to permissions)**

**Instructions which should be customized in other environments are *italicized* **

## Step 1: Grabbing the Kubeconfig File

- *Go to rancher and copy the contents from the Kubeconfig File on the homepage (main cluster -> Kubeconfig file) into a local file.*
- *Alternatively you can use the config-file you set up during the local installation of Kubectl*

## Step 2: SSH into the active Jenkins-Pod

- Verify the Pod is running by executing: *kubectl get pods* (*-n YOUR_NAMESPACE_NAME*)
- Open a shell into the Pod by:
  - Running the command *kubectl exec -it **POD_NAME** -- /bin/bash*
  - Opening the workload in Rancher and clicking Options (three dots) -> Execute Shell

## Step 3: Set up the Kubeconfig in the Pod

- Navigate to the .kube directory: *cd $HOME/.kube*
- If the .kube directory does not exist yet, you can create it using *mkdir .kube* once inside the $HOME directory
- Use the command *cat > config* to create a file and start writing the config
- Add all the text from the Kubeconfig file, and use *Ctrl-D* to exit
- Use *cat config* to check the context of the file
- If desired you can run *kubectl config set-context --current --namespace=your-name-space-name* to change the current namespace context
- Check whether the Kubectl can connect by using *kubectl get pods*