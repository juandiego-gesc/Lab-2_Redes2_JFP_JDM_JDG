 # Introducción
El objetivo de este laboratorio es recordar los conceptos que ya teníamos relacionados con la creación de redes LAN e IPv4. Se usarán VLANs, DHCP, NAT y ACL. Además de esto, se añadirá otro nivel de dificultad, pues Ahora aplicaremos protocolos de enrutamiento dentro de las redes WAN.


# Desarrollo de la solución


## Topología de Red:


>![Imagen](/pics/Topologia%20Logica.png)


>
## Tabla de enrutamiento
>![Imagen](/pics/TE_1.png)
>![Imagen](/pics/TE_2.png)

## Enrutamiento en Internet
>![Imagen](/pics/Routing_1.png)
>![Imagen](/pics/Routing_2.png)
>![Imagen](/pics/Routing_3.png)
>![Imagen](/pics/Routing_4.png)


## Configuraciones
>
> Omitiendo las configuraciones basicas de los dispositivos de red, se presentan a continuacion las configuraciones mas relevantes para este taller. 
>
> **Configuracion MLSW**
>```
> interface GigabitEthernet1/0/1
> switchport mode trunk
>
>interface GigabitEthernet1/0/2
>switchport mode trunk
>
>interface GigabitEthernet1/0/3
>switchport mode trunk
>```
> **Creación de VLANs**
>
> Creacion VLAN nativa con id 99
>
>```
> SW1_Intranet(config)#vlan 99
> SW1_Intranet(config-vlan)#name native
> SW1_Intranet(config-vlan)#interface vlan 99
>```
>Creacion VLANs intranet Bogotá
>```
>SW1_Intranet(config)#vlan 50
>SW1_Intranet(config-vlan)#name ing
>SW1_Intranet(config-vlan)#vlan 100
>SW1_Intranet(config-vlan)#name tech
>```
> **Configuracion EIGRP**
>```
> router eigrp 1
> network 190.85.218.0 0.0.0.3
> network 190.85.218.4 0.0.0.3
> network 190.85.218.8 0.0.0.3
>```
> Este ejemplo particular es para el Router ISP_BOG. Para configurar el EIGRP se tenia que definir en cada uno de los routers las direcciones de los routers vecinos, junto con sus WildCards para hacer el enrutamiento.
> 
> **Asignacion eficiente del esquema de direccionamiento desarrollado**
>
>
> Para poder hacer un arreglo eficiente del esquema de direccionamiento desarrollado, se siguieron los siguientes puntos:
>
>Se verificaron la cantidad de hosts prevista para que cada subred sea la adecuada y ajustarla según sea necesario. Si una subred tiene un gran número de hosts, entonces se asigno una máscara de red con suficientes bits para cubrir esa cantidad.
>
>Se asignaron direcciones IP a cada uno de los hosts en la subred, asegurándose de que no haya conflictos de direcciones IP. También se uso DHCP para facilitar la configuración de direcciones IP de forma automática.
>
>Se configuraron los dispositivos de red (routers, switches,multilayer switch.) con las direcciones IP asignadas a cada subred. Asegúrese de que los dispositivos de red estén correctamente configurados para enrutar tráfico entre subredes.
>
>Se implementaron el esquema de seguridad de red adecuado, como el filtrado de paquetes y las listas de control de acceso (ACL), para proteger la red contra posibles amenazas externas

>


## Verificaciones
> ### Pruebas de conexión por puertos:
>
>El comando ping es utilizado para verificar la conectividad con un dispositivo de red mediante el envío de paquetes ICMP de solicitud de eco y esperando la respuesta del dispositivo. Sin embargo, este protocolo no utiliza puertos, por lo que no es posible enviar un ping al puerto 80.
>
>En este caso, como se busca verificar la conexión a los puertos de red 80 y 443 (HTTP y HTTPS), es posible usar el comando telnet (Dirección IP destino) (Número de puerto), de esta manera se verifica que:
Los dispositivos de la red de Bogotá tienen acceso al servidor web mediante el puerto 443, pero no por el 80.
>
>
>![Imagen](/pics/TelnetPuertosBogota.png)
>
>Los dispositivos del departamento de tecnología de esta misma red (Bogotá) pueden acceder al servidor por medio de ambos puertos, es decir, no tienen ninguna restricción.
> 
>
>
>
>![Imagen](/pics/TelnetPuertosTech.png)
>
>Los dispositivos de la subred de Tesorería (Madrid) tienen acceso al servidor web por medio del puerto 443, pero no por el puerto 80.
>
>![Imagen](/pics/TelnetPuertosTesoreria.png)
>
>Finalmente, los dispositivos de la subred Vicepresidencia no tienen acceso al servidor web por ninguno de los dos puertos, es decir, tienen bloqueado su acceso.
>
>![Imagen](/pics/TelnetPuertosVicepresidencia.png)
>
> ## Pruebas de conexion
>
>Para verificar la conexión de una terminal a otra, se utiliza el comando ping en la terminal del dispositivo de origen para enviar paquetes de solicitud de eco a la dirección IP del dispositivo de destino y verificar si se recibe una respuesta.
>
>Se observa que los dispositivos de la intranet Bogotá se comunican entre ellos de manera efectiva, de igual manera, existe conexión entre estos dispositivos y el servidor.
>
>![Imagen](/pics/PingIntranetBogota.png)
>
>![Imagen](/pics/PingServerBogota.png)
>
>Por otro lado, la red de Madrid también logra establecer conexión entre los dispositivos de la intranet, ya sea que pertenezcan a la misma VLAN o no. El servidor web también envía respuesta a estos dispositivos, lo que verifica que la conexión se ha establecido correctamente.
>
>![Imagen](/pics/PingIntranetMadrid.png)
>![Imagen](/pics/PingServerMadrid.png)



# Simulación

>Para terminar, se hace un envío de paquetes de un PC de la red de Madrid al servidor web, para comprobar que se realiza un intercambio de paquetes
>![Imagen](/pics/Simulacion.png)
>
>Se puede observar que el paquete de respuesta es enviado efectivamente por el enrutador R1_BOG a través de la internet
>
>![Imagen](/pics/SimulacionPDU.png)


# Access List:
>Es necesario configurar una lista de control de acceso de puertos, que se utiliza para controlar el tráfico que fluye a través de los puertos de red entre las computadoras personales y el servidor web. Estas se ubican en los enrutadores frontera, en este caso R1_BOG y R2_MAD, y se configuran de la siguiente manera:
>
>```
>R1_BOG:
>access-list 120 deny tcp 172.24.4.0 0.0.3.255 any eq www
>access-list 120 permit ip any any
>
>```
>
>```
>R2_MAD:
>access-list 121 deny tcp 10.118.2.0 0.0.1.255 any eq www
>access-list 121 deny ip 10.118.4.0 0.0.1.255 host 161.125.13.4
>access-list 121 permit ip any any
>```
>
>



# Protocolos:

Para el desarrollo de este laboratorio, con el objetivo de comunicar las VLANs, las Intranets y las para implementar routing dinamico, se utilizaron los siguientes protocolos:


> **-EIGRP (Enhanced Interior Gateway Routing Protocol):** 
> 
>EIGRP es un protocolo de enrutamiento de puerta de enlace interior (IGP) avanzado patentado de Cisco que determina la mejor ruta a través de la red en función de factores como la distancia, el ancho de banda, la demora, la carga y la confiabilidad. EIGRP ofrece convergencia rápida, menor uso de ancho de banda y confiabilidad, además de capacidades de equilibrio de carga. EIGRP también admite enrutamiento sin clases y redes jerárquicas. Además, EIGRP utiliza la CPU y el protocolo de transmisión de multidifusión de Cisco con uso eficiente del ancho de banda para enviar actualizaciones de enrutamiento a los nodos vecinos.
>
>
> **-Trunking (IEEE 802.1Q):** 
> 
> Este protocolo implica el uso de las etiquetas que agregó el protocolo antes mencionado para permitir que un conmutador o enrutador transmita datos desde múltiples VLAN utilizando la misma interfaz.
> 
> Dado que el enrutador ahora puede reenviar tramas utilizando una sola interfaz, el enlace troncal permite que todas las VLAN necesarias se transmitan a través de un solo cable, lo que permite la comunicación entre las VLAN en una red sin la necesidad de separar físicamente los cables que transportan información a cada VLAN.


# Desafíos afrontados:


La conexión de la intranet de Madrid fue un reto para el desarrollo del laboratorio ya que el hacer el protocolo dhcp sin la utilización de un servidor es algo novedoso al momento de la implementación del protocolo. Se realizaron varias búsquedas de información sobre esto y la utilización del multilayer switch o switch de capa 3. Finalmente, se logró de manera positiva el protocolo dhcp en los respectivos pcs de acuerdo a las vlans solicitadas. Por otro lado, la utilización de manera correcta de las Access list en los routers de las dos grandes ciudades fue reto ya que permitir que un protocolo como http o el https funcione o no en un pc es determinado por los puertos de entrada y el bloqueo que se hace.



# Conclusiones:



•	Se lograron aplicar los conceptos para la construcción de redes LAN-WAN basadas IPv4 a través de los protocolos DHCP, NAT y ACL .

•Se utilizó el protocolo de enrutamiento EIGRP en las interfaces de internet para hacer la conexión entre routers y que se permitiera el enrutamiento de paquetes.

•	Se aplicaron las Access list para la configuración del servicio HTTP en los contenidos Web y el servicio DNS, simulando y analizando el flujo entre redes, routers, vlans y pcs.



# Referencias:



[1] Jeremy D. Cioara. Subnetting Examples [En línea]. Disponible en: https://bit.ly/3v6crh

[2] Llorca Alcón, Manuel. "Configuración de una VLAN mediante Switches CISCO". RiuNet repositorio UPV. https://riunet.upv.es/handle/10251/1424?tl=a (accedido el 23 de septiembre de 2022).

[3] "Cómo configurar un switch de red para su empresa en 6 pasos". Cisco. https://www.cisco.com/c/es_mx/solutions/small-business/resource-center/networking/how-to-setup-network-switch.html (accedido el 23 de septiembre de 2022).
[4] Cisco, "Networking Essentials. Module Group 5."

[5] Juan M. Aranda, “Basic configuration”, Slides.

[6] Jeremy D. MicroNugget: How to Select Subnet Sizes for VLANs [En línea]. Disponible en: https://bit.ly/3BuXHeV

[7] "Tipos de Spanning Tree (CST, PVST+, RSTP, MSTP)". Tech Riders. https://techriders.tajamar.es/tipos-de-spanning-tree/#:~:text=Per-VLAN%20Spanning%20Tree%20(PVST,VLAN%20mientras%20bloquea%20otras%20VLAN.

[8] "802.1Q Encapsulation Explained". NetworkLessons.com. https://networklessons.com/switching/802-1q-encapsulation-explained.

[9] Biblioteca Universitaria - UTB. TUTORIAL TEÓRICO PRÁCTICO DE LABORATORIO RIPV1, RIPV2, EIGRP Y OSPF. https://biblioteca.utb.edu.co/notas/tesis/0063133.pdf.



