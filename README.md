# LINUX


port: 7890
socks-port: 7891
mixed-port: 7892
log-level: info

proxies:
  - name: "V2Ray Server"
    type: v2ray
    server: <V2Ray_IP>
    port: <V2Ray_Port>
    uuid: <Your_UUID>
    alterId: <Your_AlterId>
    cipher: auto

proxy-groups:
  - name: "Auto"
    type: select
    proxies:
      - "V2Ray Server"

rules:
  - MATCH, Auto
