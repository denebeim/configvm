FROM ubuntu
RUN apt update; apt install -y --no-install-recommends curl ca-certificates

RUN curl -s https://apt.rapidblock.org/install.sh | bash

VOLUME ["/opt/rapidblock"]

CMD /opt/rapidblock/bin/rapidblock
