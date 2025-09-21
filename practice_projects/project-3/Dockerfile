FROM nginx:1.29.0-bookworm

# Copy website and nginx configuration files
COPY ./hello/ /usr/share/nginx/hello/
COPY ./hello.conf /etc/nginx/conf.d/

# Remove the default configuration
RUN rm /etc/nginx/conf.d/default.conf 

# The image will inherit nginx entrypoint command
#CMD ["nginx", "-g", "daemon off;"]