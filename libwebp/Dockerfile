
##
## ==============================
## :::: LIBWEBP DOCKER BUILD ::::
## ==============================
## - download and build google's libwebp from source
## - libwebp emscripten build outputs "dist/webp.js"
##

##
## ESTABLISH DEBIAN CONTAINER
##

FROM debian:buster-slim
RUN mkdir /work
RUN apt-get update && apt-get install -y \
  vim git-core curl \
  build-essential cmake python nodejs \
  libpng-dev libjpeg-dev

##
## DOWNLOAD AND INSTALL EMSCRIPTEN
##

# download emscripten
RUN cd /work \
  && git clone https://github.com/emscripten-core/emsdk.git

# run emscripten self installation
RUN cd /work/emsdk \
  && git pull \
  && ./emsdk install 1.39.7-fastcomp \
  && ./emsdk activate 1.39.7-fastcomp

# verify emscripten
RUN ["/bin/bash", "-c", "cd /work/emsdk && source ./emsdk_env.sh && emcc --version"]

##
## DOWNLOAD LIBWEBP SOURCE
##

RUN mkdir /work/libwebp && cd /work/libwebp \
  && git clone --branch v1.1.0 https://github.com/webmproject/libwebp.git .

##
## COPY LOCAL FILES INTO CONTAINER
##

ADD . /work

##
## RUN LIBWEBP EMSCRIPTEN BUILD
##

# # replace libwebp emscripten cmake instructions
# RUN rm -rf /work/libwebp/CMakeLists.txt \
#   && cp /work/source/CMakeLists.txt /work/libwebp

# run the webp js build
RUN ["/bin/bash", "-c", "cd /work/emsdk \
  && source ./emsdk_env.sh \
  && cd /work/libwebp/webp_js \
  && cmake -DWEBP_BUILD_WEBP_JS=ON \
    -DEMSCRIPTEN_GENERATE_BITCODE_STATIC_LIBRARIES=1 \
    -DCMAKE_TOOLCHAIN_FILE=$EMSDK/fastcomp/emscripten/cmake/Modules/Platform/Emscripten.cmake \
    ../ \
  && make"]
