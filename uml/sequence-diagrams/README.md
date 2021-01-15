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

Once an AADM (abstract application deployment model) has been defined, an Application Ops Expert initiates the deployment via the SODALITE IDE. 
The SODALITE IDE provides the AADM to the IaC Blueprint Builder, which in turn creates a CSAR (Cloud Service Archive - an archive that contains TOSCA blueprints and metadata, and the deployment and implementation artifacts) and passes it to the Orchestrator. 
The Orchestrator saves the CSAR and returns a Blueprint ID to the IaC Blueprint Builder, which is further passed back to SODALITE IDE. 
At this point, the deployment is registered in the Orchestrator system and can be later referred through its Blueprint ID, e.g. for the deployment execution or deployment updates.

Once the Blueprint ID is received by the SODALITE IDE, it directly requests the Orchestrator to start the deployment and receives a Session ID to monitor the deployment progress. 
Optionally, a set of inputs can be passed along the request to parameterise the deployment. 
Then, the Orchestrator starts the deployment by executing the deployment workflow specified in the blueprint. 
For each execution platform specified in the blueprint, its resources are instantiated, and the application components are deployed on top of them. As such:

  - In IaaS clouds, before the deployment of the application components, the virtual resources must be firstly provisioned. For that, the Orchestrator requests to the IaaS resource manager (e.g. OpenStack, AWS EC2) to provision a set of virtual machines (VMs) and other resources that the application demands, e.g. security group, network and storage. The LRE is then installed on VMs as a runtime for the execution of the application components. Upon the installation, the Orchestrator configures and deploys application components, pulling them from specified image registries.
  - Kubernetes automates the resource provisioning and the application deployment by exposing an endpoint, through which the deployment and configuration descriptions are passed. Hence, the Orchestrator submits these descriptions to Kubernetes, which configures and deploys the application components, pulled from specified image registries.
  - When resource management systems are selected (e.g. Torque or Slurm), the Orchestrator pre-uploads the artifacts (e.g. pulling required container images) and the job description script to the user workspace (e.g. home directory of the user) on login (front-end) nodes and then submits the job to the batch system. UC7 describes the start of applications, deployed using one of the resource management systems.

At this point, the deployment of the heterogeneous application components is performed on different resources, and the application is started. 
Optionally, the configuration of a monitoring platform can be additionally performed if such mechanism is requested to the Orchestrator. 
First, for each allocated Execution Platform (EP), one or more different Monitoring Exporters (i.e. a monitoring probes)
are registered and configured within monitoring agent. 
Then, a similar exporter registration and configuration process is conducted for each application component that requires a specialized exporter.

### UC7: Start application

UC7 is accompanying UC6 by describing in details the start of batch applications, i.e. applications that take an input, process it and give the results, unlike the services (e.g. web servers, REST APIs), which start right after deployment and run continuously. 
Furthermore, these applications can be deployed once and executed several times with different inputs.

When a job is submitted to the batch system, it does not start immediately, but it is firstly enqueued to specified or default queue. 
It starts, once the job's turn comes in the queue, and it leaves the queue for its execution. 
It may take some time depending on the resources' availability in the queue.

During the deployment (or redeployment), when resource management system is selected as an execution platform, the Orchestrator submits a job and monitors its state, whether it is running or finished. 
When the job is finished, the Orchestrator determines whether the execution was successful or failed. 
The deployment terminates when the execution is failed, otherwise the deployment continues with the next application component.

In this case of HPC scheduling, the precise start time for job execution (after being dequeued) is unpredictable, as it depends on the eventual availability of the requested resources. 
Therefore, job monitoring must start at queue time, as it is requested by the orchestrator to the Monitoring System, which, at this point, starts collecting statistics describing the application job status. 
From then on, the Orchestrator can initiate the collection of metrics for its purposes (e.g., to check application job health).

### UC8: Monitor runtime

The Monitor component collects system statistics on an ongoing basis. On each
host (whether physical or virtual) there are one or more exporters (i.e. probes) 
that interact with the Monitoring component and reports back to it some standard (but also specialized) statistics 
about their target, which could be either the execution platform or the application component. 
Statistics are usually collected on each target by reading various counters and registers 
that hold updated system statistics. 
Periodically, Monitoring collects the statistics from all the registered exporters and stores them into its internal database for further inspection, upon the reception of queries requested from external clients.
High level reports on standard statistics are available to be consulted by AOEs in the Monitoring Dashboard that is
accessible from the SODALITE IDE. 
Some monitoring alerts could also be triggered by the Monitoring Alert Manager to the Deployment Refactor, 
for those situations where a condition (defined within a dynamic alerting rule) holds on concrete monitoring stats.
In such cases, the Deployment Refactor component could make placement decisions based on the resource usage. 
In other cases it may be desirable to inspect some specific non-standard monitoring figures in order to isolate the cause of some
observed anomaly. In this case, the AOE can request such a report by using the Monitoring Dashboard component, 
which, in turn, creates and issues to the Monitoring component all the queries required to create the report.
Using the results to those queries returned by Monitoring, the dashboard presents the aggregated report to the AOE.

### UC9: Identify Refactoring Options

The Deployment Refactorer is initialized with the IaC models for the initial deployment, the initial set of refactoring options, 
and application goals. It uses the Node Managers of each of the nodes in the application topology to manage the resources in those nodes. 
The node resource management is based on the node level goals derived from the application goals. Via the Monitoring Alert Manager, 
the Deployment Refactorer is eventually notified when any of the defined application goals are violated. Upon such circumstances, 
the Deployment Refactorer tries to find a new deployment model for the application that can resolve the detected application goal violations. 
If a new deployment model cannot be found, the Application Ops Expert is alerted. The new deployment is enacted via the Deployment
Preparation component. The Deployment Refactorer also may reassign node-level goals as necessary. 
The Refactoring Option Discoverer can find the new refactoring options as well as the changes to the existing refactoring options 
using patterns and anti-patterns (bugs). It uses the Semantic Reasoner for this purpose. Both the Deployment Refactorer and the Node Manager 
use the Monitoring Agent to collect data to determine the impacts of the refactoring decisions and to update the predictive models used
for refactoring option selection and node resource allocation, respectively.

### UC10: Execute Partial Redeployment

A redeployment (or deployment updates) is an act of modifying application topology or application parameters at runtime. 
The partial redeployment refers to updates of only affected components of application, i.e. those components that require modifications during the runtime of application as oppose to modification the whole application topology. 
As an example, a certain application component needs the updates of the container image or it requires scaling to improve the performance. 
Another example might be the changes in application topologies, when new components are introduced or old components need to be removed.

In SODALITE, partial redeployment can be triggered via the SODALITE IDE when the Application Ops Expert manually changes a AADM or he/she confirms one of the Refactoring Options suggested by Refactorer as described in UC9. 
Refactorer may also be configured to automatically request a redeployment if it detects any Refactoring Options at runtime. 
In both cases, a reference to a particular Blueprint ID, which is obtained after execution of UC6, should be used.

When a redeployment is triggered manually by the Application Ops Expert, the SODALITE IDE then requests IaC Blueprint Builder to register a new CSAR from the updated AADM and to redeploy the blueprint in the Orchestrator. 
After that, SODALITE IDE directly requests the Orchestrator to start the deployment updates and it receives a Session ID to monitor the redeployment progress.

Alternatively, when a redeployment is triggered automatically by Refactorer, it creates a CSAR, which reflects the updated deployment, and updates the blueprint in the Orchestrator. 
Similarly, it then directly requests the Orchestrator to start the deployment updates and receives Session ID to monitor the redeployment progress.

At this point, the deployment updates are executed by the Orchestrator. 
The Orchestrator derives the difference between current and updated deployments and applies adaptation actions until the current state of deployment becomes the updated state. 
Such adaptation actions are performed on the Execution Platforms used for the redeployment. 
If the selected platform is IaaS Cloud or Kubernetes, the actions that might be performed are the following:

  - any form of scaling (in/out/up/down),
  - migration to another Execution Platform,
  - deployment of the new application components introduced by the Application Ops Expert and removal of current components.

It should be notes that Kubernetes, being itself an orchestration platform, is enforcing partial redeployment. 
For what concerns resource management systems (common in HPC), these Execution Platforms lack flexibility in scaling at runtime, hence the scaling actions are not present as possible adaptation actions; however, all the other actions can be executed as well (migration, deployment and removal of components).

#### TODO: Sync with deployment updates from xopera

#### TODO: Sync with refactorer