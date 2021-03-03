FROM denismakogon/ffmpeg-alpine:4.0-buildstage as ffmpeg

FROM l3tnun/epgstation:alpine

COPY --from=ffmpeg /tmp/fakeroot/bin /usr/local/bin
COPY --from=ffmpeg /tmp/fakeroot/share /usr/local/share
COPY --from=ffmpeg /tmp/fakeroot/include /usr/local/include
COPY --from=ffmpeg /tmp/fakeroot/lib /usr/local/lib
