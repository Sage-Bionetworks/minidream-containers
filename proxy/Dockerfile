FROM httpd:2.4.48

# Install self-signed SSL certificate
# /etc/ssl/certs/ssl-cert-snakeoil.pem
RUN apt-get update \
    && apt-get -y install \
        ssl-cert \
    && rm -rf /var/lib/apt/lists/*

# Enable modules required for SSL and reverse proxy
COPY conf/httpd.conf /usr/local/apache2/conf/httpd.conf

# Forward HTTPS traffic to port 8787
COPY conf/proxy.conf /usr/local/apache2/conf/extra/proxy.conf 

# Allow traffic on HTTPS port
EXPOSE 443

# Start Apache by default
CMD ["httpd", "-D", "FOREGROUND"]
