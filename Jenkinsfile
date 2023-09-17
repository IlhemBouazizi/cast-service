pipeline 
{
    agent any // Jenkins will be able to select all available agents
    stages 
    {
        stage('Pre-Build')
        { //we pass the built image to our docker hub account
            environment
            {
                DOCKER_PASS = credentials("DOCKER_PASS") // we retrieve  docker password from secret text called docker_hub_pass saved on jenkins
                DOCKER_ID = 'ilhemb'
                BRANCH_NAME = "${GIT_BRANCH.split("/")[1]}"
            }
            steps 
            {
                script 
                {
                    sh '''
                    echo "Pulling $BRANCH_NAME branch"

                    git branch
                    
                    docker login -u $DOCKER_ID -p $DOCKER_PASS
                    '''
                }
            }
        }
        stage('Build')
        { // docker build image stage
            steps {
                script 
                {
                    sh '''
                    echo ""
                    echo "-----  Build cast service"
                    docker image build webapp/cast-service/ -t cast-service:latest
                    docker tag cast-service ilhemb/cast-service:latest
                    docker image push ilhemb/cast-service:latest
                    '''
                }
            }
        }
        stage('Deploiement en dev')
        {
            environment
            {
                KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
                NAMESPACE = 'dev'
                NODEPORT = '30010'
                DB_IP = '10.43.60.0'
            }
            steps 
            {
                script 
                {
                    sh '''
                    cat $KUBECONFIG > k8s_config
                    helm upgrade --kubeconfig k8s_config --install cast-microservice-$NAMESPACE  helm/cast-microservice/ --values=helm/cast-microservice/values.yaml --set nameSpace="$NAMESPACE" --set cast.service.nodePort="$NODEPORT" --set castDB.service.clusterIP="$DB_IP"
                    '''
                }
            }
        }
        stage('Test deploiement en dev')
        {    
            environment
            {
                NODEPORT = '30010'
            }
            steps 
            {
                script 
                {
                    sh '''
                    sleep 60
                    curl "http://localhost:$NODEPORT/api/v1/casts/docs"
                    '''
                }
            }            
        }
        stage('Deploiement en qa')
        {
            environment
            {
                KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
                NAMESPACE = 'qa'
                NODEPORT = '30011'
                DB_IP = '10.43.60.1'
            }
            steps 
            {
                script 
                {
                    sh '''
                    cat $KUBECONFIG > k8s_config
                    helm upgrade --kubeconfig k8s_config --install cast-microservice-$NAMESPACE  helm/cast-microservice/ --values=helm/cast-microservice/values.yaml --set nameSpace="$NAMESPACE" --set cast.service.nodePort="$NODEPORT" --set castDB.service.clusterIP="$DB_IP"
                    '''
                }
            }
        }
        stage('Test deploiement en qa')
        {    
            environment
            {
                NODEPORT = '30011'
            }
            steps 
            {
                script 
                {
                    sh '''
                    sleep 60
                    curl "http://localhost:$NODEPORT/api/v1/casts/docs"
                    '''
                }
            }            
        }        
        stage('Deploiement en staging')
        {
            environment
            {
                KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
                NAMESPACE = 'staging'
                NODEPORT = '30012'
                DB_IP = '10.43.60.2'
            }
            steps 
            {
                script 
                {
                    sh '''
                    cat $KUBECONFIG > k8s_config
                    helm upgrade --kubeconfig k8s_config --install cast-microservice-$NAMESPACE  helm/cast-microservice/ --values=helm/cast-microservice/values.yaml --set nameSpace="$NAMESPACE" --set cast.service.nodePort="$NODEPORT" --set castDB.service.clusterIP="$DB_IP"
                    '''
                }
            }
        }
        stage('Test deploiement en staging')
        {    
            environment
            {
                NODEPORT = '30012'
            }
            steps 
            {
                script 
                {
                    sh '''
                    sleep 60
                    curl "http://localhost:$NODEPORT/api/v1/casts/docs"
                    '''
                }
            }            
        }        
        stage('Deploiement en prod')
        {
            input
            {
                message "Confirmer le deployment en prod"
            }
            environment
            {
                KUBECONFIG = credentials("config") // we retrieve  kubeconfig from secret file called config saved on jenkins
                NAMESPACE = 'prod'
                NODEPORT = '30013'
                DB_IP = '10.43.60.3'
            }
            steps 
            {
                script 
                {
                    sh '''
                    cat $KUBECONFIG > k8s_config
                    helm upgrade --kubeconfig k8s_config --install cast-microservice-$NAMESPACE  helm/cast-microservice/ --values=helm/cast-microservice/values.yaml --set nameSpace="$NAMESPACE" --set cast.service.nodePort="$NODEPORT" --set castDB.service.clusterIP="$DB_IP"
                    '''
                }
            }
        }
        stage('Test deploiement en prod')
        {    
            environment
            {
                NODEPORT = '30013'
            }
            steps 
            {
                script 
                {
                    sh '''
                    sleep 60
                    curl "http://localhost:$NODEPORT/api/v1/casts/docs"
                    '''
                }
            }            
        }        
    }
}