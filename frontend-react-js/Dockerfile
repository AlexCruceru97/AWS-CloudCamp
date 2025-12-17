# 1st stage: building stage 
FROM node:16.18 AS builder

# Set working directory (inside the container)
WORKDIR /frontend-react-js

# Copy only dependency files. If nothing changes in a step, Docker reuses the cached laye
COPY package*.json ./
# Install dependencies (inside the container)
RUN npm install

#   Copy files into /frontend-react-js, which is set above by WORKDIR (Same as COPY . ./frontend-react-js)
COPY . .


# 2nd stage: runtime stage
FROM node:16.18-slim
# Set default port environment variable
ENV PORT=3000

# Set working directory (inside the container)
WORKDIR /frontend-react-js
# Copy node_modules from builder stage
COPY --from=builder /frontend-react-js/node_modules ./node_modules



# Expose the port (inside the container)
EXPOSE ${PORT}
# Command to run the application (inside the container)
CMD ["npm", "start"]
