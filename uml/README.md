# UML diagrams

SODALITE UML diagrams reflect the models identified 

## UML tools

SODALITE UML diagrams are created using the [PlantUML](http://plantuml.com/) tool and can be viewed/edited using these tools:

*   Online tool: [https://www.planttext.com](https://www.planttext.com/) (copy/paste UML code) 
*   Offline tool: [http://plantuml.com/download](http://plantuml.com/download) (copy/paste UML code)
*   JetBrains IDEs (e.g. IntelliJ):  [https://plugins.jetbrains.com/plugin/7017-plantuml-integration](https://plugins.jetbrains.com/plugin/7017-plantuml-integration)

SODALITE-EU project uses PlantUML to define different UML concepts such as: 
* Use case diagrams
* Sequence diagrams
* Component diagrams

The consortium uses the same tools for definition of simple [mindmaps](https://en.wikipedia.org/wiki/Mind_map).

An example of the PlantUML UML use case diagram definition is presented here.

## SODALITE Use Case Diagram 

Link to diagram: [SODALITE Use Case Diagram](https://www.planttext.com/?text=fLTVRzis47yUFyMzjGFcTcwIRXKAeuwTkG6QsOlR5GFwmacKIuGY1L9QucxzxZjI54N9Sj9iVaWY_lxmT7VaEMzCHNLL5u8GmV8AAWMQxpvNa77DqeehIOYvuRAacXPG6vPIm-1l0lWvf-dDJgjQPWibb8PtLdWkQjQWIcTCDyYIwfkje1LRQRedO-BNh-6ASWcp6Vp1X52tAysO71hTmt8_vbghssRBSu4RD8jffNGSpPzzw1SbgrzfmM2XQisP9f7g8CYtTK7T_YibClALuEV9akLSCfiSiDann6QEofrWi5qlv-yktrzWnhvx-c4n1S-TbwNWARNv_IINh1HgNp1PmPNAc809VDoSV0_KmDE67z4xikDQehD-0LicS5-uPWRV8sKcjccm00NL2QoOP1eh0vTq0IbQEjbf8sj1n2narvti57vdckTx9uhupIek1kOPR3JBECOrbnbcMDlSm7czCx7ES-OspuvX2S7Z9LpSiRJ663TQ_SKDPe7BtG-zn3NkCkUxMhiyEVkpnjxBOtKdTPj6Ggjos_u4Mtisubh5fWwCiA3x6QieENOFNDUouWMBLHxoID2yWiiCOx4flMOvjRs7GSAwjCucDcXv4SspWjdqnovX62Ed0aspBbSZcMbeCQjp2qrhY-qgzvxUAQcAVLp2XeCKosW9eOJJ5tXmowutFqbRC1jumGtjNjAtAt835OazNkZTwOa_BgCTxw33Xf-UmeL1DzlnlzLKy6eFYyzukQKLzYuYASO6wpokkd7TzL7f6w5e5c_IMdcdWT4wZqtGexNeqd9cUmovL8YzJyUNmUrWzrxsYtSQEj1BMsKaf39RHiLiDto95UGY2wrvMT0Twvr5XtkuGPtwQ--Yq-2Qpv0aFXthqc3dbKnN2SHt9TeY3L44IT8MPOoAp1PstBP4urIFMcO8JhVkRO2jNmAHEKcsYEBBmHNFCi5kgMu8VhbR9Sb25KKjstSSy7jWh1hmOcYr-G0BKHji58VXit_4pOIgiyRP_aMIjvk5UyJV96cEgmrZWzCqLxfmc5rk5_eLmV1A9R434b-4iD1bZDrLJDfhyVdENVU2Vt5YWbl2f2ud_ypwExZ8Hf7kJKPXSYHRBJbAqa4THZWaVfyM3nMETpu03tq3vFF2NVvKZWqbcWFydBhlGuQ4NeR9PFQD8tgW6Pl70yhOx1rG-kDs01wVgGFYuwVZy6N69k60CpBt1em71jkHe8yChfZzw8joBFNmR7RiQhIt4fUfg3Cscn7INKfnUumDnY7p_qoJWtm_C47TL-ReghNFtoJiYNnf3oOjUNZ_PxF7rcMGvyVthUs5SCn7EhAFtz-FVUxztJZW_lSgzut6ZbsFGTumcTb_xVu5)


```
@startuml

left to right direction

skinparam usecase {
    BackgroundColor LightBlue
    BorderColor DarkSlateGray

    BackgroundColor<< Main >> YellowGreen
    BorderColor<< Main >> YellowGreen
    
    ArrowColor Black
    ActorBorderColor black
    ActorFontName Courier

    ActorBackgroundColor<< Human >> Gold
}



'Define use cases
rectangle SODALITE {
	(UC1 Define Application\nDeployment Model - WP3) as (DefineApplicationDeploymentModelWP3)
	(UC2 Select Resources - WP3) as (SelectResourcesWP3)
	(UC3 Generate IaC code - WP4) as (GenerateIaCcodeWP4)
	(UC4 Verify IaC - WP4) as (VerifyIaCWP4)
	(UC5 Predict And Correct Bugs - WP4) as (PredictAndCorrectBugsWP4)
	(UC6 Execute Provisioning,\nDeployment And Configuration - WP5) as (ExecuteProvisioningDeploymentAndConfigurationWP5)
	(UC7 Start Application - WP5) as (StartApplicationWP5)
	(UC8 Monitor Runtime - WP5) as (MonitorRuntimeWP5)
	(UC9 Identify Refactoring Options - WP5) as (IdentifyRefactoringOptionsWP5)
	(UC10 Execute Partial Redeployment - WP5) as (ExecutePartialRedeploymentWP5)
	(UC11 Define IaC Bugs Taxonomy - WP4) as (DefineIaCBugsTaxonomyWP4)
	(UC12 Map Resources\nAnd Optimisations - WP3) as (MapResourcesAndOptimisationsWP3)
	(UC13 Model Resources - WP3) as (ModelResourcesWP3)
	(UC14 Estimate Quality Characteristics\n Of Applications And Workload - WP3) as (EstimateQualityCharacteristicsOfApplicationsAndWorkloadWP4)
	(UC15 Statically Optimize Application\nAnd Deployment - WP4) as (StaticallyOptimizeApplicationandDeploymentWP4)
	(UC16 Build Runtime Images - WP4) as (BuildRuntimeImagesWP4)
}

'Define user actors
:Application Ops Expert: << Human >> as AppOpsExp
:Resource Expert: << Human >>as ResourceExp
:Quality Expert: << Human >>as QualityExp

'Define resource actors
:Resource: as Resource
:Storage Middleware: as StorageMiddleware
:Communication Middleware: as CommunicationMiddleware
:Middleware: as Middleware
:GPU Cluster: as GPUCluster
:Cloud: as Cloud
:HPC: as HPC
:Execution Platform: as ExecPlatform
:Application Component: as AppComponent

'Define extension/generalization
HPC -up-|> ExecPlatform
Cloud -up-|> ExecPlatform
GPUCluster -up-|> ExecPlatform

CommunicationMiddleware -up-|> Middleware
StorageMiddleware -up-|> Middleware

AppComponent -up-|> Resource
ExecPlatform -up-|> Resource
Middleware -up-|> Resource

'Definition of relations/actions
AppOpsExp --> (DefineApplicationDeploymentModelWP3)
AppOpsExp --> (SelectResourcesWP3)
AppOpsExp --> (GenerateIaCcodeWP4)
AppOpsExp --> (VerifyIaCWP4)
AppOpsExp --> (PredictAndCorrectBugsWP4)
AppOpsExp --> (ExecuteProvisioningDeploymentAndConfigurationWP5)
AppOpsExp --> (StartApplicationWP5)
AppOpsExp --> (MonitorRuntimeWP5)
AppOpsExp --> (IdentifyRefactoringOptionsWP5)
AppOpsExp --> (ExecutePartialRedeploymentWP5)
AppOpsExp -- (StaticallyOptimizeApplicationandDeploymentWP4)
(GenerateIaCcodeWP4) .> (BuildRuntimeImagesWP4): <<include>>

QualityExp --> (DefineIaCBugsTaxonomyWP4)
QualityExp --> (EstimateQualityCharacteristicsOfApplicationsAndWorkloadWP4)

AppOpsExp --> (MapResourcesAndOptimisationsWP3)    
ResourceExp    --> (MapResourcesAndOptimisationsWP3)
ResourceExp --> (ModelResourcesWP3)

(MapResourcesAndOptimisationsWP3) .> (StaticallyOptimizeApplicationandDeploymentWP4): <<include>>

(ExecuteProvisioningDeploymentAndConfigurationWP5) -- Resource
(MonitorRuntimeWP5) -- Resource
(IdentifyRefactoringOptionsWP5) -- Resource
(ExecutePartialRedeploymentWP5) -- Resource
(EstimateQualityCharacteristicsOfApplicationsAndWorkloadWP4) -- Resource
(StartApplicationWP5) -- Resource

@enduml
```
Other UML diagrams can be found within the UML submaps.
