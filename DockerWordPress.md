## Docker Project with WordPress

### Installing Docker
- I will be installing Docker in [Windows](https://docs.docker.com/desktop/install/windows-install/). Use this link if you are installing Docker in [macOS](https://docs.docker.com/desktop/install/mac-install/).  
- Check the "System requirements" and make sure you have everything, such as enabling WSL 2, enable virtualization, installing the Linux Kernel update package, and more. 
- If you are not in your admin account, you must add the user account to the docker-users group, or run Docker as an administrator.
  - As an administrator, run Computer Manager. Navigate to Local Users and Group, Groups, docker-users and right click to add the user to the group. 
- If there is any pop up message after running Docker, following the instructions given. 
- Docker Compose should be installed by default on Docker Desktop.

### Setting up WordPress
- Using your prefered command line (I used powershell), create a directory for WordPress ```mkdir wordpress```
- Then go into the directory ```cd wordpress```
- Within the wordpress directory create a docker-compose.yml file (I used notepad++) and input this text and save it.  
```
version: "3" 
# Defines which compose version to use
services:
  # Services line define which Docker images to run. In this case, it will be MySQL server and WordPress image.
  db:
    image: mysql:5.7
    # image: mysql:5.7 indicates the MySQL database container image from Docker Hub used in this installation.
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: MyR00tMySQLPa$$5w0rD
      MYSQL_DATABASE: MyWordPressDatabaseName
      MYSQL_USER: MyWordPressUser
      MYSQL_PASSWORD: Pa$$5w0rD
      # Previous four lines define the main variables needed for the MySQL container to work: database, database username, database user password, and the MySQL root password.
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    restart: always
    # Restart line controls the restart mode, meaning if the container stops running for any reason, it will restart the process immediately.
    ports:
      - "8000:80"
      # The previous line defines the port that the WordPress container will use. After successful installation, the full path will look like this: http://localhost:8000
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: MyWordPressUser
      WORDPRESS_DB_PASSWORD: Pa$$5w0rD
      WORDPRESS_DB_NAME: MyWordPressDatabaseName
# Similar to MySQL image variables, the last four lines define the main variables needed for the WordPress container to work properly with the MySQL container.
    volumes:
      ["./:/var/www/html"]
volumes:
  mysql: {}
```
- To create and start the containers, within the wordpress directory, run this command ```docker compose up -d```

### Installing wordpress 
- In your browser, go to this link [http://localhost:8000/](http://localhost:8000/)
- Go through the process of setting up your website and log in. 
- You should be presented with the main WordPress dashboard screen. 
![Finished Wordpress Website](https://user-images.githubusercontent.com/113713588/201997259-a1cf0eb1-fc62-4d9f-a07e-9a2e9fb21251.png)


### Resources
[https://www.hostinger.com/tutorials/run-docker-wordpress](https://www.hostinger.com/tutorials/run-docker-wordpress)

