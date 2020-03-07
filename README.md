# masvoz-sagemcom-fast-5355-hacks

Investigación sobre el router Sagemcom F@ST 5355

## Indice
1. Introducción
2. Operadores que ofrecen el router
3. Manuales del router

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
>  cat /proc/version
Linux version 3.4.11-rt19 (g503707@shz-p0000665fl) (gcc version 4.6.2 (GCC) ) #21 SMP PREEMPT Wed Oct 17 14:51:30 CST 2018
```

```
cat /proc/mtd
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
> cat /proc/cpuinfo
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

## Acceso SSH

1. Entra como el usuario 1234 (password: 1234) con Chrome / Firefox
2. Abre la consola de desarrollador (F12 en Chrome)
3. Pincha en consola. Esta las consola de comandos de Chrome / Firefox en el cual podemos usar javascript para programar o tocar el codigo ya existente en cualquier web. A continución iremos escribiendo una serie de comandos xmo 

## Referencias
- [SAGEMCOM-FAST-5370e-TELIA](https://github.com/wuseman/SAGEMCOM-FAST-5370e-TELIA) Gran investigación sobre otro router no del todo aplicatible ya que en España el firmware ha cambiado, se ha actualizado y se ha personalizado. Pero es tan tan parecido que incluso en el firmware hay ficheros que hablan de felia y townki.
- [Foro Banda Ancha sobre MasMovil](https://bandaancha.eu/foros/extraer-gpon-router-sagemcom-fast-5655v2-1731346) Aqui realmente empecé a ver que decian los usuarios ya que mi idea era poner mi propio router en el domicilio y la imposibilidad o impasividad del servicio tecnico de MasVoz te de lo ayudee en lo necesario para configurar otro router o acceder a una configuración mas avanzada del mismo.
