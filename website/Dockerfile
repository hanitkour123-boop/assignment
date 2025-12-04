# Use a tiny nginx image
FROM nginx:alpine

# Remove default nginx content (optional)
RUN rm -rf /usr/share/nginx/html/*

# Copy site files into nginx html directory
COPY . /usr/share/nginx/html

# Expose port 80 (container listens here)
EXPOSE 80

# Start nginx (default CMD from base image is fine, but we'll explicit it)
CMD ["nginx", "-g", "daemon off;"]
