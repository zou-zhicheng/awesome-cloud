# jameszou example dockerfile
FROM busybox
MAINTAINER zouzhicheng@foxmail.com
WORKDIR /testdir
RUN touch tmpfile1
COPY ["tmpfile2", "."]
ADD ["dockerfile-example1.tgz", "."]
ENV WELCOME "hello docker!!!"
