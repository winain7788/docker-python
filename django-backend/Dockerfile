FROM continuumio/miniconda3

# Define environment variable
ARG GIT_BRANCH=develop
ARG CONDA_ENV_DEV=env
ARG CONDA_ENV_PRD=base
ARG DB_DEV=posdbt01
ARG DB_PRD=posdbt01


# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY /key/gitlab/* /app/

RUN mkdir /root/.ssh && \
    chmod 600 id_rsa && \
    cp id_rsa id_rsa.pub known_hosts /root/.ssh
RUN git clone -b ${GIT_BRANCH} --single-branch git@gitlab.com:weserve-dev/pos-backend-py.git

RUN sed -i -e "s/${CONDA_ENV_DEV}/${CONDA_ENV_PRD}/g" pos-backend-py/DjangoBackEnd/envDev.yml && \
    sed -i -e "s/${DB_DEV}/${DB_PRD}/g" pos-backend-py/DjangoBackEnd/my.cnf

#Adjust env file for conda
RUN sed -i '/libcxx/d' pos-backend-py/DjangoBackEnd/envDev.yml && \
    sed -i '/libcxxabi/d' pos-backend-py/DjangoBackEnd/envDev.yml && \
    sed -i '/prefix/d' pos-backend-py/DjangoBackEnd/envDev.yml
RUN echo "prefix: /opt/conda" >> pos-backend-py/DjangoBackEnd/envDev.yml

# Install any needed packages specified in requirements.txt
#RUN conda update -y --quiet --prefix /opt/conda conda
RUN conda env update --quiet -f=pos-backend-py/DjangoBackEnd/envDev.yml

# Generate static folder
#RUN python pos-backend-py/DjangoBackEnd/manage.py collectstatic --noinput

# Make port 8000 available to the world outside this container
EXPOSE 8000

# Run app.py when the container launches
#CMD ["python", "pos-backend-py/DjangoBackEnd/manage.py", "runserver", "0.0.0.0:8000"]

CMD ["gunicorn", "-b 0.0.0.0:8000", "--chdir", "pos-backend-py/DjangoBackEnd", "DjangoBackEnd.wsgi"]
