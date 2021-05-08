  FROM kyori/ffmpeg:latest as ffmpeg
FROM kyori/avisynthplus-alpine:latest as avisynthplus

FROM alpine:3.13.2 as build

COPY --from=ffmpeg /output/ /tmp/output/
COPY --from=avisynthplus /output/ /tmp/output/

ENV DEV_PKGS="cmake curl gcc git g++ linux-headers make meson ninja pkgconfig" \
  PKG_CONFIG_PATH=/tmp/output/lib/pkgconfig:/tmp/output/lib64/pkgconfig

RUN apk add --no-cache ${DEV_PKGS} &&\
  # L-SMASH
  git clone https://github.com/l-smash/l-smash.git /tmp/l-smash -b v2.14.5 --depth=1 &&\
  cd /tmp/l-smash &&\
  ./configure --prefix=/tmp/output --enable-shared &&\
  make -j$(nproc) install &&\
  # L-SMASH-Works
  git clone https://github.com/HolyWu/L-SMASH-Works.git /tmp/L-SMASH-Works -b 20200728 --depth=1 &&\
  cd /tmp/L-SMASH-Works/AviSynth &&\
  LDFLAGS="-Wl,-Bsymbolic" meson --prefix=/tmp/output build &&\
  cd /tmp/L-SMASH-Works/AviSynth/build &&\
  ninja install &&\
  # Boost
  mkdir -p /tmp/boost &&\
  cd /tmp/boost &&\
  curl -sL https://boostorg.jfrog.io/artifactory/main/release/1.75.0/source/boost_1_75_0.tar.gz | tar -xz --strip-components=1 &&\
  ./bootstrap.sh --with-libraries=filesystem,program_options,system &&\
  ./b2 install &&\
  # JoinLogoScpTrialSetLinux
  git clone --recurse-submodules https://github.com/tobitti0/JoinLogoScpTrialSetLinux.git /tmp/jlse --depth=1 &&\
  cd /tmp/jlse/modules/chapter_exe/src &&\
  sed -i 's/\/usr\/local\//\/tmp\/output\//' Makefile &&\
  make -j$(nproc) &&\
  mkdir -p /output/opt/jlse/bin/ &&\
  cp chapter_exe /output/opt/jlse/bin/ &&\
  cd /tmp/jlse/modules/logoframe/src &&\
  sed -i 's/\/usr\/local\//\/tmp\/output\//' Makefile &&\
  make -j$(nproc) &&\
  cp logoframe /output/opt/jlse/bin/ &&\
  cd /tmp/jlse/modules/join_logo_scp/src &&\
  make -j$(nproc) &&\
  cp join_logo_scp /output/opt/jlse/bin/ &&\
  mkdir -p /tmp/jlse/modules/tsdivider/build &&\
  cd /tmp/jlse/modules/tsdivider/build &&\
  cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/output/opt/jlse .. &&\
  make -j$(nproc) install &&\
  sed -i 's/ext\s!==\s"\.ts"/ext !== ".m2ts"/g' /tmp/jlse/modules/join_logo_scp_trial/src/jlse.js &&\
  mkdir -p /output/usr/local/ &&\
  cp -r /tmp/output/* /output/usr/local/ &&\
  mkdir -p /output/root/.local/lib/ &&\
  cp -r /output/usr/local/lib64/avisynth /output/root/.local/lib/ &&\
  cp -r /tmp/jlse/modules/join_logo_scp_trial/src \
    /tmp/jlse/modules/join_logo_scp_trial/package.json \
    /tmp/jlse/modules/join_logo_scp_trial/yarn.lock /output/opt/jlse/ &&\
  cp /tmp/jlse/modules/join_logo_scp_trial/bin/lwinput.aui /output/opt/jlse/bin/ &&\
  rm -rf /tmp/* &&\
  apk del --no-cache ${DEV_PKGS}

FROM l3tnun/epgstation:alpine

ENV LD_LIBRARY_PATH=/usr/local/lib64

COPY --from=build /output/ /
RUN cd /opt/jlse &&\
  yarn --production --pure-lockfile &&\
  ln -s /opt/jlse/src/jlse.js /usr/local/bin/jlse &&\
  jlse --help
