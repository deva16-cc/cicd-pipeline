pipeline {

agent any

environment{

FRONTEND="deva1605/frontend"
BACKEND="deva1605/backend"

}

stages{

stage('Clone'){
steps{
git 'https://github.com/deva16-cc/cicd-pipeline.git'
}
}

stage('Code Quality'){
steps{
echo "Running code quality..."
}
}

stage('Test'){
steps{
echo "Running Tests..."
}
}

stage('Build Docker'){
steps{

sh 'docker build -t $FRONTEND frontend'
sh 'docker build -t $BACKEND backend'

}
}

stage('Push Docker'){
steps{

withCredentials([usernamePassword(credentialsId:'dockerhub',
usernameVariable:'USER',
passwordVariable:'PASS')]){

sh 'echo $PASS | docker login -u $USER --password-stdin'

}

sh 'docker push $FRONTEND'
sh 'docker push $BACKEND'

}
}

stage('Deploy Dev'){
steps{

sh 'docker compose up -d'

}
}

stage('Health Check'){
steps{

sh 'curl http://localhost:5000/health'

}
}

stage('Production Approval'){
steps{

input "Deploy Production?"

}
}

stage('Production Deploy'){
steps{

echo "Deploying Production"

}
}

}

post{

failure{

mail to:'baskardeva7@gmail.com',
subject:'Pipeline Failed',
body:'Check Jenkins'

}

success{

archiveArtifacts '**/*'

}

}

}
