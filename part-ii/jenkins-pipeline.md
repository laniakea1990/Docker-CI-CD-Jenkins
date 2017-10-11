### 什么是Pipeline

16年4月20日，历经Alpha\(2/29\)，Beta\(3/24\)，RC\(4/7\)3个版本的迭代，Jenkins 2.0 终于正式发布。这也是Jenkins面世11年以来的首次大版本升级。

Pipeline as Code是Jenkins  2.0的精髓所在，是帮助Jenkins实现CI\(Continuous Integration\)到CD\(Continuous Delivery\)转变的关键推手。所谓Pipeline，简单来说，就是一套运行于Jenkins上的工作流框架，将原本独立运行于单个或者多个节点的任务连接起来，实现单个任务难以完成的复杂发布流程。Pipeline的实现方式是一套Groovy DSL，任何发布流程都可以表述为一段Groovy脚本，并且Jenkins支持从代码库直接读取脚本（Jenkinsfile），从而实现了Pipeline as Code的理念。

Pipeline的几个基本概念：

* Stage: 阶段，一个Pipeline可以划分为若干个Stage，每个Stage代表一组操作。注意，Stage是一个逻辑分组的概念，可以跨多个Node。

* Node: 节点，一个Node就是一个Jenkins节点，或者是Master，或者是Agent，是执行Step的具体运行期环境。

* Step: 步骤，Step是最基本的操作单元，小到创建一个目录，大到构建一个Docker镜像，由各类Jenkins Plugin提供。

本节介绍Jenkins Pipeline的一些核心概念，并介绍在运行的Jenkins实例中定义和使用Pipelines的基础知识。

### **创建Pipeline**

基于前面安装的Jenkins master，进入jenkins主页：[http://192.168.139.132:8088/](http://192.168.139.132:8088/)，点击左边菜单的新建选项：

![](/assets/import6.png)

选择"Pipeline"类型并输入项目名：

![](/assets/import5.png)

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



