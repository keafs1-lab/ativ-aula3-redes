# ativ-aula3-redes
Relatório da prática de interfaces e endereçamento de rede feitas em sala 

## 1. SUMÁRIO

- **Aluno:** [Kauã Eder Augusto de Freitas SIlva]
    
- **Turma:** [ 913-A]
    
- **Matéria:** Laboratório de Sistemas Operacionais e Redes
    
- **Atividade:** Aula 03 — Interfaces e Endereçamento de Rede
    

## 2. Objetivo

O objetivo desta atividade foi entender como os computadores e as máquinas virtuais se conectam à rede, identificar seus endereços IP e testar a comunicação entre eles e com a internete

## 3. Ambiente utilizado

Para realizar os testes, utilizei:

- **Sistema principal:** Linux Mint MATE.
- **Máquina virtual:** Ubuntu Server.
- **Virtualização:** virt-manager com libvirt.
- **Conexão da VM:** NAT.
    

## 4. Procedimentos feitos:
FIZ UMA VERIFICAÇÃO fazendo uma consultas dentro da rede

Consultei os endereços IP, os endereços MAC, as rotas e o gateway. Depois, executei testes com `ping` para verificar a comunicação local, o acesso ao gateway e a conexão com a internet. Também utilizei `traceroute` para observar o caminho até um endereço externo.
Os resultados foram registrados neste relatório por meio de tabelas e da descrição dos testes, sem utilizar capturas de tela.

## 5. Testes e resultados

### 5.1. Linux Mint MATE

O Mint é o sistema principal, onde a máquina virtual está sendo executada.

|Informação|Resultado|
|---|---|
|Interface Wi-Fi|`wlp2s0`|
|IP do Mint|`172.16.20.220/16`|
|Gateway padrão|`172.16.0.1`|
|Interface da rede virtual|`virbr0`|
|IP da rede virtual|`192.168.122.1/24`|
|Rede virtual|`default` — ativa|

Também foi identificado o `docker0`, com endereço `172.17.0.1/16`, mas essa interface estava inativa e não foi utilizada nos testes da atividade.

**Teste do Mint para a VM:**

Comando utilizado:

`ping -c 4 192.168.122.40`

Resultado: 4 pacotes enviados, 4 recebidos e nenhuma perda.
Isso mostra que o Mint consegue se comunicar com o Ubuntu Server pela rede virtual.

### 5.2. Ubuntu Server

A máquina virtual utiliza a interface `enp1s0`, que estava ativa e recebeu seu endereço automaticamente pela rede virtual.

|Informação|Resultado|
|---|---|
|Interface|`enp1s0`|
|IP da VM|`192.168.122.40/24`|
|Máscara|`255.255.255.0`|
|Endereço MAC|`52:54:00:ad:2e:8e`|
|Gateway|`192.168.122.1`|
|DNS configurado|`192.168.122.1`|
|Rede|`192.168.122.0/24`|
|Broadcast|`192.168.122.255`|

O endereço IP identifica a máquina na rede. A máscara indica o tamanho da rede, enquanto o endereço MAC identifica a interface de rede. O gateway permite que a VM encaminhe tráfego para outras redes.

### 5.3. Testes de conectividade na VM

|Destino|Pacotes recebidos|Perda|Resultado|
|---|---|---|---|
|`127.0.0.1`|4 de 4|0%|Comunicação local funcionando|
|`192.168.122.40`|4 de 4|0%|A VM respondeu ao próprio IP|
|`192.168.122.1`|4 de 4|0%|Comunicação com o gateway funcionando|
|`8.8.8.8`|3 de 4|25%|Houve perda de um pacote|
|`google.com`|3 de 4|25%|O nome foi resolvido, mas houve perda de um pacote|

Os testes locais funcionaram sem perda de pacotes. Já nos testes externos, um pacote não recebeu resposta em cada execução.
O teste com `google.com` mostrou que o nome foi convertido para um endereço IP, então a resolução DNS funcionou naquele momento. A perda de pacotes, sozinha, não permite identificar a causa.

### 5.4. Caminho da conexão

Executei o comando espécifico:

`traceroute 8.8.8.8`

O primeiro salto foi `192.168.122.1`, que é o gateway da rede virtual no Mint. Os saltos seguintes exibidos não responderam às sondagens.
Isso não significa necessariamente que a internet esteja sem conexão, pois alguns equipamentos não respondem a esse tipo de teste.

### 5.5. Comparação entre o Mint e a VM

|Item|Linux Mint MATE|Ubuntu Server|
|---|---|---|
|IP|`172.16.20.220/16`|`192.168.122.40/24`|
|Interface principal|`wlp2s0`|`enp1s0`|
|Gateway|`172.16.0.1`|`192.168.122.1`|
|Conexão|Wi-Fi|Rede virtual NAT|

Os dois sistemas estão em redes diferentes. O libvirt utiliza a rede `192.168.122.0/24` para conectar a VM ao sistema principal e permitir o acesso externo por NAT daí 
nesse ambiente, o Mint consegue acessar a VM pelo IP `192.168.122.40`. Portanto, o comportamento observado é diferente do exemplo de NAT do VirtualBox apresentado no roteiro da atividade.

## 6. Problemas e soluções

Durante a atividade, tentei mudar a conexão da VM para uma interface ligada ao Wi-Fi. Depois da alteração, a máquina virtual perdeu a conectividade.
TENTEI até recuperar acesso à configuração NAT padrão do libvirt. A conexão voltou a funcionar e os testes puderam continuar.
A tentativa de usar essa outra configuração não foi concluída com sucesso, por isso não registrei o modo Bridge como se tivesse funcionado.
Também houve perda de pacotes nos testes externos. Como os testes locais funcionaram normalmente, registrei os resultados sem afirmar uma causa que não foi confirmada.

## 7. Conclusão

Com esta atividade, consegui identificar os endereços de rede do Mint e do Ubuntu Server e entender melhor a função do IP, da máscara, do MAC e do gateway.

Os testes mostraram que a comunicação entre o sistema principal e a máquina virtual funciona pela rede NAT do libvirt. Também observei que os testes externos tiveram alguma perda de pacotes e que o `traceroute` não recebeu resposta de todos os saltos.

Por fim, aprendi que a configuração da rede virtual influencia a comunicação entre a VM, o sistema principal e a internet.
