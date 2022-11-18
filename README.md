## 6.4.1

A parte do laboratório que exige a configuração do PAgP não está no material da Cisco, portanto segue abaixo o modo de configuração:

```
SWA>en
SWA#configure terminal
SWA(config)#interface range g0/1-2
SWA(config-if-range)#channel-protocol pagp
SWA(config-if-range)#channel-group 1 mode disarable
SWA(config-if-range)#no shutdown
SWA(config-if-range)#exit
SWA(config)#interface port-channel 1
SWA(config-if)#switchport mode trunk
SWA(config-if)#exit

```

Outro detalhe importante ainda no 6.4.1 é que o Port Channel 3 deve ser configurado no SWB e no SWC, não no SWA como pede a atividade.