pipeline {
   agent none

   stages {
     stage('maven进行项目构建') {
        agent {
          docker {
            args '-v /root/.m2:/root/.m2'
            image 'maven:3-alpine'
          }
        }
       steps {
          sh label: '', script: 'mvn -gs setting.xml clean package -Dmaven.test.skip=true'
          sh 'pwd'
          sh 'ls'
          sh 'echo 666'
       }
     }

     stage('docker构建镜像') {
       agent any
       steps {
         sh 'docker build -t icoding-java-img .'
       }
     }


// 每个step里面必须有东西，全局agent none的时候，每个stage 都必须有自己的agent
     stage('docker swarm进行部署') {
       agent any
       steps {
          //人工确认
          //input id: 'Deployee-to-prod', message: '确定部署到生产环境？', ok: '部署', submitter: 'admin'
          //sh 'docker stack deploy -c docker-compose.yaml icodingapp'
          //邮件通知
          mail to: '1547153449@qq.com',
                       subject: "${env.JOB_NAME}-${env.BUILD_NUMBER}构建成功",
                       body: "项目 ${env.BRANCH_NAME}分支 构建成功 ${env.BUILD_URL}，"
       }
     }

//参数化构建
      stage('是否发布镜像') {
            agent any
            steps {
                withCredentials([usernamePassword(credentialsId: 'aliyundocker', passwordVariable: 'apwd', usernameVariable: 'user')]) {
                    // some block
                     sh 'docker login -u $user -p $apwd registry.cn-zhangjiakou.aliyuncs.com'
                     sh 'docker tag icoding-java-img registry.cn-zhangjiakou.aliyuncs.com/icodingdocker/icoding-java-img:v1.0'
                     sh 'docker push registry.cn-zhangjiakou.aliyuncs.com/icodingdocker/icoding-java-img:v1.0'
                }

            }
      }
   }



}
