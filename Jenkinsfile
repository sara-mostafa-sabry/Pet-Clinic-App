pipeline {
    agent  any 
    
    stages {
    
        
        stage('Build image') {
            steps {
                    
                 sh "docker build -t petclinic:v$BUILD_NUMBER . "

            }
        }
        
        stage('Deploy on Dev')  {
             steps {
                 
		docker network create net-1
		
		docker run -d --name "db-dev" -p 5432:5432 -e POSTGRES_DB=petclinic -e POSTGRES_USER=petclinic -e POSTGRES_PASSWORD=petclinic 
		 -v /var/lib/docker/volumes/dev-vol:/var/lib/postgresql/data  --network net-1  postgres:15.2

		docker run -d -p 8070:8080 --name "java-app-dev"  --network net-1 -e SPRING.PROFILES.ACTIVE=postgres -e POSTGRES_URL=jdbc:postgresql://db-dev/petclinic
		 -e POSTGRES_USER=petclinic -e POSTGRES_PASSWORD=petclinic  petclinic:v$BUILD_NUMBER 
    
            }
        }
       
	 stage('Deploy on Prod')  {
             steps {

                docker run -d --name "db-prod" -p 5433:5432 -e POSTGRES_DB=petclinic -e POSTGRES_USER=petclinic -e POSTGRES_PASSWORD=petclinic
                 -v /var/lib/docker/volumes/prod-vol:/var/lib/postgresql/data  --network net-1  postgres:15.2

                docker run -d -p 8090:8080 --name "java-app-prod"  --network net-1 -e SPRING.PROFILES.ACTIVE=postgres -e POSTGRES_URL=jdbc:postgresql://db-prod/petclinic
                 -e POSTGRES_USER=petclinic -e POSTGRES_PASSWORD=petclinic  petclinic:v$BUILD_NUMBER

            }
        }
    }
}
