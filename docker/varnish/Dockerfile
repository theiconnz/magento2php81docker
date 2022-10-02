FROM httpd:latest

ARG PROJECT_DOMAIN
ARG PROJECT_ROOT

RUN apt-get update && apt-get install -y vim curl libxml2-dev
RUN apt-get update && apt-get install -y varnish vim && apt-get clean

##install supervisor and setup supervisord.conf file
RUN apt-get install -y supervisor \
&& mkdir -p /var/log/supervisor

ADD start.sh /start.sh
RUN chmod +x /start.sh

COPY varnish.vcl /etc/varnish/varnish.vcl
COPY varnish.secret /etc/varnish/varnish.secret
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY varnish /etc/default/varnish

ENV VCL_CONFIG      /etc/varnish/default.vcl
ENV CACHE_SIZE      256M
ENV VARNISHD_PARAMS -a :80 -p workspace_backend=98304 \
        -p http_resp_size=98304 \
        -p http_resp_hdr_len=65536 -s malloc,256m \
        -T localhost:6082 \
        -p default_ttl=3600 -p default_grace=3600 -S /etc/varnish/secret

#RUN echo "upstream php-upstream { server php:9000; }" > /etc/nginx/conf.d/upstream.conf

EXPOSE 6082 80
CMD ["/usr/bin/supervisord"]
CMD /start.sh