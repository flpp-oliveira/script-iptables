# Script-iptables

Este conjunto de regras tem como objetivo proteger o sistema contra diversos tipos de ataques e vulnerabilidades de segurança. 

### OBSERVAÇÕES
- O script foi testado no Ubuntu 22.04
- A versão do IPTABLES utilizada foi v1.8.7
- O script tem como função a segurança para uso pessoal, podendo ser adaptado para servidores de clientes.

### Permissão do script para ser executado apenas pelo dono do script

chmod 700 iptables-script.sh

## Explicação de cada conjunto de regras:

- Bloqueia conexões de entrada ssh

Este comando bloqueia qualquer pacote que chegue à máquina com o protocolo TCP e porta 22, que é a porta padrão do serviço SSH.

```
iptables -A INPUT -p tcp --dport 22 -j DROP
```

- Permite conexões de saída ssh

Este comando permite que a máquina inicie conexões com outros sistemas usando o protocolo TCP e porta 22 (serviço SSH).

```
iptables -A OUTPUT -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
```

- bloqueia trafego não solicitado

Este comando bloqueia pacotes com o estado "INVALID", que são aqueles que não foram solicitados pelo sistema.

```
iptables -A INPUT -m state --state INVALID -j DROP
```

- Bloqueia ataques de negação de serviço DoS e Ping Flood

Este conjunto de regras bloqueia ataques de negação de serviço (DoS) e Ping Flood. A primeira regra permite um pacote "echo-request" (ping) a cada segundo, enquanto a segunda regra bloqueia qualquer pacote adicional "echo-request" além desse limite.

```
iptables -A INPUT -p icmp --icmp-type echo-request -m limit --limit 1/s -j ACCEPT
iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
```

- Bloqueia ataques de spoofing

Este conjunto de regras bloqueia ataques de spoofing, que são ataques em que um invasor falsifica o endereço IP de origem de um pacote. As regras bloqueiam pacotes de endereços IP reservados para uso local e endereços IP não atribuídos.

```
iptables -A INPUT -s 127.0.0.0/8 -j DROP
iptables -A INPUT -s 169.254.0.0/16 -j DROP
iptables -A INPUT -s 172.16.0.0/12 -j DROP
iptables -A INPUT -s 192.0.2.0/24 -j DROP
iptables -A INPUT -s 224.0.0.0/4 -j DROP
iptables -A INPUT -d 224.0.0.0/4 -j DROP
iptables -A INPUT -s 240.0.0.0/5 -j DROP
iptables -A INPUT -d 240.0.0.0/5 -j DROP
iptables -A INPUT -s 0.0.0.0/8 -j DROP
iptables -A INPUT -d 0.0.0.0/8 -j DROP
iptables -A INPUT -d 239.255.255.0/24 -j DROP
iptables -A INPUT -d 255.255.255.255 -j DRO
```

Esses IPs foram bloqueados usando iptables porque eles estão em sub-redes reservadas ou não utilizadas na Internet. Essas sub-redes são geralmente reservadas para uso especial, como testes de rede, endereços multicast e endereços privados não roteáveis.

Algumas dessas sub-redes são:

    - 127.0.0.0/8: este é o endereço de loopback, usado para se comunicar com o próprio computador.

    - 169.254.0.0/16: este é o endereço de link local, usado para comunicação entre dispositivos na mesma rede local sem passar pelo roteador.

    - 172.16.0.0/12: este é um bloco de endereços privados, usado para comunicação entre dispositivos em redes privadas, sem acesso à Internet.

    - 192.0.2.0/24: este é um bloco de endereços de teste reservados, usados ​​para testes de rede e documentação.

    - 224.0.0.0/4: este é o endereço de multicast, usado para transmitir pacotes para vários destinos em uma rede.

    - 240.0.0.0/5: este é um bloco de endereços reservados para uso futuro.

    - 0.0.0.0/8: este é um endereço de destino inválido, usado para indicar uma rota padrão.

    - 255.255.255.255: este é o endereço de broadcast, usado para transmitir pacotes para todos os dispositivos em uma rede.

Bloquear esses endereços de IP pode ajudar a aumentar a segurança de uma rede, já que eles são geralmente considerados inseguros para se comunicar com a Internet ou com outros dispositivos na rede

- Bloqueia ataques de SYN Flood

```
iptables -A INPUT -p tcp --syn -m limit --limit 1/s -j ACCEPT
iptables -A INPUT -p tcp --syn -j DROP
```

## Salvar no iptables rules

Este comando salva as regras atuais do iptables em um arquivo de regras persistente, para que as regras sejam aplicadas automaticamente a cada vez que o sistema for reiniciado.

```
iptables-save > /etc/iptables.rules
```
