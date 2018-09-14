FROM jboss/wildfly:14.0.0.Final

MYSQL_DIRVER_VERSION=5.2.31

ARG DB_HOST=localhost
ARG DB_PORT=4306
ARG DB_NAME=test
ARG DB_USER=root
ARG DB_PASS=root

# Add an admin user (password admin)
RUN $JBOSS_HOME/bin/add-user.sh admin -p admin -s

# Start jboss
RUN /bin/sh -c '$JBOSS_HOME/bin/standalone.sh &' && sleep 10

# Install databas drivers
RUN curl --location --output /tmp/mysql-connector-java-${MYSQL_DIRVER_VERSION}.jar --url https://repo1.maven.org/maven2/mysql/mysql-connector-java/${MYSQL_DIRVER_VERSION}/mysql-connector-java-${MYSQL_DIRVER_VERSION}.jar $JBOSS_HOME/bin/jboss-cli.sh --connect --command="module add --name=com.mysql --resources=/tmp/mysql-connector-java-${MYSQL_DIRVER_VERSION}.jar --dependencies=javax.api,javax.transaction.api" && \ 
  $JBOSS_HOME/bin/jboss-cli.sh --connect --command="/subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-xa-datasource-class-name=com.mysql.jdbc.jdbc2.optional.MysqlXADataSource)"

# Install datasource
RUN $JBOSS_HOME/bin/jboss-cli.sh --connect --command="data-source add --name=eapdb --jndi-name=java:/jdbc/datasources/eapdb --driver-name=mysql --connection-url=jdbc:mysql://${DB_HOST}:${DB_PORT}/${DB_NAME}?useUnicode=true&amp;characterEncoding=UTF-8  --user-name=${DB_USER} --password=${DB_PASS} --use-ccm=false --max-pool-size=25 --blocking-timeout-wait-millis=5000 --enabled=true"

# Shutdown jboss
RUN $JBOSS_HOME/bin/jboss-cli.sh --connect --command=:shutdown && \
  rm -rf $JBOSS_HOME/standalone/configuration/standalone_xml_history/ $JBOSS_HOME/standalone/log/* && \
  rm -rf /tmp/mysql-*.jar

# Expose http and admin ports
EXPOSE 8080 9990

# Set the default command to run on boot
# This will boot WildFly in the standalone mode
CMD ["/opt/jboss/wildfly/bin/standalone.sh", "-b", "0.0.0.0", "-bmanagement", "0.0.0.0", "-Djboss.http.port=8080"]