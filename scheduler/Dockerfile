FROM python:3.8-slim-buster

# update files
RUN apt-get update && \
    apt-get install -y netcat && \
    apt-get install vim -y && \
    pip3 install apache-airflow[celery,redis,postgres,crypto]==2.1.4 celery==4.4.7 flower==0.9.7

WORKDIR /root/airflow

## adding spark submit
ENV SPARK_VERSION=3.1.2
ENV HADOOP_VERSION=3.2

### Java install
RUN mkdir -p /usr/share/man/man1
RUN apt-get update && apt-get install -y software-properties-common \
    && apt-add-repository 'deb http://security.debian.org/debian-security stretch/updates main' \
    && apt-get update && apt-get install -y openjdk-8-jdk

WORKDIR /root

RUN apt-get install -y wget \
    && wget https://archive.apache.org/dist/spark/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz \
    && tar -xvzf spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz \
    && mv spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION} spark \
    && rm spark-${SPARK_VERSION}-bin-hadoop${HADOOP_VERSION}.tgz \
    && pip3 install pyspark==${SPARK_VERSION} \
    #&& cd /css \
    #&& jar uf /spark/jars/spark-core_2.11-${SPARK_VERSION}.jar org/apache/spark/ui/static/timeline-view.css \
    && cd /

ENV SPARK_MASTER_NAME spark-master
ENV SPARK_MASTER_PORT 7077
ENV SPARK_HOME /root/spark
ENV PATH="/root/spark/bin:${PATH}"
ENV MINIO_ACCESS_KEY=${MINIO_ACCESS_KEY}
ENV MINIO_SECRET_KEY=${MINIO_SECRET_KEY}

WORKDIR /root/airflow/

COPY config/variables.json variables.json
COPY config/connections.yml connections.yml
COPY config/setup_connections.py setup_connections.py
COPY config/requirements.txt requirements.txt
COPY entrypoint.sh entrypoint.sh
RUN chmod +x ./entrypoint.sh

ENV PYTHONHASHSEED 1

ENTRYPOINT ["./entrypoint.sh"]