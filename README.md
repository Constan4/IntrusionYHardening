PARA LEER TODA LA INVESTIGACION ACCEDER AL PDF

🛡️ Ciclo de Intrusión y Hardening en Sistemas Windows 💻
👤 Información del Autor
Autor: Constan Millán Hernández

Rol: Estudiante de Ciberseguridad en Neoland

Tutora: María Cecilia Romestant

Proyecto: Trabajo de Fin de Grado (TFG)

📝 1. Introducción
Este proyecto documenta un ciclo completo de seguridad informática, simulando un escenario real de Pentesting (auditoría ofensiva) y posterior Hardening (defensa proactiva). El objetivo es demostrar cómo las debilidades en configuraciones por defecto y el factor humano permiten el compromiso total de un equipo, y cómo medidas sencillas de endurecimiento pueden neutralizar estas amenazas.

🎯 Objetivos
Simular una intrusión desde el acceso a la red hasta el control del sistema.

Demostrar la efectividad de la ingeniería social mediante macros.

Ejecutar técnicas de post-explotación: persistencia y exfiltración.

Implementar medidas de mitigación mediante el firewall de Windows.

⚙️ 2. Diseño del Escenario de Laboratorio
Para este proyecto se utilizó un entorno controlado:

Máquina Atacante: Kali Linux (Suite Aircrack-ng, Metasploit, Nmap).

Máquina Víctima: Portátil ASUS con Windows 11.

Objetivo Crítico: Sustracción de un archivo sensible llamado clasificado.txt.

🔍 3. Fase de Reconocimiento y Explotación
📶 3.1. Acceso a la Red y Discovery
Se realizó una auditoría de la red Wi-Fi local mediante cracking WPA2 para situar a la máquina atacante dentro del mismo segmento que la víctima.

Herramienta: Nmap

Comando: nmap -sV -O 192.168.1.0/24

Resultado: Identificación del portátil objetivo con puertos críticos abiertos (135, 139, 445).

📧 3.2. Ingeniería Social y Entrega
Se optó por un vector de ataque basado en Malicious Macros.

Creación de un documento Word legítimo.

Inyección de código VBA diseñado para descargar y ejecutar un agente remoto.

Uso de un nombre de archivo convincente para asegurar la ejecución por parte del usuario.

⚡ 3.3. Payloads y Explotación
Se generó un payload específico para arquitectura x64 mediante msfvenom para maximizar la compatibilidad y estabilidad con Windows 11.

LHOST: 192.168.1.162 (Kali)

Handler: multi/handler con windows/x64/meterpreter/reverse_tcp

🕵️ 4. Post-Explotación: El Control Total
Una vez establecida la sesión de Meterpreter, se ejecutaron las siguientes acciones:

🕒 4.1. Persistencia Silenciosa
Para garantizar el acceso tras reinicios, se creó una clave en el registro de Windows:

Clave: oN8F1z6V

Ubicación: HKCU\Software\Microsoft\Windows\CurrentVersion\Run

📸 4.2. Exfiltración y Espionaje
Webcam: Uso de webcam_stream para vigilancia en tiempo real.

Screenshots: Captura del escritorio para visualizar documentos abiertos.

Keylogging: Registro de pulsaciones para capturar credenciales bancarias y de correo.

Robo de Archivos: Localización y descarga del archivo clasificado.txt.

🧱 5. Hardening: La Defensa Fundamental
Tras analizar el éxito del ataque, se implementaron medidas de endurecimiento:

🚫 5.1. Mitigación vía Firewall
Se configuraron reglas estrictas de entrada para bloquear los protocolos utilizados en el reconocimiento y movimiento lateral:

Puerto 135 (MSRPC)

Puerto 139 (NetBIOS)

Puerto 445 (SMB)

⚖️ 5.2. Análisis Usabilidad vs. Seguridad
Se demostró que el cierre de estos puertos no afecta la operatividad diaria del usuario (navegación, streaming, trabajo), pero elimina completamente el vector de ataque por red local.

🎓 6. Conclusiones
El Firewall como pilar: Es la primera y más crítica línea de defensa. Sin él, el atacante recibe información privilegiada de forma gratuita.

El eslabón humano: Incluso el sistema más actualizado es vulnerable si el usuario ejecuta archivos adjuntos de fuentes no verificadas.

Hacking Ético: La importancia de auditar sistemas para descubrir estas brechas antes que los actores malintencionados.
