
# s01 – Лаборатория: топология

## Среда
- Хост: MacBook Air (Apple Silicon M4, 16 GB RAM)
- Хипервайзор: VMware Fusion Pro (ARM64)
- Гост ОС: Debian 13 (Trixie) arm64, без графична среда
- Потребител: `student`

## Диаграма

```text
                 Интернет
                     |
            [ VMware NAT – WAN ]
                     |
                    wan (DHCP)
               +-----------+
               |    gw     |  ip_forward=1, NAT (masquerade) към wan
               +-----------+
        lan1 192.168.10.1   lan2 192.168.20.1
              |                   |
   [ LAN1 – vmnet3 ]       [ LAN2 – vmnet4 ]
   192.168.10.0/24         192.168.20.0/24
              |
        lan1 192.168.10.10
        +-----------+
        |    srv    |
        +-----------+
```

## Виртуални машини

| VM  | vCPU | RAM  | Диск  | Адаптери |
|-----|------|------|-------|----------|
| gw  | 1    | 1 GB | 10 GB | 3 (wan, lan1, lan2) |
| srv | 2    | 4 GB | 20 GB | 1 (lan1) |

## Сегменти и адресация

| Сегмент | Fusion мрежа | Подмрежа | gw | srv |
|---------|--------------|----------|----|-----|
| WAN  | Share with my Mac (NAT) | DHCP от VMware | wan – DHCP | – |
| LAN1 | vmnet3 (без DHCP, без хост) | 192.168.10.0/24 | 192.168.10.1 | 192.168.10.10 (GW 192.168.10.1) |
| LAN2 | vmnet4 (без DHCP, без хост) | 192.168.20.0/24 | 192.168.20.1 | – |

Интерфейсите са преименувани по MAC адрес чрез `systemd .link` файлове (`/etc/systemd/network/`).
## Конгигурация
GW:/etc/network/interfaces configs/gw/interfaces
interfaces                                                  100%  221   336.7KB/s   00:00
GW:/etc/systemd/network/*.link" configs/gw/
10-wan.link                                                 100%   54    76.5KB/s   00:00    
11-lan1.link                                                100%   55    80.3KB/s   00:00    
12-lan2.link                                                100%   55   111.4KB/s   00:00   
GW:/etc/sysctl.d/99-ip-forward.conf configs/gw/
99-ip-forward.conf                                          100%   22    46.7KB/s   00:00
GW:/etc/nftables.conf configs/gw/
nftables.conf                                               100%  180   317.9KB/s   00:00 
SRV::/etc/network/interfaces configs/srv/interfaces
interfaces                                                  100%  149   154.8KB/s   00:00 
SRV:/etc/systemd/network/*.link" configs/srv/
10-lan1.link                                                100%   55    72.7KB/s   00:00
