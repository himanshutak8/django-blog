pipeline {
        agent any
        stages {
                stage('Code Checkout') {
                   steps{
                        git 'https://github.com/himanshutak8/django-blog.git'
                   }
                }

                stage('sample unit case testing') {
                   steps{
                        script {
                            sh '''
                                #!/bin/bash
                                if sudo docker images | grep himanshutak8/pytest
                                then
                                       echo "Image Available 111"
                                else
                                       sudo docker pull himanshutak8/pytest:v1
                                fi 
                                if sudo docker ps | grep test_env
                                then
                                        echo "already running"
                                        
                                        sudo docker exec test_env sed -i "s/ALLOWED_HOSTS = [[]]/ALLOWED_HOSTS = ['*',]/g" /code/mysite/settings.py
                                        
                                        sudo docker exec test_env python3.6 /code/manage.py runserver 0.0.0.0:8000 &
                                        if sudo docker exec test_env pytest -v  /unit_testing/test_web.py
                                        then
                                            exit 0
                                        else
                                            exit 1
					    echo "Test Cases Failed............!!!!!!!!!!!"
                                        fi
                                else
                                        sudo docker run -dit -p 8086:8000 -v /var/lib/jenkins/workspace/seed_job_pipeline/:/code --name test_env himanshutak8/pytest:v1
                                        sudo docker exec test_env sed -i "s/ALLOWED_HOSTS = [[]]/ALLOWED_HOSTS = ['*',]/g" /code/mysite/settings.py
                                        
                                        sudo docker exec test_env python3.6 /code/manage.py runserver 0.0.0.0:8000 &
                                        if sudo docker exec test_env pytest -v  /unit_testing/test_web.py
                                        then
                                            exit 0
                                        else
					    exit 1
                                            echo "Test Cases Failed............!!!!!!!!!!!"
                                        fi
                                fi
                            '''
                        }
                   }
                }


                stage('App Deployment on Server') {
                   steps{
                         script {
                                sh '''
                                        #!/bin/bash
                                        if sudo docker images | grep himanshutak8/prod
                                        then
                                                echo "already pulled image"
                                        else
                                                sudo docker pull himanshutak8/prod:v1
                                        fi
                                        if  sudo docker ps -a | grep prod_server | grep Exited
                                        then
                                            sudo docker rm -f prod_server
                                        fi
                                        if sudo docker ps | grep prod_server
                                        then
                                                echo "already running new changes"
                                                
                                                sudo docker exec prod_server sed -i "s/ALLOWED_HOSTS = [[]]/ALLOWED_HOSTS = ['*',]/g" /code/mysite/settings.py
                                                
                                                sudo docker exec prod_server python3.6 /code/manage.py runserver 0.0.0.0:8000 &
                                        else
                                               sudo docker run -dit -p 9999:8000 -v /var/lib/jenkins/workspace/seed_job_pipeline/:/code --name prod_server himanshutak8/prod:v1
                                                
                                                sudo docker exec prod_server sed -i "s/ALLOWED_HOSTS = [[]]/ALLOWED_HOSTS = ['*',]/g" /code/mysite/settings.py
                                                
                                                sudo docker exec prod_server python3.6 /code/manage.py runserver 0.0.0.0:8000 &
                                        fi
                                '''
                                }
                   }
                }
        }
}
