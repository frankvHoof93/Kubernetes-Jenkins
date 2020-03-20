# Setting up a Build-Slave

## Step 1: Configuring the connection

- Open Jenkins, and navigate to *Manage Jenkins -> Manage Nodes and Clouds -> Configure Clouds -> Add -> Kubernetes* (Configure Clouds is in the left-side menu)
- Hit Test Connection, and you should see *Connection test successful*
  - Jenkins Auto-Configures by using the *config* file created during the [Configuration-instructions](Configuration-Instructions.md)
  - *In the Fontys-Environment the test will throw a permission-error. Set the **Kubernetes Namespace** configuration to get access*
- Set the *Concurrency Limit* 
  - The concurrency limit is also dependent on the limit to the amount of Pods in the namespace. See [Setup Step 2](../docs/Setup-Instructions.md#step-2:-create-a-namespace).
- Save the configuration

## Step 2: Set the master IP

- Get the IP of the Jenkins-Node, one of two ways:
  - Use *kubectl get pods* and *kubectl describe pod POD_NAME* locally in a shell
  - Run the following command in the Jenkins Script Console **println InetAddress.localHost.hostAddress**
    - The Script Console can be found under *Manage Jenkins -> Script Console*
- Navigate back to the *Configure Clouds*-Menu, and set the Jenkins URL (**http://MASTER_IP:8080**)

## Step 3: Find or Create a Docker Image for the Slave

- Use one of the existing Slave-Images found at the [Jenkins Docker Hub](https://hub.docker.com/u/jenkins) as a build slave.
- Alternatively, you can build your own, if you have any specific requirements. Make sure to use [jnlp-slave](https://hub.docker.com/r/jenkins/jnlp-slave) as a base.
  - My personal .Net Core 3.1.2 buildslave can be found [here](https://hub.docker.com/repository/docker/frankvhoof/jenkins-dotnet-slave). The Dockerfile for it can be found [here](../Slaves/DotNet/Dockerfile-DotNet)

## Step 4: Add a Pod Template

- Navigate back to the *Configure Clouds*-Menu in Jenkins
- Add a Pod-Template, and give it a name (e.g. *jenkins-dotnet*)
- Set the namespace for the pod equal to the namespace of the master
- Add a label to the pod, so you can identify its purpose (e.g. dotnet)
- Under **Usage** there are two options:
  - **Use this node as much as possible**: Use this for generalised build slaves
  - **Only build jobs with label expressions matching this node**: Use this for slaves with specific requirements. To build using the slave, you will need to make sure the **label** in the build-stage matches the label of the Pod

## Step 5: Add a Container Template

- Click **Add Container -> Container Template**
- **IMPORTANT: THE NAME MUST! BE SET TO 'jnlp'. IF IT IS NOT, JENKINS WILL CREATE A POD WITH 2 CONTAINERS (one being the base slave-image) AND CONNECT TO THE 2ND ONE (not your custom slave)**
- Set the Docker image to your custom Slave Image (e.g. *frankvhoof/jenkins-dotnet-slave:3.1.2*)
- Enable *Always pull image* to make sure your image is always up to date
- Clear the *Command to run*, or Jenkins will try to run a shell instead of a build
- The slave requires 2 arguments in order to connect to the master:
  - Set the *Arguments to pass to the command* to: **${computer.jnlpmac} ${computer.name}**
- Set the **Pod Retention** and the **Time in minutes to retain agent when idle** to preferred values
  - These options allow you to keep a Slave alive (for a while) after the build completes. This can allow you to open a shell to the Slave to check its logs if anything is going wrong.
- If you want, you can disable **Show raw yaml in console** to keep the yaml for the Pod from being displayed for every build

## Step 6: Test the Slave

- Create a new Freestyle Project on the Jenkins Master (*New Item -> Freestyle Project*)
- If you set a label to the slave, and set the Usage to *Only build with label matching this node*, then you must:
  - Enable *Restrict where this project can be run*
  - Set the *Label Expression* to equal that of the Slave Label
- Add a Build Step (of type *Execute Shell*)
- Add a command to check if your build-requirements are available (e.g. **dotnet --version** or **npm -v**)
- Add a command to sleep the pod for a while, just to check whether it boots and shuts down correctly (**sleep 15**)
- Save the project, and hit Build. The build should succeed
  - If it does not succeed, check the output of the build. If your required tools are missing, check how many containers are running in the pod (see the previous step). It might be spinning up 2 containers, and connecting to the default slave.
- You can now delete the test-project, and add your projects to the master

## Step 7: Prevent builds on the Master

- Navigate to **Manage Jenkins -> Manage Nodes and Clouds -> Master -> Configure**
- Set the **# of executors** to 0 to prevent builds running on the master itself