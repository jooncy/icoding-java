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
          //人工确认，当前镜像部署有问题，所以注释掉了
          //input id: 'Deployee-to-prod', message: '确定部署到生产环境？', ok: '部署', submitter: 'liujunxi'
          //sh 'docker stack deploy -c docker-compose.yaml icodingapp'
          //邮件通知
          mail to: '1547153449@qq.com',
             subject: "项目 ${env.JOB_NAME}-${env.BUILD_NUMBER} 触发构建",
             body: "请点击==> URL ${env.BUILD_URL} 验证构建结果"
       }
     }

//参数化构建
      stage('是否发布镜像') {
            agent any
            steps {
                withCredentials([usernamePassword(credentialsId: 'aliyundocker', passwordVariable: 'apwd', usernameVariable: 'user')]) {
                    // some block
                     sh 'docker login -u $user -p $apwd registry.cn-hangzhou.aliyuncs.com'
                     sh 'docker tag icoding-java-img registry.cn-hangzhou.aliyuncs.com/milianops/icodingdocker/icoding-java-img:v1.0'
                     sh 'docker push registry.cn-hangzhou.aliyuncs.com/milianops/icodingdocker/icoding-java-img:v1.0'
                }

            }
      }
   }



}
