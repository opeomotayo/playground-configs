FROM openjdk:11.0.5-jdk
COPY *.jar app.jar
ENTRYPOINT java -Dorg.jenkinsci.plugins.durabletask.BourneShellScript.HEARTBEAT_CHECK_INTERVAL=86400 -jar app.jar
