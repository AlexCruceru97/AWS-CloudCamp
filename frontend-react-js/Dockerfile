FROM node:16.18
# Set default port environment variable
ENV PORT=3000
# Create working directory inside the container
COPY . /frontend-react-js
# Set working directory (inside the container)
WORKDIR /frontend-react-js
# Install dependencies (inside the container)
RUN npm install
# Expose the port (inside the container)
EXPOSE ${PORT}
# Command to run the application (inside the container)
CMD ["npm", "start"]
