# Boundless Service Broker

This is an implementation of [Cloud Foundry Service Broker] (https://docs.cloudfoundry.org/services/api.html) to broker and run instances of Boundless OpenGeo Servers on Cloud Foundry. 

# Prerequisites
* The PCF install with MySQL Service

# Steps
* Build the service broker jar file using mvn clean install (use jdk 1.8)
```
# Ignore running maven test as it would require bosh-lite to run local tests for cf-java-client
mvn -Dmaven.test.skip=true clean install && cf push
```
* Create mysql-service (of type : mysql) in the org/space where the Service Broker would be deployed
```
cf create-service p-mysql 100mb-dev mysql-service
```
* Edit the manifest.yml with correct credentials to CF.
* Deploy the service broker jar file using the manifest.yml and `cf push`
```
```
* Register service broker on CF
```
# The below code uses default testuser:testuser as username & password for authentication based on manifest.yml entries
cf create-service-broker boundless-service-broker testuser testuser https://boundless-service-broker.<domain>
```
* Open up access to service plans
```
cf enable-service-access boundless-suite
```
* Look for service offerings in marketplace
```
cf marketplace

# output
service          plans                               description
boundless-suite  geolite                             Open Source Server for Sharing Geospatial Data.
p-mysql          100mb-dev                           MySQL service for application development and testing
p-rabbitmq       standard                            RabbitMQ is a robust and scalable high-performance multi-protocol messaging broker.
p-redis          shared-vm, dedicated-vm             Redis service to provide a key-value store
```
* Edit the appParamPayload.json to edit the org, space, app names or routes, memory, instances etc.
* Create a Boundless GeoLite service instance on CF by passing the appParamPayload.json to configure the app details. If no json file is provided, default configurations would be used to bring up one instance of geoserver with some random generated names and routes in the same org/space where the service instance is being created.

```
#cf cs boundless-suite basic opengeo-test  -c appParamPayload.json
cf create-service boundless-suite basic opengeo-test  -c appParamPayload.json
# Check status of apps (if they are in same org/space)
cf apps
```
* Update Boundless OpenGeo service instance on CF by passing an updated appParamPayload.json to re-configure memory/instances  of app instances
```
cf update-service opengeo-test  -c appParamPayload.json
```
* Delete Boundless service instance on CF 
```
cf delete-service -f opengeo-test  
# Check status of apps (if they are in same org/space)
cf apps
```
* Note: On update to service broker code, update the broker against CF to pick changes to service or plans
```
# Rebuild the service broker jar file & push to cf
mvn -Dmaven.test.skip=true clean install && cf push
cf update-service-broker boundless-service-broker testuser testuser https://boundless-service-broker.<domain>
```
