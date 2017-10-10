### Jenkinsfile样例

```
//push to private registry
node {
   stage('Check out source code') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/raiseking/docker-hello-world.git'
   }
   stage('Build and push docker image'){
	   docker.withRegistry('http://192.168.139.129:5000/') {
			docker.image('maven:3.5-alpine').inside('-v $HOME/.m2:/root/.m2') {
				// Run the maven build
				sh "mvn clean package"
			}
			def customImage = docker.build("192.168.139.129:5000/docker-cicd:${env.BUILD_ID}")
			customImage.push()
       }
   }

   stage('Deploy with docker image') {
      echo "Deploy artifact image to docker env."
	  docker.withRegistry('http://192.168.139.129:5000/') {
			sh "docker pull 192.168.139.129:5000/docker-cicd:${env.BUILD_ID}"
			sh "docker run -d -p 8888:8080 192.168.139.129:5000/docker-cicd:${env.BUILD_ID}"
       }
   }
}
```



