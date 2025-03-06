#Laravel Docker Setup
Overview

This project sets up a Laravel application using Docker with Nginx, PHP, and MySQL

Steps to Set Up

1. Clone the Repository
git clone <repository-url>
cd <project-directory>

2. Build and Start the Containers
docker-compose up --build -d

3. Import SQL File into the Database
Place your laravel_db.sql file inside the db_backup directory and run
  docker exec -i mysql_container mysql -u root -proot -h localhost laravel_db < ./db_backup/laravel_db.sql

4. Uncomment Cache Clear Script
After importing the SQL file, make sure to uncomment the following line in the Dockerfile

  RUN if [ -f ./cache-clear.sh ]; then ./cache-clear.sh; fi

6. Stop the Docker Containers
docker-compose down

7. Rebuild the Containers
docker-compose up --build -d

9. Access the Application
Open your browser and go to:

http://localhost


Let me know if you need modifications!
