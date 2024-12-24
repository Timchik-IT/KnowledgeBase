[[Лабы по сетям]]

**Важно для данной лабы использовать маршрутизаторы уровня 15.х и выше**
### Топология 
![[Pasted image 20241224150553.png]]

- **R1** - DHCPv6 сервер с делегированием префикса.
- **R2** - PD-клиент (Prefix Delegation).
- **R3** - Простой SLAAC-клиент.
- **R4** и **R5** - Простые SLAAC-клиенты.

### Настройка на R1
####  Настройка DHCPv6 сервера

1. **Создание пула для делегирования префикса:**
``` bash
R1(config)# ipv6 local pool LOCAL_POOL 3000:136::/48 48
```

2. **Настройка DHCPv6 сервера:**
``` bash
R1(config)# ipv6 dhcp pool DHCP_POOL
R1(config-dhcpv6)# address prefix-delegation pool LOCAL_POOL
R1(config-dhcpv6)# dns-server 2000:136::1
```

3. **Включение DHCPv6 на интерфейсе:**
``` bash
R1(config)# interface f0/0
R1(config-if)# ipv6 address 2000:136::1/64
R1(config-if)# ipv6 enable
R1(config-if)# ipv6 dhcp server DHCP_POOL
```

4. **Настройка SLAAC с префиксом 2000:136::/64:**
``` bash 
R1(config)# interface f0/0
R1(config-if)# ipv6 address 2000:136::1/64
R1(config-if)# ipv6 enable
R1(config-if)# ipv6 nd ra prefix 2000:136::/64
```

5. Не забываем указать, что R1 не конечный пользователь и прописываем юникаст ipv6 роутинг:
``` bash 
R1(config)# ipv6 unicast-routing
```

### Настройка PD-клиента (R2)

1. **Получение делегированного префикса:**
``` bash
R2(config)# interface f0/0
R2(config-if)# ipv6 enable
R2(config-if)# ipv6 dhcp client pd PDP
```

`PDP` - это как переменная в которую мы записываем наш префикс.

2. **Настройка SLAAC на левом интерфейсе:**
``` bash 
R2(config)# interface f0/1
R2(config-if)# ipv6 add au
```

3. **Проверка полученного префикса:**
``` bash
R2# show ipv6 interface brief
```
	Должен вывести DUID и полученный префикс.

4. Не забываем указать, что R2 так-же не конечный пользователь и прописываем юникаст ipv6 роутинг:
``` bash 
R2(config)# ipv6 unicast-routing
```

### Использование делегированного префикса на R2

1. **Формирование адресов на LAN интерфейсах:**
``` bash
R2(config)# interface f1/0
R2(config-if)# ipv6 address PDP 0.0.0.0.136::1/64
R2(config-if)# ipv6 enable

R2(config)# interface f1/1
R2(config-if)# ipv6 address PDP 0.0.0.0.137::1/64
R2(config-if)# ipv6 enable
```

### Настройка SLAAC-клиентов

```bash 
R4(config)# interface f0/0
R4(config-if)# ipv6 add au

R5(config)# interface f0/0
R5(config-if)# ipv6 add au

R3(config)# interface f0/0
R3(config-if)# ipv6 add au
```


### Включение О-флага на R1 и R2

``` bash
R1(config)# interface f0/0
R1(config-if)# ipv6 nd other-config-flag

R2(config)# interface f1/0
R2(config-if)# ipv6 nd other-config-flag

R2(config)# interface f1/1
R2(config-if)# ipv6 nd other-config-flag
```

После включения о - флага R3 сразу запросит дополнительный данные от dhcp сервера. R4 и R5 запросят, но не получат. Т.к надо настроить relay на R2.

``` bash 
R2(config)# interface f1/0
R2(config-if)# ipv6 dhcp relay destination 2000:136::1

R2(config)# interface f1/1
R2(config-if)# ipv6 dhcp relay destination 2000:136::1
```

После данной настройки каждый из маршрутизаторов - клиентов получат данный от dhcp сервера, в том числе dns сервер.