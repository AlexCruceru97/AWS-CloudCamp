# 1st stage: building stage 
FROM node:16.18 AS builder

# Set working directory (inside the container)
WORKDIR /frontend-react-js
# Create working directory inside the container
COPY . /frontend-react-js
# Install dependencies (inside the container)
RUN npm install

# 2nd stage: runtime stage
FROM node:16.18-slim
# Set default port environment variable
ENV PORT=3000

# Set working directory (inside the container)
WORKDIR /frontend-react-js
# Copy node_modules from builder stage
COPY --from=builder /frontend-react-js/node_modules ./node_modules

#   Copy application source code from builder stage
COPY . .

# Expose the port (inside the container)
EXPOSE ${PORT}
# Command to run the application (inside the container)
CMD ["npm", "start"]
