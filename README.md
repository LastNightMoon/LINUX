# LINUX

port: 7890
socks-port: 7891
mixed-port: 7892
allow-lan: false
mode: rule
log-level: info

proxies:
  - name: "VLESS-Reality"
    type: vless
    server: 80.76.35.132
    port: 22959
    uuid: 7181ad56-c30b-4648-8ac7-893abac97b40
    flow: ""
    alterId: 0
    cipher: auto
    udp: true
    network: tcp
    tls: true
    reality-opts:
      public-key: A3FJ3xaHy0bFkIOYrqL7THJx5oXGDf1mXs20q-rzkEQ
      short-id: 6a4f
      spider-x: "/"
    servername: google.com
    fingerprint: chrome

proxy-groups:
  - name: "Auto"
    type: select
    proxies:
      - "VLESS-Reality"

rules:
  - MATCH, Auto

