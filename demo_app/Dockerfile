FROM python:alpine

# Install nginx
RUN apk add --no-cache nginx

# Install the New Relic package
RUN pip install newrelic

# Copy your application files to the nginx HTML directory
COPY . /usr/share/nginx/html

# Start nginx
CMD ["nginx", "-g", "daemon off;"]
