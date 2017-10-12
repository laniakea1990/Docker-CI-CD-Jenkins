### 什么是Pipeline

16年4月20日，历经Alpha\(2/29\)，Beta\(3/24\)，RC\(4/7\)3个版本的迭代，Jenkins 2.0 终于正式发布。这也是Jenkins面世11年以来的首次大版本升级。

Pipeline as Code是Jenkins  2.0的精髓所在，是帮助Jenkins实现CI\(Continuous Integration\)到CD\(Continuous Delivery\)转变的关键推手。所谓Pipeline，简单来说，就是一套运行于Jenkins上的工作流框架，将原本独立运行于单个或者多个节点的任务连接起来，实现单个任务难以完成的复杂发布流程。Pipeline的实现方式是一套Groovy DSL，任何发布流程都可以表述为一段Groovy脚本，并且Jenkins支持从代码库直接读取脚本（Jenkinsfile），从而实现了Pipeline as Code的理念。

Pipeline的几个基本概念：

* Stage: 阶段，一个Pipeline可以划分为若干个Stage，每个Stage代表一组操作。注意，Stage是一个逻辑分组的概念，可以跨多个Node。

* Node: 节点，一个Node就是一个Jenkins节点，或者是Master，或者是Agent，是执行Step的具体运行期环境。

* Step: 步骤，Step是最基本的操作单元，小到创建一个目录，大到构建一个Docker镜像，由各类Jenkins Plugin提供

![](/assets/import9.png)

### **创建Pipeline**

Pipeline脚本是用Groovy写的，可以通过以下任一方式创建基本Pipeline：

* pipeline script：直接在Web UI的script输入框里面输入pipeline script语句即可，参考说明可以点击输入框下边的Pipeline Syntax，里面有很多示例操作说明，非常好用。

* pipeline script from SCM：需要配置SCM代码存储Git地址或SVN地址，指定script文件Jenkinsfile所在路径，每次构建job会自动去指定的目录执行script文件

以上两种方法定义Pipeline的语法都是一样的。

基于前面安装的Jenkins master，进入jenkins主页：[http://192.168.139.132:8088/](http://192.168.139.132:8088/)，点击左边菜单的新建选项：

![](/assets/import6.png)

选择"Pipeline"类型并输入项目名：

![](/assets/import5.png)

进入项目的配置页面，首先配置项目的自动触发：

##### ![](/assets/import7.png)

##### **在Web UI中定义Pipeline**

在Pipeline配置页面的文本区域内，输入Pipeline script：

##### ![](/assets/import10.png)

##### **在SCM中定义pipeline**

复杂的Pipeline难以在Pipeline配置页面的文本区域内进行写入和维护。为了解决这一问题，jenkins Pipeline支持在文本编辑器中编写脚本文件jenkinsFile，Jenkins可以通过从SCM选项的控件中加载Pipeline脚本。

选择SCM选项中的Pipeline脚本后，不要在Jenkins UI中输入任何Groovy代码; 只需指定要检索的Pipeline脚本的路径。更新指定的存储库时，只要Pipeline配置了SCM轮询触发器，就会触发一个新构建。

### ![](/assets/import8.png)Jenkinsfile样例

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

### Pipeline语法

Pipeline最基本的部分是“step”。基本上，step告诉Jenkins 要做什么，并且作为Declarative Pipeline和Scripted Pipeline语法的基本构建块。

Pipeline支持两种语法：Declarative Pipeline（在Pipeline 2.5中引入，结构化方式）和Scripted Pipeline，两者都支持建立连续输送的Pipeline。

### 



