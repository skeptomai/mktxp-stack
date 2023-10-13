## General links

* [MKTXP Stack](https://github.com/akpw/mktxp-stack)
* [Graphite Exporter (from TrueNAS to Prometheus)](https://github.com/prometheus/graphite_exporter)
* [Syslog relay for Loki](https://alexandre.deverteuil.net/post/syslog-relay-for-loki/)
* [Monitoring TrueNAS with Prom & Loki](https://alexandre.deverteuil.net/post/monitoring-truenas-with-prometheus-and-loki/)
* [FreeNAS MIB](https://mibs.observium.org/mib/FREENAS-MIB/)
* [TrueNAS bespoke alerts](https://medium.com/nerd-for-tech/truenas-bespoke-alerts-e8f91e3de5c1)
* [Sending syslog alerts to Discord](https://www.syslog-ng.com/community/b/blog/posts/first-steps-of-sending-alerts-to-discord-and-others-from-syslog-ng-http-and-apprise)

## To debug a malfunctioning container.. like the graphite-exporter

1. Create an image from the container and start a new container from that image. This preserves ports and volumes

    ```
    docker commit graphite-exporter cb/poopstain

    docker run -ti -p 9109:9109 -p 9108:9108 -p 2003:2003 -p 9109:9109/udp -p 9108:9108/udp -p 2003:2003/udp -v ./graphite-exporter/graphite-exporter.yml:/tmp/graphite-exporter.yml:ro  --entrypoint=/bin/sh cb/poopstain
    ```

2. In the container, start the offending app by hand (from terminal)

    ```
    bin/graphite_exporter --graphite.mapping-config=/tmp/graphite-exporter.yml --graphite.listen-address=:2003
    ```

3. Or run a new container with a complete launch line, ports, volumes and everything

    ```
    docker run -ti -p 9108:9108 -p 9109:9109 -p 9109:9109/udp -p 2003:2003 -p 2003:2003/udp \
            -v ${PWD}/graphite-exporter/graphite-exporter.yml:/tmp/graphite-exporter.yml \
            prom/graphite-exporter --graphite.mapping-config=/tmp/graphite-exporter.yml --graphite.listen-address=:2003
    ```