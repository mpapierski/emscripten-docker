FROM debian:latest
MAINTAINER Vilibald Wanča (wvi@apiary.io)

ENV EMCC_SDK_VERSION 1.36.7
ENV EMCC_SDK_ARCH 32

WORKDIR /

RUN apt-get update && apt-get install -y --no-install-recommends ca-certificates build-essential curl git-core openjdk-7-jre-headless python \
    && apt-mark hold openjdk-7-jre-headless \
    && apt-mark hold make \
    && curl -Os https://cmake.org/files/v3.6/cmake-3.6.1.tar.gz \
    && tar xf cmake-3.6.1.tar.gz \
    && cd cmake-3.6.1 && ./configure && make && make install && cd ..\
    && rm -rf cmake* \
    && curl -sL https://deb.nodesource.com/setup_6.x | bash - \
    && apt-get install -y nodejs \
    && curl https://s3.amazonaws.com/mozilla-games/emscripten/releases/emsdk-portable.tar.gz > emsdk-portable.tar.gz \
    && tar xzf emsdk-portable.tar.gz \
    && rm emsdk-portable.tar.gz \
    && cd emsdk_portable \
    && ./emsdk update \
    && ./emsdk install --build=MinSizeRel sdk-tag-$EMCC_SDK_VERSION-${EMCC_SDK_ARCH}bit \
\
    && mkdir -p /clang \
    && cp -r /emsdk_portable/clang/tag-e$EMCC_SDK_VERSION/build_tag-e${EMCC_SDK_VERSION}_${EMCC_SDK_ARCH}/bin /clang \
    && mkdir -p /clang/src \
    && cp /emsdk_portable/clang/tag-e$EMCC_SDK_VERSION/src/emscripten-version.txt /clang/src/ \
    && mkdir -p /emscripten \
    && cp -r /emsdk_portable/emscripten/tag-$EMCC_SDK_VERSION/* /emscripten \
    && cp -r /emsdk_portable/emscripten/tag-${EMCC_SDK_VERSION}_${EMCC_SDK_ARCH}bit_optimizer/optimizer /emscripten/ \
    && echo "import os\nLLVM_ROOT='/clang/bin/'\nNODE_JS='nodejs'\nEMSCRIPTEN_ROOT='/emscripten'\nEMSCRIPTEN_NATIVE_OPTIMIZER='/emscripten/optimizer'\nSPIDERMONKEY_ENGINE = ''\nV8_ENGINE = ''\nTEMP_DIR = '/tmp'\nCOMPILER_ENGINE = NODE_JS\nJS_ENGINES = [NODE_JS]\n" > ~/.emscripten \
    && rm -rf /emsdk_portable \
    && rm -rf /emscripten/tests \
    && rm -rf /emscripten/site \
    && for prog in em++ em-config emar emcc emconfigure emmake emranlib emrun emscons; do \
           ln -sf /emscripten/$prog /usr/local/bin; done \
    && apt-get -y --purge remove curl git-core build-essential gcc \
    && apt-get -y clean \
    && apt-get -y autoclean \
    && apt-get -y autoremove \
    && echo "Installed ... testing"
RUN emcc --version \
    && mkdir -p /tmp/emscripten_test && cd /tmp/emscripten_test \
    && printf '#include <iostream>\nint main(){std::cout<<"HELLO"<<std::endl;return 0;}' > test.cpp \
    && em++ -O2 test.cpp -o test.js && nodejs test.js \
    && em++ test.cpp -o test.js && nodejs test.js \
    && cd / \
    && rm -rf /tmp/emscripten_test \

VOLUME ["/src"]
WORKDIR /src

