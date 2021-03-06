FROM alpine:3.6
LABEL maintainer Marco Palladino, marco@mashape.com

ENV KONG_VERSION 0.13.1
ENV KONG_SHA256 caf119ce7a367b9d9cd1ace55c4b6a499ff3c4693f80ea25129ebf2da0373fcc 

RUN apk add --no-cache --virtual .build-deps wget tar ca-certificates \
	&& apk add --no-cache libgcc openssl pcre perl tzdata curl \
	&& wget -O kong.tar.gz "https://bintray.com/kong/kong-community-edition-alpine-tar/download_file?file_path=kong-community-edition-$KONG_VERSION.apk.tar.gz" \
	&& echo "$KONG_SHA256 *kong.tar.gz" | sha256sum -c - \
	&& tar -xzf kong.tar.gz -C /tmp \
	&& rm -f kong.tar.gz \
	&& cp -R /tmp/usr / \
	&& rm -rf /tmp/usr \
	&& cp -R /tmp/etc / \
	&& rm -rf /tmp/etc \
	&& apk del .build-deps


ENV KONG_LUA_PACKAGE_PATH /kong-plugins/?.lua;;
ENV KONG_CUSTOM_PLUGINS cas-ticket-auth,jws-token-auth
ADD kong/ /kong-plugins/kong/

COPY ssl /ssl
ENV KONG_SSL_CERT /ssl/cert/STAR_saas-plat_com.crt
ENV KONG_SSL_CERT_KEY /ssl/saas-plat.key

COPY docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod +x /docker-entrypoint.sh
ENTRYPOINT ["/docker-entrypoint.sh"]

EXPOSE 8000 8443 8001 8444

STOPSIGNAL SIGTERM
CMD ["/usr/local/openresty/nginx/sbin/nginx", "-c", "/usr/local/kong/nginx.conf", "-p", "/usr/local/kong/"]
