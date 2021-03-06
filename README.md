# Implementación de una Base Station Transceiver BTS 2G(GSM) utilizando un dispositivo SDR
En este proyecto se exponen las herramientas de hardware y software necesarias para implementar una estación base GSM. Se utilizan las aplicaciones de OSMOCOM dedicadas al desarrollo de software para telefonía celular donde incluyen el estándar GSM. En la parte de Hardware se utilizó una PC portátil, para instalar las aplicaciones de OSMOCOM, y el dispositivo limeSDR en su versión mini fabricado por la empresa Microsystems. Se explica cada uno de los elementos que contiene el sistema para comprender el funcionamiento de las redes de telefonía celular que tienen gran importancia en la actualidad. Además se realizan una serie de pruebas sobre la conexión a la estación base, el envió de mensajes SMS y la comunicación por llamadas de voz entre dos usuarios.

### Requisitos
- Equipo de computo, Laptop o equipo de escritorio.
- Dispositivo LimeSDR de la empresa Microsystems
- Dos o mas celulares para hacer prueba de funcionamiento.

### SDR
El termino Radio Definido por Sotware (SDR) se refiere a una radio reconfigurable. Es decir, una radio que a partir de un programa de software puede variar sus parámetros sin tener que recurrir al hardware. Anteriormente solo se podía hacer por hardware lo que provocaba limitaciones al momento de una actualización de ese dispositivo de radiofrecuencia. Por ejemplo, la radio FM y AM, estos radios para poder capturar los datos de voz, emitidos por la antena transmisora, tenían que modificar un parámetro de hardware para sintonizar la frecuencia, ya fuera variar la capacitancia o resistencia. Con la aparición del SDR ese mismo proceso se hace desde el software sintonizando muy fácil, además no solo poder sintonizar un rango de frecuencias pequeño si no que ahora se puede capturar un rango grande de frecuencias y sintonizar varios estándares con modulaciones diferentes, es decir se pueden ver señales de estándares como AM, FM y hasta televisión digital que se encuentra en otra frecuencia y tiene otro tipo de modulación.

![](/img/SDR.png "SDR") 
El dispositivo SDR nos permitira hacer la transmicion y recepcion de los mensajes SMS y voz. LimeSDR es un  dispositivo creado por microsystems es utilizado en este trabajo por lo que se explicaran en detalle las características más relevantes.
- Transceptor RF: Lime Microsystems LMS7002M
- FPGA : Intel Altera MAX 10 (10M16SAU169C8G), empaquetado de 169 pines, 549 KB de memoria M9K, 2,368 KB de memoria flash del usuario, 4 x PLL, 45 multiplicadores de 18x18 bits, 130 x entrada / salida de propósito general (GPIO), Tensión de alimentación única, Característica de flash, Configuración de FPGA a través de JTAG
- Memoria EEPROM: 2 x 128 KB para el firmware y los datos del MCU transceiver RF
- Memoria flash: 1 x 4 MB de memoria flash para datos
- Entradas / salidas generales del usuario: 2 x doble color (rojo + verde) LED, 8 x cabezal de pin FPGA GPIO (3.3 V)
- Conectividad: USB 3.0 Tipo A, 2 x conectores RF coaxiales (SMA) (cada uno puede cambiarse entre bandas de alta y baja frecuencia), Conector UFL para fuente de reloj externa, Cabeceras FPGA GPIO, Conector FPGA JTAG
- Sistema de reloj: VCTCXO a bordo de 30.72 MHz, Posibilidad de sintonizar VCTCXO con DAC incorporado, Entrada de reloj externa a través del conector UFL.
![](/img/LimeSDRmini.png "LimeSDR mini")
LimeSDR permitira la transmisión y recepción de ondas de radio a diferentes frecuencias, 

### OSMOCOM
OSMOCOM es un proyecto sobre comunicaciones móviles de código abierto que permite instalar sus dependencias sin ninguna restricción, existe desde finales del 2008 y es un proyecto comunitario clásico dirigido por entusiastas. Con la desventaja de ser para personas expertas en comunicaciones. OSMOCOM ofrece los siguientes proyectos:
- Las implementaciones GSM y 3G del lado de la red originalmente conocidas como OpenBSC (pero ahora incluyen BTS, PCU, BSC, MSC, HLR, PCU, SGSN, GGSN)
- Implementación GSM del lado del teléfono OsmocomBB
- El proyecto RTL-SDR utilizado para la transmisión y recepción de televisión digital.
- OsmoNitb que implementa la arquitectura de una red celular 2G para principiantes en el área de comunicaciones.

#### OSMO-TRX-LMS
OsmoTRX es un transceptor de radio definido por software que implementa la capa física de Capa 1 de un BTS que comprende las siguientes especificaciones de 3GPP:
- TS 05.01 "Capa física en la ruta de radio"
- TS 05.02 "Multiplexación y acceso múltiple en la ruta de radio"
- TS 05.04 "Modulación"
- TS 05.10 "Sincronización de subsistemas de radio"
OsmoTRX se basa en el código del transceptor del proyecto OpenBTS , pero se configura para funcionar de manera independiente con el propósito de usarlo con software y proyectos que no sean de OpenBTS, mientras se mantiene la compatibilidad con OpenBTS . Actualmente hay numerosas funciones en OsmoTRX que amplían la funcionalidad del transceptor OpenBTS. Estas características incluyen soporte mejorado para varias plataformas integradas, en particular ARM

#### OSMO-NITB Y OSMO-BTS
OsmoNITB implementa todo lo necesario para una red de conmutación GSM, esto de manera limitada, ya que en la actualidad ya no se le da soporte a esta parte. OsmoNITB se encarga de todo el subsistema de conmutación de red, desde el MSC hasta las bases de datos HLR y VLR.

![](/img/Implementacion2G.png "Arquitectura de red 2G") 

### Instalación 
#### LimeSuite
~~~ 
apt install git g++ cmake libsqlite3-dev libusb-1.0-0-dev
apt install “nombre de la librería” 

git clone https://github.com/myriadrf/LimeSuite.git
cd LimeSuite
mkdir construir && cd construir
cmake ../
make
make install
ldconfig
cd ../
sh LimeSuite/udev-rules/install.sh
CD ../

apt install libtool pkg-config libtalloc-dev libgnutls28-dev
~~~

#### LIBOSMOCORE
~~~
apt install libtool pkg-config libtalloc-dev libgnutls28-dev

git clone git://git.osmocom.org/libosmocore
cd libosmocore
autoreconf -fi
./configure
make
make install
ldconfig
cd ../
~~~

#### OSMO-TRX-LMS
~~~ 
apt install libfftw3-dev

git clone git://git.osmocom.org/osmo-trx
cd osmo-trx
autoreconf -fi
./configure --without-uhd --with-lms
make
make install
ldconfig
cd ../

~~~

#### OSMO-BTS y OSMO-NITB
~~~
Wget http://download.opensuse.org/repositories/network:/osmocom:/latest/Raspbian_9.0/Release.key
sha256sum Release.key
apt-key add Release.key
rm Release.key
echo "deb http://download.opensuse.org/repositories/network:/osmocom:/latest/Raspbian_9.0/ ./" > /etc/apt/sources.list.d/osmocom-latest.list
apt update
apt install osmocom-nitb osmo-bts-trx telnet
systemctl disable osmo-bts-trx.service
systemctl disable osmo-nitb.service

~~~
### Resultados
Para ejecutar los archivos de configuracion de la arquitectura de OSMOCOM utilizamos los siguientes comandos en terminales diferentes ya que generara un proceso que se estara ejecutando de manera indeterminada hasta que decidamos detenerlo:
~~~
osmo-trx-lms -C ./osmo-trx.cfg
osmo-nitb -C ./openbsc.cfg
osmo-bts-trx -C ./osmo-bts.cfg
~~~
Al final tendran la ejecución de tres ventanas como se muestra en el [video][video] que se encuentra en la ruta de este repositorio. 

Para una información mas completa del funcionamiento de este trabajo recomiendo leer el proyecto de [Tesis][Tesis].

[video]: https://github.com/GeovanniMadrigal/Red-celular-2G-LimeSDR/tree/main/video
[Tesis]:https://github.com/GeovanniMadrigal/Red-celular-2G-LimeSDR/blob/main/Tesis.pdf