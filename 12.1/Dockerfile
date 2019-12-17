FROM postgres:12.1
RUN apt update; \
    apt install -y git postgresql-server-dev-12 gcc make; \
    cd "/root"; \
    git clone 'https://github.com/zilder/pg_dropcache.git'; \
    cd pg_dropcache; \
    make install USE_PGXS=1; \
    apt remove -y git postgresql-server-dev-12 gcc make; \
    apt autoremove -y; \
    cd /; \
    rm -r /root/pg_dropcache;
