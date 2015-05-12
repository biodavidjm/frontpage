FROM node:0.10.38
MAINTAINER Siddhartha Basu <siddhartha-basu@northwestern.edu>

# Add an user that will be used for install purpose
RUN useradd -m -r -s /sbin/nologin -c "Docker image user" caboose \
    && chown -R caboose /home/caboose

# Set as default user 
USER caboose

# Clone the source code
RUN git clone --branch refactor/standardize-install \
    && https://github.com/dictyBase/frontpage-dictybase.git \
    && /home/caboose/frontpage-dictybase 

# Source code folder will be the default landing spot
WORKDIR /home/caboose/frontpage-dictybase

# Env to include binaries inside node_modules folder
ENV node_modules/.bin:$PATH

# Install dependencies
RUN npm install --production \
    && bower install

# Default command
CMD ["npm", "run", "build"]
    

