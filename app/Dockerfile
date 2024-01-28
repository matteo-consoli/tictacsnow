# Use an official Nginx runtime as a parent image
FROM --platform=linux/amd64 nginx:latest

# Set the working directory in the container
#WORKDIR /tic-tac-toe
WORKDIR /usr/share/nginx/html

# Copy the content of the local index.html file to the working directory
COPY index.html .

# Copy the content of the local style.css file to the working directory
COPY style.css .

# Copy the content of the local script.js file to the working directory
COPY script.js .

COPY logo.png .
