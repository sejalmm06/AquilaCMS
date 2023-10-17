# Use an official Nginx runtime as the base image
FROM nginx:latest

# Copy your web application files to the Nginx document root
COPY . /usr/share/nginx/html

# Expose port 80 (the default for HTTP)
EXPOSE 80

# Define the command to start Nginx
CMD ["nginx", "-g", "daemon off;"]
