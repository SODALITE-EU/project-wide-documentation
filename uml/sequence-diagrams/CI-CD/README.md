# SODALITE CI/CD pipeline

## Continuous integration
SODALITE employs github flow for Continuous integration. New changes are applied to feature branches, unit-tested and 
submitted vi Pull requests to master/main branch. Changes are reviewed by human reviewer as well as automated tools 
(Codacy, Sonarcloud). After all checks are met, changes are merged. 

## Continuous delivery
SODALITE Continuous delivery is comprised of two stages, staging and production. The purpose of staging is to deploy
 component to SODALITE testbed for integration testing and final inspection, while production is supposed to publish
 docker images to dockerhub and deploy production version to SODALITE testbed.
 
Both parts of continuous delivery require code being published via Github (pre)release and are then triggered manually via SODALITE jenkins.