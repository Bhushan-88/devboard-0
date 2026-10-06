FROM node:26 # Use the official Node.js 26 image as the base image 

WORKDIR /app # Set the working directory inside the container to /app

COPY . . # Copy all files from the host to the working directory inside the container

RUN  npm install # Install the project dependencies specified in package.json

EXPOSE 5173 # Expose port 5173 to allow external access to the application

CMD ["npm", "run", "dev", "--", "--host", "0.0.0.0", "--port", "5173"]