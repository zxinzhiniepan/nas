FROM alpine:latest

LABEL maintainer="zxinzhiniepan@outlook.com"

ENV APPATH=${APPATH:-"/samba"}

WORKDIR ${APPATH}

ENV TZ=${TZ:-Asia/Shanghai}

COPY ./* ${APPATH}

RUN chmod +x ./* && \
    ./build.sh

EXPOSE 137/udp 138/udp 139 445

VOLUME [ "/config" ]

ENTRYPOINT ["/bin/bash", "start-up.sh" ]
CMD [ "smbd", "-F", "--debug-stdout", "--no-process-group" ]

HEALTHCHECK --interval=30s --timeout=10s \
  CMD smbclient -L \\localhost -U % -m SMB3
