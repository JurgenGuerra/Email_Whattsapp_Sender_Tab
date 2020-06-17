# Alternativa a SendGrid para el envío automático y funcional de correos a los equipos de lxs debatientes utilizando Python y HTML:
Este repositorio contiene una alternativa al uso de SendGrid para enviar correos automaticamente. Esta iniciativa surge de la necesidad de enviar correos masivos y automáticos a lxs debatientes en los torneos de debate Parlamentario Británico, sin embargo su uso no esta *limitado* y se puede usar para practicamente cualquier aplicación que requiera enviar correos masivamente sin el uso de algún software de **paga**.
Asimismo planteo una alternativa(igualmente automática) para aquellos casos en los que no se tiene el correo de lxs destinatarios o existe un error de tipeo. Esta alternativa usa mensajes automatizados a traves de **Whatsapp**, note que esta alternativa tambien puede ser de uso prioritario en caso se requiera una via de comunicación mas fluida que los correos.

Entonces me centraré en explicar los módulos del código mas importantes, los codigos completos esta en este repositorio a su **libre** disposición.
Las partes de esta explicación van en el siguiente orden:
- Librerías a Instalar
- Uso de smtp y MIME
- Automatización de envío masivo de correos
- Alternativa de Wsp con selenium y WebDriver

Vamos :3
## Librerías a Instalar
Para empezar tiene que tener en cuenta 2 cosas, este código esta escrito en pytho debido a sufacilidad de programación y entendimiento pero eso no significa que se pueda replicar en otra clase de lenguajes, creo que en tanto el propósito se cumpla todo estará bien :3, lo segundo es que las librerias a utilizar solo facilitan el manejo de puertos y servidores necesarios para poder enviar un mensaje automaticamente. 

Antes de importar las librerias estas deben ser instaladas con el comando `pip install`, solo instalaremos smtplib ya que MIME y csv vienen por defecto con el paquete de instalación de python, en caso no fuere asi existen diversos repositorios para la instanacion de estas otras librerías, basta con busca: "como instalar [inserte nombre de libreria] en python" y le aparecera un sinfin de tutoriales y repositorios:
```python
pip install smtplib
```
Las librerías a instalar se utilizan para implementar protocolos de envio de correos a traves de los puertos del computador, *smtplib* se usa para usar los Protocolos de envio de correos SMTP, *MIME* para estandarizar los correos y poder enviar correos en formato HTML (con imagenes, videos y links), *csv* sirve para manejar datos en formato Coma separated Value que pueden ser facilmente editables con excel.
Adicionalmente se debe 

```python

import smtplib, ssl 
from email.mime.text import MIMEText 
from email.mime.multipart import MIMEMultipart 
import csv 
 
```
