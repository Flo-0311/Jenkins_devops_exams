pipeline {
    agent any

    environment {
        docker_id = "fm92962"
        movie = "jenkins_devops_exams-movie_service"
        cast = "jenkins_devops_exams-cast_service"
        nginx = "nginx-proxy"
        docker_tag = "v.${BUILD_ID}"
        docker_pass = credentials("Docker_hub_pass")
    }

    stages {

        stage('Docker build cast'){

            steps{
                script {
                    sh '''
                    docker build -t ${docker_id}/${cast}:${docker_tag} ./cast-service
                    docker login -u ${docker_id} -p ${docker_pass}
                    docker push ${docker_id}/${cast}:${docker_tag}
                    sleep 5
                    '''
                }
            }

        }

        stage('Docker build movie'){

            steps{
                script {

                    sh '''
                    docker build -t ${docker_id}/${movie}:${docker_tag} ./movie-service
                    docker login -u ${docker_id} -p ${docker_pass}
                    docker push ${docker_id}/${movie}:${docker_tag}
                    sleep 5
                    '''
                }

            }
        }

        stage('Docker build nginx'){

            steps {
                script {

                    sh '''
                    docker build -t ${docker_id}/${nginx}:${docker_tag} ./nginx
                    docker login -u ${docker_id} -p ${docker_pass}
                    docker push ${docker_id}/${nginx}:${docker_tag}
                    sleep 5
                    '''
                }

            }
        }

        stage('Deployment in dev'){

            environment {
                KUBECONFIG = credentials("config")
            }

            steps {
                script {

                    sh '''
                    rm -Rf .kube
                    mkdir .kube
                    cat $KUBECONFIG > .kube/config
                    sleep 5
                    '''

                    sh '''
                    cp ./k3s/cast-service/statefulset.yaml statefulset.yaml
                    cat statefulset.yaml
                    sed -i "s/name: cast-pv/name: cast-pv-dev/g" statefulset.yaml
                    sed -i "s/volumeName: cast-pv/volumeName: cast-pv-dev/g" statefulset.yaml
                    cat statefulset.yaml
                    kubectl apply -f ./statefulset.yaml -n dev
                    cp ./k3s/movie-service/statefulset.yaml statefulset.yaml
                    cat statefulset.yaml
                    sed -i "s/name: movie-pv/name: movie-pv-dev/g" statefulset.yaml
                    sed -i "s/volumeName: movie-pv/volumeName: movie-pv-dev/g" statefulset.yaml
                    cat statefulset.yaml
                    kubectl apply -f ./statefulset.yaml -n dev
                    sleep 120
                    '''

                    sh '''
                    cp ./chart-cast/values.yaml values.yaml
                    cat values.yaml
                    sed -i "s|^  tag:.*|  tag: \"${docker_tag}\"|" values.yaml
                    cat values.yaml
                    helm upgrade --install cast ./chart-cast --values=values.yaml -n dev
                    sleep 5
                    '''

                    sh '''
                    cp ./chart-movie/values.yaml values.yaml
                    cat values.yaml
                    sed -i "s|^ tag:.*|  tag: \"${docker_tag}\"|" values.yaml
                    cat values.yaml
                    helm upgrade --install movie ./chart-movie --values=values.yaml -n dev
                    sleep 5
                    '''

                    sh '''
                    cp ./k3s/nginx/deployment.yaml deployment.yaml
                    cat deployment.yaml
                    sed -i "s|fm92962/nginx-proxy:.*|fm92962/nginx-proxy:${docker_tag}|g" deployment.yaml
                    cat deployment.yaml
                    kubectl apply -f deployment.yaml -n dev
                    sleep 5
                    '''

                }
            }
        }

        stage('Deployment in qa'){

            environment {
                KUBECONFIG = credentials("config")
            }

            steps {
                script {

                    sh '''
                    rm -Rf .kube
                    mkdir .kube
                    cat $KUBECONFIG > .kube/config
                    sleep 5
                    '''

                    sh '''
                    cp ./k3s/cast-service/statefulset.yaml statefulset.yaml
                    cat statefulset.yaml
                    sed -i "s/name: cast-pv/name: cast-pv-qa/g" statefulset.yaml
                    sed -i "s/volumeName: cast-pv/volumeName: cast-pv-qa/g" statefulset.yaml
                    cat statefulset.yaml
                    kubectl apply -f ./statefulset.yaml -n qa
                    cp ./k3s/movie-service/statefulset.yaml statefulset.yaml
                    cat statefulset.yaml
                    sed -i "s/name: movie-pv/name: movie-pv-qa/g" statefulset.yaml
                    sed -i "s/volumeName: movie-pv/volumeName: movie-pv-qa/g" statefulset.yaml
                    cat statefulset.yaml
                    kubectl apply -f ./statefulset.yaml -n qa
                    sleep 120
                    '''

                    sh '''
                    cp ./chart-cast/values.yaml values.yaml
                    cat values.yaml
                    sed -i "s|^  tag:.*|  tag: \"${docker_tag}\"|" values.yaml
                    cat values.yaml
                    helm upgrade --install cast ./chart-cast --values=values.yaml -n qa
                    sleep 5
                    '''

                    sh '''
                    cp ./chart-movie/values.yaml values.yaml
                    cat values.yaml
                    sed -i "s|^ tag:.*|  tag: \"${docker_tag}\"|" values.yaml
                    cat values.yaml
                    helm upgrade --install movie ./chart-movie --values=values.yaml -n qa
                    sleep 5
                    '''

                    sh '''
                    cp ./k3s/nginx/deployment.yaml deployment.yaml
                    cat deployment.yaml
                    sed -i "s|fm92962/nginx-proxy:.*|fm92962/nginx-proxy:${docker_tag}|g" deployment.yaml
                    cat deployment.yaml
                    kubectl apply -f deployment.yaml -n qa
                    sleep 5
                    '''

                }
            }
        }

        stage('Deployment in staging'){

            environment {
                KUBECONFIG = credentials("config")
            }

            steps {
                script {

                    sh '''
                    rm -Rf .kube
                    mkdir .kube
                    cat $KUBECONFIG > .kube/config
                    sleep 5
                    '''

                    sh '''
                    cp ./k3s/cast-service/statefulset.yaml statefulset.yaml
                    cat statefulset.yaml
                    sed -i "s/name: cast-pv/name: cast-pv-staging/g" statefulset.yaml
                    sed -i "s/volumeName: cast-pv/volumeName: cast-pv-staging/g" statefulset.yaml
                    cat statefulset.yaml
                    kubectl apply -f ./statefulset.yaml -n staging
                    cp ./k3s/movie-service/statefulset.yaml statefulset.yaml
                    cat statefulset.yaml
                    sed -i "s/name: movie-pv/name: movie-pv-staging/g" statefulset.yaml
                    sed -i "s/volumeName: movie-pv/volumeName: movie-pv-staging/g" statefulset.yaml
                    cat statefulset.yaml
                    kubectl apply -f ./statefulset.yaml -n staging
                    sleep 120
                    '''

                    sh '''
                    cp ./chart-cast/values.yaml values.yaml
                    cat values.yaml
                    sed -i "s|^  tag:.*|  tag: \"${docker_tag}\"|" values.yaml
                    cat values.yaml
                    helm upgrade --install cast ./chart-cast --values=values.yaml -n staging
                    sleep 5
                    '''

                    sh '''
                    cp ./chart-movie/values.yaml values.yaml
                    cat values.yaml
                    sed -i "s|^ tag:.*|  tag: \"${docker_tag}\"|" values.yaml
                    cat values.yaml
                    helm upgrade --install movie ./chart-movie --values=values.yaml -n staging
                    sleep 5
                    '''

                    sh '''
                    cp ./k3s/nginx/deployment.yaml deployment.yaml
                    cat deployment.yaml
                    sed -i "s|fm92962/nginx-proxy:.*|fm92962/nginx-proxy:${docker_tag}|g" deployment.yaml
                    cat deployment.yaml
                    kubectl apply -f deployment.yaml -n staging
                    sleep 5
                    '''

                }
            }
        }

        stage('Waiting for Manually prod deployment'){

             input{
                message "Press Ok to continue deploy in production"
                ok "Starting deploying in production"
                submitterParameter "username"
                parameters {
                    string(name:'username', defaultValue: 'user', description: 'Username of the user pressing Ok')
                }
            }

        }

        stage('Deploying in prod'){

            environment {
                KUBECONFIG = credentials("config")
            }

            when {
                branch 'master'
            }

            steps {
                script{
                     sh '''
                    rm -Rf .kube
                    mkdir .kube
                    cat $KUBECONFIG > .kube/config
                    sleep 5
                    '''

                    sh '''
                    cp ./k3s/cast-service/statefulset.yaml statefulset.yaml
                    cat statefulset.yaml
                    sed -i "s/name: cast-pv/name: cast-pv-prod/g" statefulset.yaml
                    sed -i "s/volumeName: cast-pv/volumeName: cast-pv-prod/g" statefulset.yaml
                    cat statefulset.yaml
                    kubectl apply -f ./statefulset.yaml -n prod
                    cp ./k3s/movie-service/statefulset.yaml statefulset.yaml
                    cat statefulset.yaml
                    sed -i "s/name: movie-pv/name: movie-pv-prod/g" statefulset.yaml
                    sed -i "s/volumeName: movie-pv/volumeName: movie-pv-prod/g" statefulset.yaml
                    cat statefulset.yaml
                    kubectl apply -f ./statefulset.yaml -n prod
                    sleep 120
                    '''

                    sh '''
                    cp ./chart-cast/values.yaml values.yaml
                    cat values.yaml
                    sed -i "s|^  tag:.*|  tag: \"${docker_tag}\"|" values.yaml
                    cat values.yaml
                    helm upgrade --install cast ./chart-cast --values=values.yaml -n prod
                    sleep 5
                    '''

                    sh '''
                    cp ./chart-movie/values.yaml values.yaml
                    cat values.yaml
                    sed -i "s|^ tag:.*|  tag: \"${docker_tag}\"|" values.yaml
                    cat values.yaml
                    helm upgrade --install movie ./chart-movie --values=values.yaml -n dev prod
                    sleep 5
                    '''

                    sh '''
                    cp ./k3s/nginx/deployment.yaml deployment.yaml
                    cat deployment.yaml
                    sed -i "s|fm92962/nginx-proxy:.*|fm92962/nginx-proxy:${docker_tag}|g" deployment.yaml
                    cat deployment.yaml
                    kubectl apply -f deployment.yaml -n prod
                    sleep 5
                    '''
                }
            }
        }
    }

    post {
        failure {
            echo "This will run if the job failed"
            mail to: "florian.munkelt@outlook.de",
            subject: "Pipeline-Job ${env.JOB_NAME} - Build # ${env.BUILD_ID} has failed", //subject form the mail
            body: "For more info on the pipeline failure, check out the console output at ${env.BUILD_URL}" //Content from the mail
            }

        success {
            echo "This will run if the job success"
            mail to: "florian.munkelt@outlook.de",
            subject: "Pipeline-Job ${env.JOB_NAME} - Build # ${env.BUILD_ID} has success",
            body: "For more info on the pipeline , check out the console output at ${env.BUILD_URL}"
            }
        }

}