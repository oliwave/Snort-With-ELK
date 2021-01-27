FROM ubuntu:latest

WORKDIR /snort_src

# system is up to date and has the latest list of packages
RUN apt-get update && apt-get dist-upgrade -y \
&& apt-get install tzdata && dpkg-reconfigure -f noninteractive tzdata \

&& apt-get install -y net-tools build-essential autotools-dev libdumbnet-dev \
libluajit-5.1-dev libpcap-dev zlib1g-dev pkg-config libhwloc-dev \
cmake liblzma-dev openssl libssl-dev cpputest libsqlite3-dev uuid-dev \
asciidoc dblatex source-highlight w3m libtool git autoconf \
bison flex libcmocka-dev libnetfilter-queue-dev libmnl-dev wget \

&& wget https://github.com/rurban/safeclib/releases/download/v08112019/libsafec-08112019.0-gad76c7.tar.gz \ 
&& tar -xzvf libsafec-08112019.0-gad76c7.tar.gz \
&& wget https://ftp.pcre.org/pub/pcre/pcre-8.43.tar.gz \
&& tar -xzvf pcre-8.43.tar.gz \
&& wget https://github.com/gperftools/gperftools/releases/download/gperftools-2.7.90/gperftools-2.7.90.tar.gz \
&& tar xzvf gperftools-2.7.90.tar.gz \
&& wget http://www.colm.net/files/ragel/ragel-6.10.tar.gz \
&& tar -xzvf ragel-6.10.tar.gz \
&& wget https://dl.bintray.com/boostorg/release/1.72.0/source/boost_1_72_0.tar.gz \
&& tar -xvzf boost_1_72_0.tar.gz \
&& wget https://github.com/intel/hyperscan/archive/v5.2.1.tar.gz \
&& tar -xvzf v5.2.1.tar.gz \
&& wget https://github.com/google/flatbuffers/archive/v1.12.0.tar.gz -O flatbuffers-v1.12.0.tar.gz \
&& tar -xzvf flatbuffers-v1.12.0.tar.gz \
&& git clone https://github.com/snort3/libdaq.git \
&& git clone git://github.com/snortadmin/snort3.git \

&& cd libsafec-08112019.0-gad76c7 && ./configure && make && make install && cd .. \
&& cd pcre-8.43 && ./configure && make && make install && cd .. \
&& cd gperftools-2.7.90 && ./configure && make && make install && cd .. \
&& cd ragel-6.10 && ./configure && make && make install && cd .. \

&& mkdir hyperscan-5.2.1-build && cd hyperscan-5.2.1-build \
&& cmake -DCMAKE_INSTALL_PREFIX=/usr/local -DBOOST_ROOT=/snort_src/boost_1_72_0/ ../hyperscan-5.2.1 \
&& make && make install && cd .. \
&& mkdir flatbuffers-build && cd flatbuffers-build && cmake ../flatbuffers-1.12.0 && make && make install && cd .. \
&& cd libdaq && ./bootstrap && ./configure && make && make install && ldconfig && cd .. \
&& cd snort3 && ./configure_cmake.sh --prefix=/usr/local --enable-tcmalloc && cd build && make && make install \
&& cd ../../

# OpenAppID
RUN wget https://snort.org/downloads/openappid/15607 -O OpenAppId-12159.tgz \
&& tar -xzvf OpenAppId-12159.tgz \
&& cp -R odp /usr/local/lib/ \
&& sed -i "93 a app_detector_dir = \'/usr/local/lib\'," /usr/local/etc/snort/snort.lua

# Configure log file
RUN mkdir /var/log/snort \
&& sed -i "94 a log_stats = true," /usr/local/etc/snort/snort.lua \
# && sed -i "236 a alert_csv = { file = true, }" /usr/local/etc/snort/snort.lua
&& sed -i "236 a alert_json = { file = true, limit = 10, fields='seconds action class b64_data dir dst_addr \
icmp_id icmp_seq icmp_type iface ip_id ip_len msg mpls pkt_gen \
pkt_len pkt_num priority proto rev rule service sid src_addr \
src_ap src_port target tcp_ack tcp_flags tcp_len tcp_seq \
tcp_win tos ttl udp_len vlan timestamp', \
}" /usr/local/etc/snort/snort.lua

## TEST OpenAppID
RUN mkdir /usr/local/etc/rules && touch /usr/local/etc/rules/local.rules \
&& echo "alert tcp any any -> any any ( msg:\"Facebook Detected\"; appids:\"Facebook\";sid:10000001; )\nalert icmp any any -> any any (msg:\"ICMP Traffic Detected\";sid:10000002;) " \
> /usr/local/etc/rules/local.rules \
&& wget https://download.netresec.com/pcap/maccdc-2012/maccdc2012_00000.pcap.gz \
&& gunzip maccdc2012_00000.pcap.gz
