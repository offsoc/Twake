# First stage: building frontend
FROM node:lts AS build

RUN yarn global add webpack webpack-cli
COPY frontend /twake-react/

WORKDIR /twake-react/

ENV GENERATE_SOURCEMAP=false
RUN cp /twake-react/src/app/environment/environment.ts.dist /twake-react/src/app/environment/environment.ts && \
    yarn install --network-timeout 1000000000 && \
    # cat /twake-react/src/app/environment/environment.ts.dist && \
    # cat /twake-react/src/app/environment/environment.ts && \
    yarn build && \
    rm -rf node_modules

# Second stage: configuring nginx and copying build artifacts
FROM nginx:latest
COPY docker/twake-nginx/nginx.conf /etc/nginx/nginx.conf
COPY docker/twake-nginx/site.conf /etc/nginx/sites-available/site.template
COPY docker/twake-nginx/redirect.conf /etc/nginx/sites-available/redirect
COPY docker/twake-nginx/entrypoint.sh /
COPY docker/twake-nginx/self-signed.sh /usr/local/bin/
COPY --from=build /twake-react /twake-react

RUN chmod +x /entrypoint.sh && rm /etc/nginx/conf.d/default.conf

ENTRYPOINT ["/entrypoint.sh", "$DEV"]

EXPOSE 80
EXPOSE 443
