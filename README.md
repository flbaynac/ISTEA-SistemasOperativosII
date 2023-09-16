# Resumen de Sistemas Operativos II

### Conceptos básicos

#### Generación

Diferencia entre Generación 1 y Generación 2 de máquinas virtuales Hyper-V:

* Gen 1 estaba en versiones de Windows Server 2008 R2
* Gen 2 sale a partir de Windows Server 2012
* Difieren en el firmware que manejan (Gen1 BIOS, no Hot Swap/ Gen2 UEFI, con Hot Swap) por lo tanto la Gen2 posee más opciones de configuración
![gen1gen2](images/gen1gen2.png)
* Se conservan ambas generaciones por compatibilidad

#### Versión de configuración

* En base al Host, cuándo se crea la máquina virtual, las características que posee en ese momento (Por ejemplo teniendo Windows 10 actualizado al crear una máquina virtual se le asigna la versión 9.0)

#### Memoria

* Memoria de inicio: Memoria con la que inicia la máquina virtual (lo que reserva en el Host)
* Memoria dinámica: Utiliza memoria a demanda (por ejemplo usa 1GB y no el límite considerado en Memoria de inicio)
* Memoria fija: Reserva toda la memoria asignada en Memoria de inicio
* Microsoft sugiere que en equipos de #producción se le asigne memoria fija y memoria dinámica se use para entornos de #desarrollo

#### Adaptador

* 

#### Discos

* 

#### Creación de máquinas virtuales

* Luego de habilitar la característica de Hyper-V en Windows abrir Hyper-V manager:
![hyper-v](images/hyper-v-manager.png)
* Click derecho sobre host (mi PC arriba a la izquierda) -> Nuevo -> Máquina virtual
* Poner nombre a nuestra máquina virtual
* Clickear checkbox para almacenar la máquina virtual en otra ubicación (se recomienda no dejarlo en el disco local C:\\ )
* Seleccionar la [Generación](#Generación)
* Se le asigna un espacio de [memoria](#Memoria) (RAM)
* Se le asigna un [adaptador](#Adaptador) de red
* Se le asigna un [disco](#Discos) duro virtual
* 