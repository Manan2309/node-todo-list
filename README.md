# node-todo-list
Jenkins CI/CD pipeline with guthub integration.

Steps-
1. Create an EC2 instance with 'Ubuntu' as the AMI and connect to the instance.

   ![image](https://github.com/Manan2309/node-todo-list/assets/98276092/7809ab40-05ba-4650-93f0-173cca252318)

2. Install Java and Jenkins. Use the following commands-
sudo apt update
sudo apt install openjdk-17-jdk
java -version ( to verify is java is installed )

curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins

3. Navigate to the security tab and edit inbound rules as such.
![image](https://github.com/Manan2309/node-todo-list/assets/98276092/96d9d371-6b52-4d65-9650-3cc8c79f9f78)

4. Enter <your-instane-public-ip>:8080 in your new tab and setup jenkins.
![image](https://github.com/Manan2309/node-todo-list/assets/98276092/99edfd1f-8293-49e3-9b3c-0d70bfa398c1)
 
5. Navigate to new item and start a freestyle project for creating a freestyle pipeline.
6. Add a description and choose github project and provide github URL.
7. Go to Gihub settings and choose SSH and GPG keys to create a new SSH KEY.
8. Type ' ssh-keygen ' in your instance connect to generate ssh key.
9. Keys are stored in folder. Give command ' cd .ssh ' get public and private keys.
10. You'll get a public and private key.
 ![image](https://github.com/Manan2309/node-todo-list/assets/98276092/f345138e-b37d-4795-8732-a950b60c8e90)
11. Add your public-key and click 'Add SSH Key'.
 ![image](https://github.com/Manan2309/node-todo-list/assets/98276092/708fe4c1-1333-47bd-9e40-53e86d6555d0)
(I'll be deleting this key afterwards)
12.Go to Jenkins and give credentials.
 ![image](https://github.com/Manan2309/node-todo-list/assets/98276092/00721f95-9e56-4f9c-8afd-889189435afa)

13. Select the brand your github repo is in and click save.
14. Click on buildnow ,you can see the console output. And Jenkins is integrated with Github.
![image](https://github.com/Manan2309/node-todo-list/assets/98276092/8057e3d0-6fb6-4235-8f42-b001c4756886)

15. To check this go to your ec2 instance and first change directory 'cd  /var/lib/jenkins/workspace/todo-node-app ' and then ' ls ' to view the files.
16. To run the code type following commands-
sudo apt install nodejs
sudo apt install npm
npm install (installs the requirement mentioned in package.json)
node app.js

17. Go to security group and give access to port 8000.
![image](https://github.com/Manan2309/node-todo-list/assets/98276092/1ee3ded5-b791-4324-bddc-b1421d6c8d5e)
18. Then in a new tab type <your-instance-public-ip>:8000 and you'll see your app running
19. To use it in other OS , create a docker file
20. Install Docker ( sudo apt install docker.io )
21. Type sudo vim Dockerfile

FROM node:12.2.0-alpine
WORKDIR app
COPY . .
RUN npm install
RUN npm run test
EXPOSE 8000
CMD ["node","app.js"]

21. To build image
docker build . -t node_todo_app

22. Manage permission
sudo usermod -a -G docker $USER

23. Then, reboot system (sudo reboot)
24. Recoonect to your instance. It can take some time.
25. Repeat steps 21 and 22.

![image](https://github.com/user-attachments/assets/eb0d95d0-5c04-4c5a-adfb-0457a94ee10a)

26. Run the image using the command
( docker run -d --name node-todo-app -p 8000:8000 node-todo-app )
27. Now, lets run it through jenkins.
28. To do this first kill docker from ec2 connect.
29. Go to build steps and choose execute shell.
30. Type the exact same commands for docker and click on save.

![image](https://github.com/user-attachments/assets/62553574-6ecd-4e05-a5c1-7e045f29eca5)

31. If it shows a permission error so give the permission once again.
(sudo usermod -a -G docker jenkins
sudo systemctl restart jenkins)
![image](https://github.com/user-attachments/assets/84731b1f-efef-4ba4-a99e-67ec25254faa)

33. We were doing it manually yet. Now to trigger automatically we will use webhooks to trigger it automatically.
34. Navigate to dashboard-> manage plugins-> available plugins-> github integration install.
35. Go to github repository settings -> webhook ->payload URL.
36. payload url = http://< your-instance-public-ip >:8080/github-webhook/
37. Go to your security gourp and configure the security of your 8080 port as anywhere otherwise github won't access.
   ![image](https://github.com/user-attachments/assets/7c4bf62e-399c-4754-a62f-fe3c5ac8d76a)

38.Then, got to your job -> configure-> build triggers ->  GitHub hook trigger for GITScm polling -> save.

39. I did a code change in github and it triggered automatically in jenkins.
![image](https://github.com/user-attachments/assets/784ae89a-2e88-4369-b120-52214122772b)

40. Hence, completed
