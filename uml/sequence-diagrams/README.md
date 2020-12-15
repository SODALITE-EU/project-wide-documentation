# UML Sequence Diagrams of SODALITE

This map contains UML sequence diagrams describing various UML use cases identified during SODALITE requirement analysis.

## Runtime Layer

### Preface

Currently, the support for the following execution platforms is provided:

- OpenStack: as a provisioner of IaaS virtual resources in private clouds;
- AWS EC2: as a provisioner of IaaS virtual resources in public clouds;
- Kubernetes: as an environment and orchestration for containerized application;
- PBS Torque and Slurm: as a batch and resource management systems common in HPC.

Each execution platform has its own way to connect to it (e.g. in clouds and Kubernetes, REST APIs are common; for HPC, connections are through ssh), and the orchestrator must use the right "API" depending on the selected platform. Furthermore, a certain type of resources can be requested depending on their availability at the resource providers sites. For example, consider a request for a GPU:

- In OpenStack, it can be achieved by specifying an availability zone that contains GPU resources or by selecting a certain flavour that encodes the selection of GPU resources. It all depends on the configuration of the infrastructure provider.
- In AWS, the GPU instances of certain flavours (e.g. P3 and P4 instances) can be selected.
- In Kubernetes, the GPU resources can be scheduled by specifying the resource limits or by using special node labels and node selectors to schedule pods to appropriate GPU-labelled nodes.
- In batch systems, the GPU request is specified in one of the job options or by selecting a certain queue.

The considered execution platforms shall provide a Lightweight Runtime Environment (LRE) to deploy and execute application container images and artifacts. In IaaS clouds (OpenStack, AWS) and Kubernetes, Docker runtime is considered. In HPC, Singularity runtime is considered due to performance and security limitations of Docker. The application components are assumed to be containerised, i.e. execution logic and dependencies are encapsulated within a container image, or assumed to use a container runtime, i.e. use only the encapsulated dependencies. 

### UC6: Execute Provisioning, Deployment and Configuration

Once an AADM (abstract application deployment model) has been defined, Application Ops Expert initiates the deployment via SODALITE IDE. SODALITE IDE provides AADM to IaC Blueprint Builder, which in turn creates a CSAR (cloud service archive - an archive that contains TOSCA blueprints and metadata, and deployment and implementation artifacts) and passes it to the Orchestrator. The Orchestrator is then saves the CSAR and returns a Blueprint ID to the IaC Blueprint Builder, which is further passed to SODALITE IDE. At this point, the deployment is registered in the Orchestrator system and can be referred through its Blueprint ID, e.g. for the deployment execution or deployment updates.

Once the Blueprint ID is received by SODALITE IDE, it directly requests the Orchestrator to start the deployment and receives Session ID to monitor the deployment progress. Optionally, a set of inputs can be passed along the request to parameterise the deployment. Then, the Orchestrator starts the deployment by executing the deployment workflow specified in the blueprint. For each execution platform specified in the blueprint, the resources are instantiated and the application components are deployed on top of them. As such:

- In IaaS clouds, before the deployment of application components, the virtual resources must be firstly provisioned. For that, the Orchestrator requests IaaS resource manager (OpenStack, AWS EC2) to provision a set of virtual machines (VMs) and other resources that the application demands, e.g. security group, network and storage. The LRE is then installed on VMs as a runtime for execution of application components. Upon the installation, the Orchestrator configures and deploys application components, pulling them from specified image registries.
- Kubernetes automates resource provisioning and application deployment by exposing an endpoint,
through which the deployment and configuration descriptions are passed. Hence, the Orchestrator
submits these descriptions to Kubernetes, which configures and deploys application components, pulled from specified image registries.
- When resource management systems are selected (e.g. Torque or Slurm), the Orchestrator pre-uploads the artifacts (e.g. pulling required container images) and job description script to the user workspace (e.g. home directory of the user) on login (front-end) nodes and then submits the job to the batch system. UC7 describes the start of applications, deployed using one of the resource management systems.

At this point, the deployment of the heterogeneous application components is performed on different resources and the application is started. Optionally, the configuration of a monitoring platform can be additionally executed if such mechanisms are provided to the Orchestrator.

#### TODO: AAI

### UC7: Start application

UC7 is accompanying UC6 by describing in details the start of batch applications, i.e. applications that take an input, process it and give the results, unlike the services (e.g. web servers, REST APIs), which start right after deployment and run continuously. Furthermore, these applications can be deployed once and executed several times with different inputs.

When a job is submitted to the batch system, it does not start immediately, but it is firstly enqueued to specified or default queue. It starts, once the job's turn comes in the queue and it leaves the queue for its execution. It may take some time depending on the queue availability.

During the deployment (or redeployment), when resource management system is selected as an execution platform, the Orchestrator submits a job and monitors its state, whether it is running or finished. When the job is finished, the Orchestrator determines whether the execution was successful or failed. The deployment terminates when the execution is failed, otherwise the deployment continues with the next application component.

In general, when a component starts, the execution environment of the application sends a message to Monitoring Platform to indicate that the component is alive and prepared to send metrics. Once the components register to Monitoring, Orchestrator can initiate collection of metrics for its purposes (e.g., check application health).

#### TODO: AAI

### UC10: Execute Partial Redeployment

A redeployment (or deployment updates) is an act of modifying application topology or application parameters at runtime. The partial redeployment refers to updates of only affected components of application, i.e. those components that require modifications during the runtime of application as oppose to modification the whole application topology. As an example, a certain application component needs the updates of the container image or it requires scaling to improve the performance. Another example might be the changes in application topologies, when new components are introduced or old components need to be removed.

In SODALITE, partial redeployment can be triggered via SODALITE IDE when Application Ops Expert manually changes AADM or he/she confirms one of the Refactoring Options suggested by Refactorer as described in UC9. Refactorer may also be configured to automatically request redeployment if it detects any Refactoring Options at runtime. In both cases, a reference to a particular Blueprint ID, which is obtained after execution of UC6, should be used.

When a redeployment is triggered manually by Application Ops Expert, SODALITE IDE then requests IaC Blueprint Builder to create CSAR from updated AADM and update the blueprint in the Orchestrator. After that, SODALITE IDE directly requests the Orchestrator to start the deployment updates and receives Session ID to monitor the redeployment progress.

(?) Alternatively, when a redeployment is triggered automatically by Refactorer, it creates a CSAR, which reflects the updated deployment, and updates the blueprint in the Orchestrator. Similarly, it then directly requests the Orchestrator to start the deployment updates and receives Session ID to monitor the redeployment progress.

At this point, the deployment updates are executed by the Orchestrator. The Orchestrator derives the difference between current and updated deployments and applies adaptation actions until the current state of deployment becomes the updated state. Such adaptation actions are performed on the Execution Platforms used for the redeployment. If the selected platform is IaaS Cloud or Kubernetes, the actions that might be performed are the following:

- any form of scaling (in/out/up/down),
- migration to another Execution Platform,
- deployment of the new application components introduced by the Application Ops Expert and removal of current components.

It should be notes that Kubernetes, being itself an orchestration platform, is enforcing partial redeployment. For what concerns resource management systems (common in HPC), these Execution Platforms lack flexibility in scaling at runtime, hence the scaling actions are not present as possible adaptation actions; however, all the other actions can be executed as well (migration, deployment and removal of components).

#### TODO: AAI

#### TODO: Sync with deployment updates from xopera

#### TODO: Sync with refactorer