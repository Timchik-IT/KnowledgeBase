[[Лабы по сетям]]

### Топология 
![[Pasted image 20241224144752.png]]

### Адресация

#### 1. Подключение ПК к коммутаторам
##### SW1:
- **Ethernet0/0:** Подключен к PC1 (VLAN N)
- **Ethernet0/1:** Подключен к PC2 (VLAN 1)
- **Ethernet0/2:** Подключен к PC3 (VLAN N)
##### SW2:
- **Ethernet0/0:** Подключен к PC4 (VLAN 1)
- **Ethernet0/1:** Подключен к PC5 (VLAN 2N)
- **Ethernet0/2:** Подключен к PC6 (VLAN 2N)

#### 2. Подключение коммутаторов между собой
##### SW1:
- **Ethernet0/3:** Подключен к SW2 (транковый порт)
- **Ethernet1/0:** Подключен к SW2 (порт доступа для VLAN 2N)
##### SW2:
- **Ethernet0/3:** Подключен к SW1 (транковый порт)
- **Ethernet1/0:** Подключен к SW1 (порт доступа для VLAN 2N)


### Настройка коммутаторов

#### Шаг 1: Создание VLAN

На обоих коммутаторах создайте необходимые VLAN:
``` bash
SW1(config)# vlan N
SW1(config-vlan)# name VLAN_N
SW1(config)# vlan 2N
SW1(config-vlan)# name VLAN_2N

SW2(config)# vlan N
SW2(config-vlan)# name VLAN_N
SW2(config)# vlan 2N
SW2(config-vlan)# name VLAN_2N
```

#### Шаг 2: Настройка портов доступа

Настройте порты доступа для каждого коммутатора:

``` bash
SW1(config)# interface Ethernet0/0
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan N

SW1(config)# interface Ethernet0/1
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 1

SW1(config)# interface Ethernet0/2
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan N

SW2(config)# interface Ethernet0/0
SW2(config-if)# switchport mode access
SW2(config-if)# switchport access vlan 1

SW2(config)# interface Ethernet0/1
SW2(config-if)# switchport mode access
SW2(config-if)# switchport access vlan 2N

SW2(config)# interface Ethernet0/2
SW2(config-if)# switchport mode access
SW2(config-if)# switchport access vlan 2N
```

#### Шаг 3: Настройка транкового линка

Настройте первый линк между коммутаторами как транковый:

``` bash 
SW1(config)# interface Ethernet0/3
SW1(config-if)# switchport mode trunk
SW1(config-if)# switchport trunk encapsulation dot1q
SW1(config-if)# switchport trunk native vlan N
SW1(config-if)# switchport trunk allowed vlan 1,N

SW2(config)# interface Ethernet0/3
SW2(config-if)# switchport mode trunk
SW2(config-if)# switchport trunk encapsulation dot1q
SW2(config-if)# switchport trunk native vlan N
SW2(config-if)# switchport trunk allowed vlan 1,N
```

#### Шаг 4: Настройка порта доступа для VLAN 2N

Настройте второй линк между коммутаторами как порт доступа для VLAN 2N:

``` bash 
SW1(config)# interface Ethernet1/0
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 2N

SW2(config)# interface Ethernet1/0
SW2(config-if)# switchport mode access
SW2(config-if)# switchport access vlan 2N
```

### Настройка VPCS

#### Назначение IP-адресов

Назначьте IP-адреса узлам в соответствии с заданием:
- PC1: 192.168.N.1/24
- PC2: 192.168.N.2/24
- PC3: 192.168.N.3/24
- PC4: 192.168.N.4/24
- PC5: 192.168.N.5/24
- PC6: 192.168.N.6/24

Команда для назначения 
``` bash
PC1> ip <адресс>/<маска>
```

### Проверка конфигурации

Для проверки конфигурации используйте следующие команды:
``` bash 
SW1# show vlan
SW1# show interfaces trunk
SW1# show running-config

SW2# show vlan
SW2# show interfaces trunk
SW2# show running-config
```

### Сохранение конфигурации

Не забудьте сохранить конфигурацию на обоих коммутаторах:
``` bash
SW1# write memory
SW2# write memory
```

### Небольшая справочка 

- **VLAN** позволяет логически разделить сеть на изолированные сегменты.
- **Транковый линк** используется для передачи трафика нескольких VLAN между коммутаторами или маршрутизаторами.
- **Тегирование кадров** позволяет идентифицировать, к какому VLAN принадлежит кадр.
- **Native VLAN** — это VLAN, для которого тегирование не применяется.
- **Разрешенные VLAN** определяют, какие VLAN могут проходить через транковый линк.