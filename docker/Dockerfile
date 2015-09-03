FROM xrlx/heka_base
MAINTAINER Xavier Lange <xavier.lange@gudtech.com> (@tureus)

RUN echo "add_external_plugin(git https://github.com/gudtech/heka-scamp master)" > "./cmake/plugin_loader.cmake"

RUN . ./env.sh && go get github.com/gudtech/scamp-go/scamp
RUN . ./env.sh && . ./build.sh && cd /heka/build && make deb

RUN mkdir -p /heka_final
RUN find /heka/build -name "*.deb" -exec cp {} /heka_final/heka.deb \;
COPY Dockerfile.final /heka_final/Dockerfile
COPY hekad.toml /heka_final/
COPY docker-entrypoint.sh /heka_final/

# I refuse to have stale SCAMP init scripts
RUN git clone https://github.com/gudTECH/scamp.git /scamp
RUN cp /scamp/scripts/init-system-config /heka_final/
RUN cp /scamp/scripts/provision-soa-service /heka_final/

RUN curl -sSL https://get.docker.io/builds/Linux/x86_64/docker-1.2.0 -o /tmp/docker && \
    echo "540459bc5d9f1cac17fe8654891814314db15e77 /tmp/docker" | sha1sum -c - && \
    mv /tmp/docker /usr/local/bin/docker && \
    chmod +x /usr/local/bin/docker

CMD docker build -t xrlx/heka_scamp /heka_final