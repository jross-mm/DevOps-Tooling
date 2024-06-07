# Use mysql 5.7 as the base image
FROM mysql:5.7

# Set environment variables for MySQL
ENV MYSQL_ROOT_PASSWORD=rootpassword
ENV MYSQL_DATABASE=flask-db

# Copy the SQL file into the docker-entrypoint-initdb.d directory
# This directory is used by the MySQL image to automatically run scripts at container startup
COPY CreateTable.sql /docker-entrypoint-initdb.d/
