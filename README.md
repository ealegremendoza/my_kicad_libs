# My KiCad Libraries
*Version 1.0*

Librerias y footprints para KiCad.

Actualmente incluidos:
- FOOTPRINTS
	- Arduino
		- Arduino 101 Shield
		- Arduino Due Shield
		- Arduino Leonardo Shied
		- Arduino M0 Shield
		- Arduino M0 Pro Shield
		- Arduino Mega 2560 Shield
		- Arduino Mega 2560 Shield flipped on top (para conectar volteado sobre la placa).
		- Arduino Micro Socket
		- Arduino Mini Socket
		- Arduino Nano Socket
		- Arduino Uno Shield flipped on top (para conectar volteado sobre la placa).
		- Arduino Zero Shield
		
	- my_footprints
		- GPS EM506 Size
		- lpc1769 board revC
		- lpc1790 board revD
		- acelerometro mpu6050 board
		- acelerometro mpu9250 board
		- XBEE 20 THT
		- XBEE PRO 20 THT 
		- xbee xb24cz7pit 004 -THT  2 socket hembra 02x20. Pitch 2mm
		- XBEE & XBEE PRO 37 SMD
	- Connector Card (SD connecotr & others)
	- TestPoint
	- logoUTN
- LIBS
	- Arduino
		- Arduino 101 Shield
		- Arduino Due Shield
		- Arduino Leonardo Shied
		- Arduino M0 Shield
		- Arduino M0 Pro Shield
		- Arduino Mega 2560 Shield
		- Arduino Micro Socket
		- Arduino Mini Socket
		- Arduino Nano Socket
		- Arduino Uno Shield
		- Arduino Zero Shield
		
	- my libs
		- GPS EM506 Size
		- lpc1769 board revC & revD
		- arduino nano v3
		- acelerometro mpu6050 board
		- acelerometro mpu9250 board
		- cargador bateria Li-ion bq24004
		- Modulo ultrasonido HC-SR04 ARDUINO
		- XBEE 20 THT
		- XBEE PRO 20 THT 
		- xbee xb24cz7pit 004 -THT  2 socket hembra 02x20. Pitch 2mm
		- XBEE & XBEE PRO 37 SMD		
		- reguladores aic1117 - lm2575 - lm2940
		- sensors motion
		- mc34064
		- Display Nokia 5110
		- hc-sr04 ultrasonido arduino



Shield significa que Arduino está diseñado para conectarse desde debajo de su PCB; socket significa que está diseñado para enchufar desde arriba.

## Comments, Requests, Bugs & Contributions
Todos son bienvenidos.
https://github.com/ealegremendoza/my_kicad_libs 



## Library Setup
Para agregar esta biblioteca a su Proyecto KiCad, realice los siguientes pasos:
1. Copie los archivos fuente a su Proyecto. Asegúrese de que la estructura de la carpeta *.pretty esté preservada.
2. En Eeschema (el editor de esquemas de KiCad) vaya a Preferencias -> Bibliotecas de componentes. Haga clic en el botón "Agregar" al lado de "Archivos de la biblioteca de componentes".
3. Navega a la carpeta de tu proyecto, selecciona "*.lib" y haz clic en "abrir".
4. Es posible que desee ajustar la biblioteca de esquemas arduino recién agregada para que esté cerca de la parte superior del orden de carga usando los botones "Arriba" y "Abajo", pero esto es opcional y solo es relevante si tiene otras bibliotecas que usan los mismos nombres para partes.
5. Salga y salga de Eeschema. Abra Pcbnew (el editor de PCB de KiCad) vaya a Preferencias -> Administrador de Bibliotecas de huellas.
6. Seleccione la pestaña "Bibliotecas específicas del proyecto" y luego haga clic en "Agregar biblioteca".
7. En la nueva línea de la tabla, establezca la Ruta de biblioteca en "$ (KIPRJMOD) \ nombre.pretty" en Windows o "$ (KIPRJMOD) /nombre.pretty" en Linux / Mac, y asegúrese de que el Tipo de complemento sea "KiCad". Las opciones y la descripción se pueden dejar en blanco. Debes configurar el sobrenombre como algo descriptivo.
8. Hecho todo: ¡ahora está listo para usar estos componentes esquemáticos y huellas!

## Una nota sobre Power and Reset pins de Arduino

### Power
En la plataforma Arduino, no es posible afirmar categóricamente que los pines de alimentación son "entradas de alimentación" o "salidas de potencia", ya que eso depende exactamente de cómo esté usando el Arduino. Por ejemplo, si está alimentando Arduino desde USB, GND, + 3.3V y + 5V serían salidas de potencia y VIN no haría nada, mientras que si está alimentando el Arduino desde una batería a través de su Shield, entonces VIN y GND son entradas de energía mientras + 5V y + 3.3V son salidas de potencia. También hay otras posibilidades más esotéricas.

Independientemente de lo anterior, necesitaba tomar una decisión sobre qué tipo de electricidad aplicar a estos pines. Podría usar algo como Pasivo o No especificado, pero entonces la herramienta del Comprobador de reglas eléctricas (ERC) de KiCad no sería efectiva para detectar errores en estos pines, mientras que el uso de Salida de potencia significa que se opone a unir pines (por ejemplo, unirse a todos el GND se conecta a una red común) incluso cuando eso está bien en algunas situaciones.

Por lo tanto, he decidido utilizar Power Input ya que esto presenta los menores problemas. Esto significa que si realmente está utilizando alguno de los pines de potencia como salidas de potencia en su esquema, de forma predeterminada el ERC se quejará de que las redes relevantes no están activadas. Para solucionarlo, deberá agregar el componente especial "PWR_FLAG" a la red afectada.

### Reset
Los pines de reinicio en la plataforma Arduino tienen características eléctricas interesantes, lo que significa que ningún tipo de dispositivo eléctrico KiCad coincide exactamente con su funcionalidad. Me decidí por Open Collector como el candidato más cercano, pero a diferencia de un verdadero pin Open Collector en un circuito integrado, los pines de reinicio en la plataforma Arduino tienen un pull-up interno débil, y el botón de reinicio que puede tirar fuertemente, para que tu circuito necesita ser capaz de hacer frente a todas estas situaciones.

En otras palabras, si usa el pin de reinicio como entrada a su escudo, entonces no necesita agregar un pull-up (hacerlo lo hará menos receptivo si no lo rompe); por el contrario, si desea manejar la línea de reinicio para reiniciar el Arduino, debe asegurarse de que solo lo jala bajo: si lo jala alto al mismo tiempo que un usuario involuntario pulsa el botón de restablecimiento físico, ha creado un corto entre potencia y tierra que probablemente freirá cualquier chip que esté en tu escudo.

### TL;DR:

* El KiCad ERC no puede detectar todos los posibles errores eléctricos en su esquema, ya que no admite de forma nativa el restablecimiento y los tipos eléctricos de los pines de potencia. Incluso si el ERC dice que está bien, revísela manualmente. *

* Si el ERC dice que sus pines de alimentación no están activados, primero compruebe manualmente que están siendo accionados. Si se manejan, agregue un componente "PWR_FLAG" a la red para que el error desaparezca. *
