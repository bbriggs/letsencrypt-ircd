FROM nginx:stable

COPY --from=bbriggs/kiwiirc:latest /app/kiwiirc/dist /usr/share/nginx/html
COPY config.json /usr/share/nginx/html/static/config.json

EXPOSE 80

STOPSIGNAL SIGTERM
