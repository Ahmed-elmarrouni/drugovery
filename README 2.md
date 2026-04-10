docker run -d -v /var/run/docker.sock:/var/run/docker.sock -v $(which docker):$(which docker) -p 8081:8080 -p 50000:50000 jenkins/jenkins:lts;

docker logs 6912

then i copy e7a783f95e8f448fa87857ec66c40506 and put it at administrative pswrd
then I slected Install suggested plugins

![alt text](images/01_plugins-installation.png)
![alt text](images/02_acc-creation.png)
![alt text](images/03_dashboard.png)

AT maven I instakked the latest version
![alt text](images/04_Maven-installations.png)
