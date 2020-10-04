# VLAN и маршрутизация между VLAN
    
## Исходные данные:

#### Addressing Table

| Device  | Interface          |IP Address   |Subnet Mask  |Default Gateway|
| -------:|------------------:| -------------:| -----------:| -------------:|
| R1      | G0/0/1.3           | 192.168.3.1  |255.255.255.0| N/A           |
|         | G0/0/1.4           | 192.168.4.1  |255.255.255.0| N/A           |
|         | G0/0/1.8           | N/A          |N/A          | N/A           |
| S1      | VLAN 3             | 192.168.3.11 |255.255.255.0| 192.168.3.1   |
| S2      | VLAN 3             | 192.168.3.12 |255.255.255.0| 192.168.3.1   |
| PC-A    | NIC                | 192.168.3.3  |255.255.255.0| 192.168.3.1   |
| PC-B    | NIC                | 192.168.4.3  |255.255.255.0| 192.168.4.1   |

#### VLAN Table

| VLAN    | Name             |Interface Assigned   
| -------:|-----------------:| ------------:| 
| 3       | Management       | S1: VLAN 3  |
|         |                  | S2: VLAN 3  |
|         |                  | S1: F0/6    |
| 4       | Operations       | S2: F0/18   |
| 7       | ParkingLot       | S1: F0/2-4, F0/7-24, G0/1-2   |
|         |                  | S2: F0/2-17, F0/19-24, G0/1-2 |
| 8       | Native           | 192.168.3.3  |

Cхема лабораторного стенда, выполненная в draw.io:

![Image alt](https://github.com/Misha-cook/otus-networks/blob/main/labs/lab01/%D0%A1%D1%85%D0%B5%D0%BC%D0%B0.png)

Cхема лабораторного стенда, выполненная в Packet Tracer:

![Image alt](https://github.com/Misha-cook/otus-networks/blob/main/labs/lab01/%D0%A1%D1%85%D0%B5%D0%BC%D0%B0%20Packet%20Tracer.png)

