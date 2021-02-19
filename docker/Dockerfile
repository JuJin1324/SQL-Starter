FROM wnameless/oracle-xe-11g-r2
LABEL maintainer="jujin1324@daum.net"
LABEL dockerhub="https://hub.docker.com/r/wnameless/oracle-xe-11g-r2"

# Docker Ubuntu Container 한글 패치: https://seolin.tistory.com/81
RUN apt-get update; apt-get install -y locales
RUN locale-gen ko_KR.UTF-8
ENV LANG ko_KR.UTF-8
ENV LC_ALL ko_KR.UTF-8
ENV LANGUAGE ko_KR.UTF-8
ENV NLS_LANG KOREAN_KOREA.AL32UTF8
RUN update-locale LANG=ko_KR.UTF-8

ADD init.sql /docker-entrypoint-initdb.d/
