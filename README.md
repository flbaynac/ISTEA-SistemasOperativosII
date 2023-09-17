# Resumen de Sistemas Operativos II

### Conceptos básicos

#### Generación

Diferencia entre Generación 1 y Generación 2 de máquinas virtuales Hyper-V:

* Gen 1 estaba en versiones de Windows Server 2008 R2
* Gen 2 sale a partir de Windows Server 2012
* Difieren en el firmware que manejan (Gen1 BIOS, no Hot Swap, usa controladora de discord IDE/ Gen2 UEFI, con Hot Swap, usa controladora de discord SCSI) por lo tanto la Gen2 posee más opciones de configuración
* ![gen1gen2](images/gen1gen2.png)
* Se conservan ambas generaciones por compatibilidad

#### Versión de configuración

* En base al Host, cuándo se crea la máquina virtual, las características que posee en ese momento (Por ejemplo teniendo Windows 10 actualizado al crear una máquina virtual se le asigna la versión 9.0)

#### Memoria Virtual

Memoria (RAM)
* Memoria de inicio: Memoria con la que inicia la máquina virtual (lo que reserva en el Host)
* Memoria dinámica: Utiliza memoria a demanda (por ejemplo usa 1GB y no el límite considerado en Memoria de inicio)
* Memoria fija: Reserva toda la memoria asignada en Memoria de inicio
* Microsoft sugiere que en equipos de #producción se le asigne memoria fija y memoria dinámica se use para entornos de #desarrollo

#### Adaptador

Conmutadores de red virtualizados
* Existen 4 tipos:
	* Privado: Permite que las máquinas virtuales se comuniquen entre sí pero no admite conexión entre el Host y el Invitado. Pensado para habientes de #desarrollo 
	* Interno: Igual que el privado pero admite la conexión entre el Host (creando una placa virtual en el mismo) e Hyper-V siempre y cuando estén en la misma subred. Pensado para habientes de #desarrollo  
	* Externo: Igual que el interno pero necesita utilizar una placa física para conectarse. Al crear el adaptador virtual, el adaptador interno (físico) pierde el poder de administración y el que se debe administrar es el virtual (tal cual como si fuese un adaptador de Red físico).
	* Default Switch: Conmutador por defecto

#### Discos Virtuales

* Se almacena en un archivo con extensión .vhdx o .vhd
* Diferencia entre .vhdx y .vhd:
	* .vhdx se encuentra a partir de Windows Server 2012, se puede crear discos virtuales hasta 64TB y Microsoft dice que es menos propenso a errores
	* .vhd se encuentra hasta Windows Server 2008R2, se puede crear discos virtuales hasta 2TB
* Se puede convertir de .vhd a .vhdx
* Se dividen en 3 categorías:
	* Dinámicos: Se le asigna el total en GB pero sólo utiliza la cantidad (espacio) de datos que tiene almacenado, para entornos de #desarrollo se puede convertir a fijo.
		![discodinamico](images/disco-dinamico.png)
	
	* Fijos: Se le asigna el total en GB y el tamaño del archivo que se crea es de esa cantidad
	* Diferenciados/secundario (sólo para ambientes de #desarrollo ya que si se cae o rompe el disco primario, también se rompe el secundario): Posee un disco duro primario ([template](#Disco\virtual\Template\o\Plantilla)) referenciado y usa su propio .vhdx para alojar las diferencias (por ejemplo el primario posee el sistema operativo y el secundario un aplicativo como SQL) 
* Se agregan al crear la máquina virtual o desde configuración en el apartado de Controladora SCSI

#### Sistema Operativo

* Se descarga desde [Microsoft](https://www.microsoft.com/es-ES/evalcenter/evaluate-windows-server-2022) en formato .ISO
* Se agregan al crear la máquina virtual o desde configuración en el apartado de Controladora SCSI, en unidades de DVD, imagen .ISO
* Vienen en dos ediciones (la cual se elije en la instalación y no se puede cambiar):
	* Sin interfaz gráfica (Línea de comandos)
	* Experiencia de escritorio (Con interfaz gráfica)

#### Snapshot

* Es una imagen del sistema operativo en un momento dado
* Se puede, una vez instalado el sistema operativo, crear una snapshot a fin de evitar un posible error al instalar una aplicación
* Crea un archivo .avhdx que aloja las diferencias
* Sólo se recomienda en entornos de #desarrollo ya que baja el rendimiento al tener que recorrer el .vhdx original y el .avhdx con las diferencias
* Se puede eliminar el snapshot una vez que se verificó que funciona todo correctamente (necesario en entornos de #producción ) para dejar un único archivo .vhdx y mejorar el rendimiento (puede tardar si posee muchos snapshots o si es muy grande)
* Los Snapshot __NO SON UN BACKUP__
* A partir de la versión de configuración 9 vienen activados los snapshots automáticos

#### SID

* Es un código identificador único de seguridad (Como la dirección MAC en dispositivos de Hardware) que se asigna cuando se instala el sistema operativo
* El ejecutable C:\\windows\\system32\\sysprep\\sysprep.exe borra archivos de configuración para poder exportar la máquina y usarla como [template](#Disco\virtual\Template\o\Plantilla)
*  ![sysprep](images/sysprep.png)

#### Disco virtual Template o Plantilla

Disco template/plantilla de [Sistema Operativo](#Sistema\Operativo)
* Debe poseer un nombre descriptivo (por ejemplo: TemplateWS2021CoreEs_Dynamic.vhdx)
* Debe tener un SID diferente (debe ejecutarse sysprep.exe en el equipo a exportar -> clickear en generalize -> y seleccionar la opción de shutdown -> copiar el .vhdx)

#### Creación de máquinas virtuales

* Luego de habilitar la característica de Hyper-V en Windows abrir Hyper-V manager:
* ![hyper-v](images/hyper-v-manager.png)
* Click derecho sobre host (mi PC arriba a la izquierda) -> Nuevo -> Máquina virtual
* Poner nombre a nuestra máquina virtual
* Clickear checkbox para almacenar la máquina virtual en otra ubicación (se recomienda no dejarlo en el disco local C:\\ )
* Seleccionar la [Generación](#Generación)
* Se le asigna un espacio de [memoria](#Memoria\Virtual) (RAM)
* Se le asigna un [adaptador](#Adaptador) de red
* Se le asigna un [disco](#Discos\Virtuales) duro virtual
* Finalizar (Posteriormente se le debe agregar un [sistema operativo](#Sistema\Operativo) para que arranque)

#### Sesión mejorada

* Se activa haciendo click derecho sobre el host (arriba a la izquierda) -> Configuración de Hyper-V -> Activar el checkbox de sesión mejorada
* Permite funciones como ajuste dinámico de la resolución, portapapeles compartido, entre otras funcionalidades

#### Inicio automático

* Determina si la máquina virtual se inicia automáticamente cuando se encienda el Host
* Se puede retrasar el inicio (útil en ambientes de #producción por ejemplo para controladores de dominio)

#### Detención automática

* Determina el comportamiento de la máquina virtual cuando se apaga el Host
* Por defecto guarda el estado en el que está (con sus programas abiertos)
* Pensado para ambientes de #desarrollo 
* En #producción se utiliza la opción de Apagar el sistema operativo invitado

#### Virtualización anidada

* Se consigue instalando el rol de Hyper-V a una máquina virtual
* Se necesita habilitar la virtualización anidada en el Host (por cada máquina virtual)
* La máquina virtual debe estar apagada, debe poseer Windows 10 o Windows Server 2016 en adelante
* Se habilita con el siguiente comando en el Host utilizando Powershell:
```
Set-VMProcessor -VMName <NombreDeLaVM> -ExposeVirtualizationExtensions $true
```
* Se habilita el rol de Hyper-V desde Server Manager
* Seleccionar un protocolo (es necesario seleccionar un protocolo utilizado en Live Migration que se utilice en ambos __nodos__, Host, Hipervisor o Servidor Físico, aunque se puede editar a posterior)
* Seleccionar una ubicación dónde se alojan los .vhdx y las VMs
