node(){
         withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'AWS_CRED']]){
               withCredentials([usernamePassword(credentialsId: 'GIT_CRED', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){
               stage('CREATE DEPLOYMENT YAML'){
               sh '''
                   echo 'apiVersion: eksctl.io/v1alpha5' > ./cluster.yaml
                   echo 'kind: ClusterConfig' >> ./cluster.yaml
                   echo 'metadata :' >> ./cluster.yaml
                   echo "  name: ${NOMCLUSTER}" >> ./cluster.yaml
                   echo "  region: ${REGION}" >> ./cluster.yaml
               '''
               if (VPCJENKINS == "CreateNew"){
                  echo 'Pas de VPC préciser on passe a la suite'
                }else{
                   sh '''
                   echo 'vpc:' >> ./cluster.yaml
                   echo "  id: ${VPCJENKINS}" >> ./cluster.yaml
                   echo "  cidr: ${CIDRVPC}" >> ./cluster.yaml"
                   echo "  subnets:" >> ./cluster.yaml
                   echo "    private:" >> ./cluster.yaml
                   echo "      ${REGION}:" >> ./cluster.yaml
                   echo "        ${SUBNETNAME}:" >> ./cluster.yaml
                   echo "        ${CIDRSUBNET}:" >> ./cluster.yaml
                  '''
                }
                }

               stage('ADDING CLUSTER PARAMETERS'){
                   sh '''
                   echo 'nodeGroups:' >> ./cluster.yaml
                   echo '  - name: ng-2' >> ./cluster.yaml
                   echo "    instanceType: ${INSTANCETYPE}" >> ./cluster.yaml
                   echo "    desiredCapacity: ${DESIREDCAPACITY}" >> ./cluster.yaml
                   '''
                   }
               stage('CREATE CLUSTER AND IDENTITY ADMIN FOR USER'){
                   sh '''
                   cat ./cluster.yaml
                   eksctl create cluster -f cluster.yaml
                   eksctl create iamidentitymapping --cluster  ${NOMCLUSTER} --arn ${ARN} --group system:masters --region ${REGION} --username admin

                   '''
                 }

                }
         }
}