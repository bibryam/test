### keys

keytool -genkeypair -dname " CN=example.com, OU=Engineering, O=Progress, ST=Co. Dublin, C=IE" -validity 365 -alias https -keypass mykeystorepass -storetype JKS -keystore keystore.jks -storepass mykeystorepass

keytool -genkeypair -dname " CN=example.com, OU=Engineering, O=Progress, ST=Co. Dublin, C=IE" -validity 365 -alias jgroups -keypass mykeystorepass -storetype JCEKS -keystore jgroups.jceks -storepass mykeystorepass

oc create -n openshift -f https://raw.githubusercontent.com/jboss-openshift/application-templates/master/jboss-image-streams.json

oc delete project bpm-demo
oc new-project bpm-demo

oc create serviceaccount processserver-service-account
oc policy add-role-to-user view system:serviceaccount:bpm-demo:processserver-service-account
oc create secret generic processserver-app-secret --from-file=certs/jgroups.jceks --from-file=certs/keystore.jks
oc secret add sa/processserver-service-account secret/processserver-app-secret

oc process -f https://raw.githubusercontent.com/jboss-openshift/application-templates/ose-v1.3.7/processserver/processserver63-mysql-s2i.json \
KIE_CONTAINER_DEPLOYMENT=processserver-library=org.openshift.quickstarts:processserver-library:1.3.0.Final \
KIE_SERVER_USER=kieserver \
KIE_SERVER_PASSWORD=kiepassword \
SOURCE_REPOSITORY_URL=https://github.com/jboss-openshift/openshift-quickstarts \
SOURCE_REPOSITORY_REF=1.3 \
CONTEXT_DIR=processserver/library \
DB_JNDI=java:jboss/datasources/ExampleDS \
DB_DATABASE=root \
DB_USERNAME=user_mysql \
DB_PASSWORD=pass_mysql \
 | oc create -f -

http://kie-app-bpm-demo.192.168.99.100.xip.io/kie-server/services/rest/server


