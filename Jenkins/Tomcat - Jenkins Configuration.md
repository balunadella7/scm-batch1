#Install & configure Tomcat server :

#Check out Official WebPage:
#https://tomcat.apache.org/

#Launch AWS EC2 Linux Instance:

#Install JDK
#Install epel Package:
amazon-linux-extras install epel

#Install Java: 
amazon-linux-extras install java-openjdk11

#Set Java Path / Environment Variables:
#open .bashrc & add the following lines:

export JAVA_HOME="/usr/lib/jvm/java-11-openjdk-11.0.13.0.8-1.amzn2.0.3.x86_64"
PATH=$PATH:$HOME/bin:$JAVA_HOME/bin

#Save the file
#open .bash_profile & add the following lines:
export JAVA_HOME="/usr/lib/jvm/java-11-openjdk-11.0.13.0.8-1.amzn2.0.3.x86_64"
PATH=$PATH:$HOME/bin:$JAVA_HOME/bin

#Save the file

source ~/.bash_profile

****************************************************************************************************

#Install tomcat in Amazon Linux Instance:

cd /opt
wget https://dlcdn.apache.org/tomcat/tomcat-8/v8.5.82/bin/apache-tomcat-8.5.82.tar.gz
tar -xvzf /opt/apache-tomcat-8.5.82.tar.gz
mv apache-tomcat-8.5.82 tomcat

#Start Tomcat Server:
#Goto:

cd /opt/tomcat/bin
./startup.sh

****************************************************************************************************

#Add-USer for Tomcat :

useradd -m -d /home/jenkins jenkin

su - jenkin

ssh-keygen

ls ~/.ssh 

#You should see following two files:

#id_rsa - private key
#id_rsa.pub - public

#cat id_rsa & copy the private key and paste it into jenkins node config. enter private key directly field.  Then,

cd /home/jenkins/.ssh

cat id_rsa.pub > authorized_keys

chown -R jenkin /home/jenkins/.ssh
chmod 600 /home/jenkins/.ssh/authorized_keys
chmod 700 /home/jenkins/.ssh

#make jenkin user as a owner to tomcat dir :

chown -R jenkin /opt/tomcat

****************************************************************************************************

# Configure Jenkins :

#Go to Jenkins Master thru Browser.

#Install Publish over ssh plugin

#Goto Manage Jenkins - Configure System - Configure Publish over ssh Plugin

#Create Jenkins freestyle Job to Test the Deployment as discussed








# Sample Pipeline Script:
pipeline {
    agent { label 'slave1' }

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "MAVEN"
    }
    
    stages {
        stage('SCM Checkout') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/LoksaiETA/Java-mvn-app2.git'
            }
		}
        stage('Maven Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
		}
        stage('Deploy to QA AppServer') {
            steps {
				script {
                    sshPublisher(publishers: [sshPublisherDesc(configName: 'QAServer', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: 'target/', sourceFiles: 'target/mvn-hello-world.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
				}
            }
		}
	}
}



**************************************************************************************

#Github webhook to automatically trigger build pipeline!

#Goto Githu Repository setting, 
Select webhook, 

Click Add Webhook

Enter Jenkins Master URL. eg.:
http://<public-IP>:8080/github-webhook/

Choose the push event and save the webhook configuration

**********************************************************************************
#Ensure that you have Installed git on jenkins master: 

sudo yum install git


Go to Jenkins job, Under build trigger, enable "GitHub hook trigger for GITScm polling" option and save the jon
