# Use an official Nginx runtime as the base image
FROM nginx:latest

# Copy your frontend code to the web server directory
COPY ./frontend /usr/share/nginx/html

# Expose port 80 (the default for HTTP)
EXPOSE 80

# Define the command to start Nginx
CMD ["nginx", "-g", "daemon off;"]
