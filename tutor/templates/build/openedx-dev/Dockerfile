FROM {{ DOCKER_REGISTRY }}{{ DOCKER_IMAGE_OPENEDX }}
MAINTAINER Overhang.io <contact@overhang.io>

# Install useful system requirements
RUN apt update && \
    apt install -y vim iputils-ping dnsutils telnet \
    && rm -rf /var/lib/apt/lists/*

# Install dev python requirements
RUN pip install -r requirements/edx/development.txt
# Pinning the jedi library is required in python 3.5 to avoid deprecation warnings from ipython
RUN pip install ipdb==0.13.2 ipython==7.9.0 jedi==0.15.2

# Recompile static assets: in development mode all static assets are stored in edx-platform,
# and the location of these files is stored in webpack-stats.json. If we don't recompile
# static assets, then production assets will be served instead.
RUN rm -r /openedx/staticfiles && \
    mkdir /openedx/staticfiles && \
    openedx-assets webpack --env=dev

# Copy new entrypoint (to take care of permission issues at runtime)
COPY ./bin /openedx/bin
RUN chmod a+x /openedx/bin/*

# Configure new user
ARG USERID=1000
RUN create-user.sh $USERID

# Default django settings
ENV SETTINGS tutor.development
