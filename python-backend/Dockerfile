FROM python:3.6-alpine

RUN apk add --update --no-cache mariadb-client \
                                mariadb-dev \
	&& apk add --no-cache --virtual .build-deps \
		gcc \
		musl-dev \
        && pip install mysqlclient==1.3.12 \
	&& apk del .build-deps

WORKDIR /install
Add /app/requirements.txt /install/
RUN pip install -r requirements.txt

# Make port 8000 available to the world outside this container
EXPOSE 8000

# Run app.py when the container launches

CMD ["gunicorn", "-b 0.0.0.0:8000", "--chdir", "/app/DjangoBackEnd", "DjangoBackEnd.wsgi"]
