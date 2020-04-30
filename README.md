# masvoz-sagemcom-fast-5355-hacks

Investigación sobre el router Sagemcom F@ST 5355

## Indice

1. [Introducción](#introducci%C3%B3n)
2. [Operadores que ofrecen el router](#operadores-que-ofrecen-el-router)
3. [Manuales del router](#manuales-del-router)
4. [Firmware](#firmware)
5. [Hardware](#hardware)
6. [Acceso SSH](#acceso-ssh)
7. [USB Storage](#usb-storage)
8. [GPON Password](#gpon-password)
9. [Configuración Inicial y actual](#configuraci%C3%B3n-inicial-y-actual)
10. [Configuración SIP](#configuraci%C3%B3n-sip)
11. [Contraseña admin](#contrase%C3%B1a-admin)
12. [DHCP](#dhcp)
12. [Referencias](#referencias)

## Introducción

No soy nigún hacker o experto en seguridad. Esta pequeña investigación es poca parte mia (el punto 3 es completamente mio) y mucha parte investigación de multitud de usuarios puestas en orden (puedes ver las fuentes usadas en el capitulo 4. Referencias)

## Operadores que ofrecen el router

Este router se ofrece a todos los operadores que trabajen sobre redes MasVoz. Actualmente son:

- MasVoz
- Pepephone
- Yoigo

## Manuales del router

No existe un manual oficial de MasVoz y cia para el usuario asi que no te vuelvas loco buscandolo. (Si alguien se anima a hacerlo se lo cuelgo aqui para ayudar a otros usuarios).

## Firmware
```
root@home:/# cat /proc/version
Linux version 3.4.11-rt19 (g503707@shz-p0000665fl) (gcc version 4.6.2 (GCC) ) #21 SMP PREEMPT Wed Oct 17 14:51:30 CST 2018
```

```
root@home:/# cat /proc/mtd
dev:    size   erasesize  name
mtd0: 00020000 00020000 "nvram"
mtd1: 000a0000 00020000 "bcm"
mtd2: 00500000 00020000 "data"
mtd3: 07a40000 00020000 "ubi"
mtd4: 00086800 0001f000 "secondaryboot"
mtd5: 00104340 0001f000 "uboot"
mtd6: 00104340 0001f000 "uboot-rescue"
mtd7: 000019e8 0001f000 "permanent_param"
mtd8: 018d3000 0001f000 "rescue"
mtd9: 0175f000 0001f000 "operational"
mtd10: 0001f000 0001f000 "firm_header"
mtd11: 0028b000 0001f000 "kernel"
mtd12: 014d4000 0001f000 "rootfs"
mtd13: 007c0000 0001f000 "filesystem1"
mtd14: 009b0000 0001f000 "filesystem2"
```

## Hardware
```
root@home:/# cat /proc/cpuinfo
system type             : F@ST5655_V2
processor               : 0
cpu model               : Broadcom BMIPS4350 V8.0
BogoMIPS                : 598.01
wait instruction        : yes
microsecond timers      : yes
tlb_entries             : 32
extra interrupt vector  : no
hardware watchpoint     : no
ASEs implemented        :
shadow register sets    : 1
kscratch registers      : 0
core                    : 0
VCED exceptions         : not available
VCEI exceptions         : not available

processor               : 1
cpu model               : Broadcom BMIPS4350 V8.0
BogoMIPS                : 606.20
wait instruction        : yes
microsecond timers      : yes
tlb_entries             : 32
extra interrupt vector  : no
hardware watchpoint     : no
ASEs implemented        :
shadow register sets    : 1
kscratch registers      : 0
core                    : 0
VCED exceptions         : not available
VCEI exceptions         : not available
```

```
root@home:/# cat /proc/meminfo
MemTotal:         206932 kB
etc...
```

## Acceso SSH

1. Entra como el usuario normal con Chrome / Firefox (usuario: 1234 y password: 1234 segun el faq en la web de Yoigo)
2. Abre la consola de desarrollador (F12 en Chrome)
3. Pincha en consola. Esta las consola de comandos de Chrome / Firefox en el cual podemos usar javascript para programar o tocar el codigo ya existente en cualquier web. A continución iremos escribiendo una serie de comandos xmo para modificar la configuracion.
4. Buscamos cual es el uid del usuario con el que nos hemos logado en mi caso es el uid=3. Tras ejecutar este comando:
```
> $.xmo.getValuesTree("Device/UserAccounts/Users");

(4) [{…}, {…}, {…}, {…}]
0: {uid: 1, Enable: true, Login: "internal", Password: "", SecretQuery: "", …}
1: {uid: 2, Enable: true, Login: "acs", Password: "", SecretQuery: "", …}
2: {uid: 3, Enable: true, Login: "1234", Password: "", SecretQuery: "", …}
3: {uid: 4, Enable: true, Login: "admin", Password: "", SecretQuery: "", …}
```
5.Una vez tenemos el UID damos permisos al usuario para que pueda usar la consola SSH
```
> $.xmo.getValuesTree("Device/UserAccounts/Users/User[@uid='3']");
> $.xmo.getValuesTree( "Device/UserAccounts/Users/User[@uid='3']/RemoteAccesses");
> $.xmo.getValuesTree( "Device/UserAccounts/Users/User[@uid='3']/RemoteAccesses/RemoteAccess[@uid='3']/Enabled");
> $.xmo.getValuesTree( "Device/UserAccounts/Users/User[@uid='3']/RemoteAccesses/RemoteAccess[@uid='4']/Enabled");
```

6. Habilitamos por ultimo el SSH y el telnet que viene deshabilitados por defecto.
```
$.xmo.setValuesTree("ACCESS_ENABLE_ALL", "Device/UserAccounts/Users/User[@uid='3']/RemoteAccesses/RemoteAccess[@uid='3']/LANRestriction");
$.xmo.setValuesTree("ACCESS_ENABLE_ALL", "Device/UserAccounts/Users/User[@uid='3']/RemoteAccesses/RemoteAccess[@uid='4']/LANRestriction");
$.xmo.setValuesTree(22, "Device/UserAccounts/Users/User[@uid='3']/RemoteAccesses/RemoteAccess[@uid='3']/Port");
$.xmo.setValuesTree(23, "Device/UserAccounts/Users/User[@uid='3']/RemoteAccesses/RemoteAccess[@uid='4']/Port");
$.xmo.setValuesTree(true, "Device/UserAccounts/Users/User[@uid='3']/RemoteAccesses/RemoteAccess[@uid='3']/Enabled");
$.xmo.setValuesTree(true, "Device/UserAccounts/Users/User[@uid='3']/RemoteAccesses/RemoteAccess[@uid='4']/Enabled");
```
7. Reiniciamos el router se recomienda hacerlo desde la interfaz del router. En mis pruebas da igual estos cambios persisten tras un reinicio fisico del router.
8. Conectamos mediante SSH/Telnet con el usuario 1234. No voy a entrar como conectar o que programa usar tan solo decir que en el paso anterior habilitamos tantos el puerto telnet standar como el SSH standard.
9. Ya dentro del rooter podemos ganar acceso root con "su" (el password suele ser "root"):
```
1234@home:/tmp$ su
Password:

BusyBox v1.17.3 (2018-10-17 14:24:21 CST) built-in shell (ash)
Enter 'help' for a list of built-in commands.
root@home:~#
```
## USB Storage

Por defecto el USB del router esta habilitado como almacenamiento sino desde la interfaz con el usuario normal se puede habilitar.
A traves de Telnet/SSH si pones un pendrive te lo reconocera inmediatamente y te lo montará en /mnt/sda1

Así puedes copiar archivos del router al pendrive o del pendrive al pc para pasar algun bin, algun archivo con el que estes trabajando sobre el router. Esto ultimo es muy recomendable para los pasos de obtener el GPON o el password SIP.
```
root@home:/# cp /archivo_origen.txt /mnt/sda1
root@home:/# cp /mnt/sda1/archivo_origen.txt /tmp
```

## GPON Password

La contraseña GPON de la fibra esta en hexadecimal. Tendras que usar algun conversor si en lugar de hexadecimal para el nuevo router que pongas la quieres en plano. Por ejemplo desde: https://codebeautify.org/hex-string-converter
```
cp /opt/filesystem2/data/optical_conf.txt /mnt/sda1
```

## Configuración Inicial y actual

El xml con la configuración actual se encuentra en XML se encuentra en: /tmp/cfg.xml desconozco porque no lo he probado si algun cambio directo en el xml tras reinicio sobrevive entiendo por la carpeta en la que esta que no, por tanto debe de almacenarse en algun sitio mas que no he localizo aún.

Por otro lado la configuración inicial del router que tienes nada mas resetear esta en: /etc/start-cfg.xml

## Configuración SIP

La configuración SIP se encuentra en /tmp/cfg.xml . El bloque que nos interesa es este:
```
<SIP>
    <AuthUserName>e34910000000@ims.yoigo.com</AuthUserName>
    <AuthPassword>12345678</AuthPassword>
    <AuthPasswordCrypt/>
    <URI>+34910000000</URI>
</SIP>
```

En mi caso con yoigo parace que el usuario es *"e34 + mi_telefono_fijo"*. Por otro lado la contraseña es aleatoria y alfanumerica.

## Contraseña admin

En el firmware de MasVoz/Yoigo parece que viene con usuario de permisos mas elevados "admin" que permite hacer muchas cosas mas en el router. Al parecer este usuario solo se encuentra activo tras el resteo y meter la clave GPON o para que conecten los chicos del soporte tecnico a tu router para hacer tele-asistencia de manera externa.

Aún asi por defecto este usuario podemos acceder a el pero tras el primer reinicio no deshabilita lo que hace es cambiar la contraseña (en primer reinicio suele ser por defecto "admin") y la cambia por una contraseña aleatoria. En mis pruebas parece que es numerica, lo cual nos viene muy bien para obtenerla y asi poder acceder a la configuración avanzada del router.

1. Para obtenerla la tenemos en el archivo con la configuracion actual en: /tmp/cfg.xml
2. La contraseña la tenemos que buscar en el bloque del usuario admin. Por ejemplo:
```
<User uid="4">
    <Enable>true</Enable>
    <Login>admin</Login>
    <Password>6b2a8b2864a82a58032a848f87b4a0d5</Password>
```
3. Como podemos intuir la contraseña no es esta sino es un MD5 de la misma. Al ser numerica es facilmente encontratable en internet buscando en un diccionario para obtener la equivalencia del MD5 con el texto plano. Esta busqueda en Google os la dejo a vosotros.

## DHCP

Para que en la ventana de DHCP te deje de guardar en la consola de depuracion de tu navegador tienes que escribir los siguientes comandos ya que la GUI del router se encuentra bugueada:

```
$('#dhcpForm').scope().dhcpForm.$valid=true
$('#dhcpForm').scope().dhcpForm.$invalid=false
```

## Referencias
- [SAGEMCOM-FAST-5370e-TELIA](https://github.com/wuseman/SAGEMCOM-FAST-5370e-TELIA) Gran investigación sobre otro router no del todo aplicatible ya que en España el firmware ha cambiado, se ha actualizado y se ha personalizado. Pero es tan tan parecido que incluso en el firmware hay ficheros que hablan de felia y townki.

- [Foro Banda Ancha sobre MasMovil](https://bandaancha.eu/foros/extraer-gpon-router-sagemcom-fast-5655v2-1731346) Aqui realmente empecé a ver que decian los usuarios ya que mi idea era poner mi propio router en el domicilio y la imposibilidad o impasividad del servicio tecnico de MasVoz te de lo ayudee en lo necesario para configurar otro router o acceder a una configuración mas avanzada del mismo.
