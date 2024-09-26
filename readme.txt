---- spring boot - vault - mongodb - integration and setup using Docker ----

STEP-1 : start vault server using hashicorp/vault:latest docker image
docker pull hashicorp/vault:latest
docker run --cap-add=IPC_LOCK -d -p 8200:8200 hashicorp/vault:latest

access Vault http://localhost:8200
provide Root Token - <get from docker container logs> 
create secrets using UI
e.g. 
path sectionone

key 					Value
mongo.host localhost
mongo.port 27017
mongo.database sectionone

save your secret.


STEP-2 : start mongodb server using mongo docker image
docker pull mongo
docker run -d -p 27017:27017 mongo


docker ps
you should get 2 containers.


STEP-3 : MongoDBCompass connect with mongodb server 
open mongocompass - click on create connection and connect with mongodb server
create 
	database(same as per your vault value) e.g. sectionone 
	collectionname(same as per your document model collection) e.g. vaultusers





STEP-3 : follow video and create spring boot application
dependencies
	- spring-boot-starter-web
	- spring-boot-starter-data-mongodb
	- spring-cloud-starter-vault-config
	
application.properties - vault & mongodb server config
#######################
spring.application.name=sectionone
spring.cloud.vault.kv.enabled=true
spring.cloud.vault.authentication=TOKEN
spring.cloud.vault.token=your-vault-token-value
spring.cloud.vault.scheme=http
spring.cloud.vault.host=127.0.0.1
spring.cloud.vault.port=8200
spring.config.import= vault://

spring.data.mongodb.uri=mongodb://${mongo.host}:${mongo.port}/${mongo.database}
#######################

Sample spring boot app model class.
@Document(collection = "vaultusers")
public class User {
    @Id
    private String id;
    private String name;
    private Integer age;
	// getter setter
}


STEP-4 : run your application and you should see application first access the vault secret
and connect with mongo databse.
now post user data using postman and data should be inserted into mongodb.
POST - http://localhost:8080/api/createuser
{
  "name": "SecondUser",
  "age": 35
}



(optional) - 
docker exec - vault kv get command to view secrets
export VAULT_ADDR='http://127.0.0.1:8200'
vault login your-vault-token-value
vault kv get secret/<project-name>
vault kv get secret/sectionone
you will get below output
Key                       Value
---                       -----
your-key				your-secret-values	



