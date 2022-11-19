## 6.4.1

A parte do laboratório que exige a configuração do PAgP não está no material da Cisco, portanto segue abaixo o modo de configuração:

```
SWA>en
SWA#configure terminal
SWA(config)#interface range g0/1-2
SWA(config-if-range)#channel-protocol pagp
SWA(config-if-range)#channel-group 1 mode desirable
SWA(config-if-range)#no shutdown
SWA(config-if-range)#exit
SWA(config)#interface port-channel 1
SWA(config-if)#switchport mode trunk
SWA(config-if)#exit

```

Outro detalhe importante ainda no 6.4.1 é que o Port Channel 3 deve ser configurado no SWB e no SWC, não no SWA como pede a atividade.

## 11.6.1

Obs: esse lab deu um pouco de trabalho, então vou deixar um guia de como configurar os dispositivos de acordo com o que está sendo pedido

### Step 1: Create a Secure Trunk

a. Connect the G0/2 ports of the two access layer switches.

    Apenas estenda um cabo de cobre direto entre as interfaces G0/2 do SW-1 e do SW-2

b. Configure ports G0/1 and G0/2 as static trunks on both switches.

c. Disable DTP negotiation on both sides of the link.

d. Create VLAN 100 and give it the name Native on both switches.

e. Configure all trunk ports on both switches to use VLAN 100 as the native VLAN.

    SW-1 and SW-2

    SW-1/SW-2(config)#interface range G0/1 - 2
    SW-1/SW-2(config-if-range)#switchport mode trunk
    SW-1/SW-2(config-if-range)#switchport nonegotiate
    SW-1/SW-2(config-if-range)#
    SW-1/SW-2(config-if-range)#vlan 100
    SW-1/SW-2(config-vlan)#name Native
    SW-1/SW-2(config-vlan)#
    SW-1/SW-2(config-vlan)#interface range G0/1 - 2
    SW-1/SW-2(config-if-range)#switchport trunk native vlan 100

### Step 2: Secure Unused Switchports

a. Shutdown all unused switch ports on SW-1.

b. On SW-1, create a VLAN 999 and name it BlackHole. The configured name must match the requirement exactly.

c. Move all unused switch ports to the BlackHole VLAN.

    SW-1(config)#interface range F0/3-9, F0/11-23
    SW-1(config-if-range)#shutdown
    SW-1(config-if-range)#exit

    SW-1(config)#vlan 999
    SW-1(config-vlan)#name BlackHole
    SW-1(config-vlan)#exit

    SW-1(config)#interface range F0/3-9, F0/11-23
    SW-1(config-if-range)#switchport access vlan 999

### Step 3: Implement Port Security

a. Activate port security on all the active access ports on switch SW-1.

    SW-1(config)#interface range F0/1, F0/2, F0/10,F0/24
    SW-1(config-if-range)#switchport mode access
    SW-1(config-if-range)#switchport port-security

b. Configure the active ports to allow a maximum of 4 MAC addresses to be learned on the ports.

    SW-1(config)#interface range F0/1, F0/2, F0/10,F0/24
    SW-1(config-if-range)#switchport port-security maximum 4

c. For ports F0/1 on SW-1, statically configure the MAC address of the PC using port security.

    SW-1(config)#interface F0/1
    SW-1(config-if)#switchport port-security mac-address 0010.11E8.3CBB

d. Configure each active access port so that it will automatically add the MAC addresses learned on the port to the running configuration.

    SW-1(config)#interface range F0/1, F0/2, F0/10,F0/24
    SW-1(config-if-range)#switchport port-security mac-address sticky

e. Configure the port security violation mode to drop packets from MAC addresses that exceed the maximum, generate a Syslog entry, but not disable the ports.

    SW-1(config)#interface range F0/1, F0/2, F0/10,F0/24
    SW-1(config-if-range)#switchport port-security violation restrict

### Step 4: Configure DHCP Snooping

a. Configure the trunk ports on SW-1 as trusted ports.

    SW-1(config)#interface range G0/1-2
    SW-1(config-if-range)#ip dhcp snooping trust

b. Limit the untrusted ports on SW-1 to five DHCP packets per second.

    SW-1(config)#interface range F0/2, F0/10,F0/24
    SW-1(config-if-range)#ip dhcp snooping limit rate 5

c. On SW-2, enable DHCP snooping globally and for VLANs 10, 20 and 99.

    SW-2(config)#ip dhcp snooping
    SW-2(config)#ip dhcp snooping vlan 10,20,99

### Step 5: Configure PortFast, and BPDU Guard

a. Enable PortFast on all the access ports that are in use on SW-1.

b. Enable BPDU Guard on all the access ports that are in use on SW-1.

    SW-1(config)#interface range F0/1-2, F0/10,F0/24
    SW-1(config-if-range)#spanning-tree portfast
    SW-1(config-if-range)#spanning-tree bpduguard enable

c. Configure SW-2 so that all access ports will use PortFast by default.

    SW-2(config)#spanning-tree portfast default