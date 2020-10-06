# VLAN и маршрутизация между VLAN
    
## Исходные данные:

#### Addressing Table

| Device  | Interface          |IP Address   |Subnet Mask  |Default Gateway|
| -------:|------------------:| -------------:| -----------:| :-------------|
| R1      | G0/0/1.3           | 192.168.3.1  |255.255.255.0| N/A           |
|         | G0/0/1.4           | 192.168.4.1  |255.255.255.0| N/A           |
|         | G0/0/1.8           | N/A          |N/A          | N/A           |
| S1      | VLAN 3             | 192.168.3.11 |255.255.255.0| 192.168.3.1   |
| S2      | VLAN 3             | 192.168.3.12 |255.255.255.0| 192.168.3.1   |
| PC-A    | NIC                | 192.168.3.3  |255.255.255.0| 192.168.3.1   |
| PC-B    | NIC                | 192.168.4.3  |255.255.255.0| 192.168.4.1   |

#### VLAN Table

| VLAN    | Name             |Interface Assigned   
| :-------|:-----------------| :-----------| 
| 3       | Management       | S1: VLAN 3  |
|         |                  | S2: VLAN 3  |
|         |                  | S1: F0/6    |
| 4       | Operations       | S2: F0/18   |
| 7       | ParkingLot       | S1: F0/2-4, F0/7-24, G0/1-2   |
|         |                  | S2: F0/2-17, F0/19-24, G0/1-2 |
| 8       | Native           | 192.168.3.3  |

## Задачи:

#### [Часть 1. Создание сети и настройка основных параметров устройства.](https://github.com/Misha-cook/otus-networks/edit/main/labs/lab01/README#L49)
#### Часть 2: Создание сетей VLAN и назначение портов коммутатора
#### Часть 3: Настройка магистрали 802.1Q между коммутаторами
#### Часть 4: Настройка маршрутизации между VLAN на маршрутизаторе
#### Часть 5: Убедиться, что маршрутизация между VLAN работает

## Топология:

#### Cхема лабораторного стенда, выполненная в draw.io:

![Image alt](https://github.com/Misha-cook/otus-networks/blob/main/labs/lab01/%D0%A1%D1%85%D0%B5%D0%BC%D0%B0.png)

#### Cхема лабораторного стенда, выполненная в Packet Tracer:

![Image alt](https://github.com/Misha-cook/otus-networks/blob/main/labs/lab01/%D0%A1%D1%85%D0%B5%D0%BC%D0%B0%20Packet%20Tracer.png)

## Выполнение задания:

#### Часть 1: Создание сети и настройка основных параметров устройства.

Основные настройки произведенные на R1 

```
(Команды выполнены в конфигурационном режиме)
hostname R1                              // переименуем маршрутизатор
!
no ip domain-lookup                      // отключите поиск DNS
!
enable secret ******                     //поставим пароль на вход в режим enable
!
aaa new-model //подключение модели AAA
!
aaa authentication login default local   // аутентификация происходит локально (с встроенной памяти)
!
username cisco privilege 15 secret cisco // устанавливаем логин и пароль для удаленного входа на маршрутизатор
!
banner motd #Unauthorized access to this device is prohibited! // установка баннера

Выходим из конфигурационного режима

clock set 13:12:00 23 october 2018      // установка времени
!
copy running-config startup-config      //сохранение конфигурации
```
Аналогичные команды заливаются на коммутаторы S1 и S2

#### Часть 2: Создание сетей VLAN и назначение портов коммутатора
- Создание VLAN 
```
(Команды выполнены в конфигурационном режиме)
vlan n                                  // создание vlan n
!
name Operation                          // устанавливаем название для vlan 
аналогично для остальных 
```
- Конфигурация управляющего интерфейса и шлюза по умолчанию
```
(управляющий интерфейс)
interface Vlan3
 description Management
 ip address 192.168.3.11 255.255.255.0
!
ip default-gateway 192.168.3.1           // шлюз по умолчанию
``` 
- Назначения VLAN 

|VLAN| Name |Status    |Ports|
| :-------|:-----------------| -----------:| -----------:| 
|1|    default    |                      active|        | 
|3|    Management |                      active|   Fa0/6|
|4|    Operations |                      active|        |
|7|    ParkingLot |                      active|    Fa0/2-4, Fa0/7-24, Gig0/1-2|
                                              
#### Часть 3: Настройка магистрали 802.1Q между коммутаторами
Рассмотрим настройку транка в сторону соседнего коммутатора
```
interface FastEthernet0/1                // интерфейс в сторону соседнего коммутатора
 description Link_R2                     // наименование интерфейса
 switchport trunk native vlan 8          // назначение Vlan 8 не тегированным 
 switchport trunk allowed vlan 3-4,8     // добавление vlan в trunk
 switchport mode trunk                   // обозначение интерфейса в виде trunk
```
#### Часть 4: Настройка маршрутизации между VLAN на маршрутизаторе
Рассмотрим настройку интерфейса interface FastEthernet0/0 в сторону коммутатора
```
interface FastEthernet0/0
 no ip address
 duplex auto
 speed auto
!
interface FastEthernet0/0.3               
 description vlan 3
 encapsulation dot1Q 3
 ip address 192.168.3.1 255.255.255.0
!
interface FastEthernet0/0.4
 description vlan 4
 encapsulation dot1Q 4
 ip address 192.168.4.1 255.255.255.0
!
interface FastEthernet0/0.8
 no ip address
```
#### Часть 5: Убедиться, что маршрутизация между VLAN работает

- ping с PC0 на шлюз: 

![Image alt](https://github.com/Misha-cook/otus-networks/blob/main/labs/lab01/1.png).

- ping с PC0 на PC1:

![Image alt](https://github.com/Misha-cook/otus-networks/blob/main/labs/lab01/2.png).

- ping с PC0 на S1:

![Image alt](https://github.com/Misha-cook/otus-networks/blob/main/labs/lab01/3.png).

#### -[Конфигурация сетевого оборудования](config/).
