FROM ubuntu
RUN apt-get update; apt-get install -y --no-install-recommends curl ca-certificates

RUN curl -s https://apt.rapidblock.org/install.sh | bash

VOLUME ["/opt/rapidblock"]

COPY rapidblock.env /etc/default/rapidblock

CMD /opt/rapidblock/scripts/cron.sh
