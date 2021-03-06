FROM openms/library as library
ENV DEBIAN_FRONTEND noninteractive

WORKDIR /openms-build

RUN apt-get -y update
RUN apt-get install -y --no-install-recommends --no-install-suggests binutils subversion

RUN for l in lib/*.so; do strip --strip-all $l; done

RUN mkdir /opt/thirdparty && \
    svn checkout https://github.com/OpenMS/THIRDPARTY/trunk/Linux/64bit /opt/thirdparty && \
    rm -rf /opt/thirdparty/.svn && \
    svn checkout https://github.com/OpenMS/THIRDPARTY/trunk/All /opt/thirdparty && \
    rm -rf /opt/thirdparty/.svn

ENV PATH /opt/thirdparty/Fido:/opt/thirdparty/LuciPHOr2:/opt/thirdparty/MSGFPlus:/opt/thirdparty/MyriMatch:/opt/thirdparty/OMSSA:/opt/thirdparty/XTandem:$PATH

RUN make TOPP && make UTILS  

FROM ubuntu:14.04
ENV DEBIAN_FRONTEND noninteractive

LABEL software="OpenMS batteries included"
LABEL software.version="develop"
LABEL website="https://www.OpenMS.de"
LABEL documentation="https://www.OpenMS.de/documentation/"
LABEL tags="proteomics, mass spectrometry"
LABEL license="https://raw.githubusercontent.com/OpenMS/OpenMS/develop/License.txt"
LABEL base.image="ubuntu:14.04"

RUN mkdir /OpenMS
WORKDIR /OpenMS

COPY --from=library /OpenMS/AUTHORS .
COPY --from=library /OpenMS/CHANGELOG .
COPY --from=library /OpenMS/LICENSE .
COPY --from=library /OpenMS/README .
COPY --from=library /OpenMS/share/OpenMS/CHEMISTRY /OpenMS/share/OpenMS/CHEMISTRY
COPY --from=library /OpenMS/share/OpenMS/CV /OpenMS/share/OpenMS/CV
COPY --from=library /OpenMS/share/OpenMS/MAPPING /OpenMS/share/OpenMS/MAPPING
COPY --from=library /OpenMS/share/OpenMS/SCHEMAS /OpenMS/share/OpenMS/SCHEMAS
COPY --from=library /OpenMS/share/OpenMS/XSL /OpenMS/share/OpenMS/XSL
COPY --from=library /OpenMS/share/OpenMS/SCRIPTS /OpenMS/share/OpenMS/SCRIPTS

COPY --from=library /openms-build/bin /OpenMS/bin
COPY --from=library /openms-build/lib /OpenMS/lib

COPY --from=library /opt/thirdparty /opt/thirdparty

RUN apt-get -y update
RUN apt-get install -y --no-install-recommends --no-install-suggests libsvm-dev \
								      libglpk-dev \
								      libzip-dev \
								      zlib1g-dev \
							              libxerces-c-dev \
                                                                     libbz2-dev
RUN apt-get install -y --no-install-recommends --no-install-suggests libboost-date-time1.54-dev \
                                                                     libboost-iostreams1.54-dev \
                                                                     libboost-regex1.54-dev \
                                                                     libboost-math1.54-dev \
                                                                     libboost-random1.54-dev
RUN apt-get install -y --no-install-recommends --no-install-suggests libqtcore4 \
								      libqt4-network
RUN apt-get install -y --no-install-recommends --no-install-suggests default-jre				      
RUN apt-get autoremove -y && \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /var/lib/{cache,log}

#example input for kicking tires
#COPY --from=library /OpenMS/share/OpenMS/examples /OpenMS/share/OpenMS/examples

WORKDIR /
ENV PATH="/OpenMS/bin/:${PATH}"
ENV LD_LIBRARY_PATH="/OpenMS/lib/:${LD_LIBRARY_PATH}"
ENV PATH /opt/thirdparty/Fido:/opt/thirdparty/LuciPHOr2:/opt/thirdparty/MSGFPlus:/opt/thirdparty/MyriMatch:/opt/thirdparty/OMSSA:/opt/thirdparty/XTandem:/opt/thirdparty/OMSSA:/opt/thirdparty/Comet:/opt/thirdparty/OMSSA:/opt/thirdparty/Percolator:/opt/thirdparty/OMSSA:/opt/thirdparty/SpectraST:${PATH}
ENV OPENMS_DATA_PATH /OpenMS/share/OpenMS

RUN useradd -u 1001 -U -d /home/OpenMSuser -m -s /bin/bash OpenMSuser
USER OpenMSuser
ENV HOME /home/OpenMSuser
WORKDIR /home/OpenMSuser