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
Las librerías a instalar se utilizan para implementar protocolos de envio de correos a traves de los puertos del computador, **smtplib** se usa para usar los Protocolos de envio de correos SMTP, **MIME** para estandarizar los correos y poder enviar correos en formato HTML (con imagenes, videos y links), **csv** sirve para manejar datos en formato coma separated Value que pueden ser facilmente editables con excel.
Adicionalmente se debe instalar la libreria **selenium** para el uso de WebDriver (en la parte de la alternativa se vera mas a detalle esto).

```python

import smtplib, ssl 
from email.mime.text import MIMEText 
from email.mime.multipart import MIMEMultipart 
import csv 
 
```
una vez instalados e importados los paquetes de libreria procedere a explicar el codigo.

## Uso del smtp y MIME 
Para esta parte es importante comprender cada parte del codigo en especial la parte de la apertura del server. Aqui una correccion mucho de este codigo no es de mi propiedo si no mas bien la union de otros repositorios que explicaban temas parecidos, lo unico que hago es diseccionar un poco y despues automatizar el envio de estos correos.
**IMPORTANTE** para el uso de los protocolos smtp es necesario que actives el acceso para aplicaciones menos seguras, se que puede parecer peligroso pero basicamente google tiene un sistema que solo permite el acceso a tu correo a muy pocas aplicaciones externas(por protección y por monopolización del mercado), obviamente una ves acabado el trabajo es necesario que la desactives nuevamente para evitar problemas.
La activacion se hace a traves de [este link](https://myaccount.google.com/lesssecureapps) una vez ya dentro de cuenta de correo que usarás para el envío.
![alt text](https://raw.githubusercontent.com/JurgenGuerra/Env-o_Autom-tico_de_Correos/master/Lesssecureapps.PNG)

Una vez esto hecho podemos comenzar a analizar el codigo.
```python

#MIME WORKS WITH ANY ASCII CHARACTER ALSO ALLOWS TO SEND HTML, IMAGES, VIDEOS, AND OTHER STUFFS
#FILL PASSWORD
import smtplib, ssl
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart

sender_email = "sender@gmail.com"
receiver_email = "receiver@outlook.es"
password = "*****"

message = MIMEMultipart("alternative")
message["Subject"] = "Aqui va el asunto"
message["From"] = sender_email
message["To"] = receiver_email

text = """\
En esta parte va el texto plano, quiere decir el texto sin codigo HTML en caso 
llegara a fallar el protocolo de traduccion del correo que recibe este mensaje
"""
html = """\
<html>
  <body>
    <p>Holi <br>
      y en esta parte va el texto central, escrito en HTML<br>
    </p>
  </body>
</html>
"""

part1 = MIMEText(text, "plain")
part2 = MIMEText(html, "html")
message.attach(part1)
message.attach(part2)


context = ssl.create_default_context()
with smtplib.SMTP_SSL("smtp.gmail.com", 465, context=context) as server:
    server.login(sender_email, password)
    server.sendmail(
        sender_email, receiver_email, message.as_string()
    )
 
```
El codigo se divide en 3 partes principales, la declaración de correos(variables), declaración del mensaje a enviar(En formato HTML) y la creación de una conexion segura para el envio del correo mediante protocolo SMTP.

### Declaración de correos
En esta aprte se introducen esas variables a primera vista es bastante sencillo de entender primero se importan las librerias previamente instaladas:
```python

#MIME WORKS WITH ANY ASCII CHARACTER ALSO ALLOWS TO SEND HTML, IMAGES, VIDEOS, AND OTHER STUFFS
#FILL PASSWORD
import smtplib, ssl
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
```

Y se definen las variables sender y receiver email donde guardaremos los correos de quien reciba y quien envia, asimismo la clave del correo gmail que sirve para enviar. Asimismo definir el asunto del mensaje.
```python
sender_email = "sender@gmail.com"
receiver_email = "receiver@outlook.es"
password = "*****"

message = MIMEMultipart("alternative")
message["Subject"] = "Aqui va el asunto"
message["From"] = sender_email
message["To"] = receiver_email
 ```
 
 ### Declaración del mensaje
Como se dijo desde el inicio de este repositorio el mensaje sera codificado en HTML, esto nos permite introducir imagenes, listas, colores, links y una variedad de otros elementos que de manejarlos bien puede dar una vision muy profesional a los correos a enviar. Se suele codificar 2 mensajes(uno en texto plano `txt` y otro en html `html`)porque algunos servidores no procesan de manera adecuada el codigo html, cuando esto sucede el correo automaticamente muestra el mensaje en texto plano para evitar la perdida del correo enviado.
```python
text = """\
En esta parte va el texto plano, quiere decir el texto sin codigo HTML en caso 
llegara a fallar el protocolo de traduccion del correo que recibe este mensaje
"""
html = """\
<html>
  <body>
    <p>Holi <br>
      y en esta parte va el texto central, escrito en HTML<br>
    </p>
  </body>
</html>
"""
 ```
Una vez ingresados el mensaje en ambas formas (como texto plano y como codigo html) se procede a componer un solo mensaje, para eso se utiliza la librería previamente  importanda MIMEText, esta nos permite agrupar 2 partes diferentes de texto, una en html y la otra en texto plano.
 
```python
part1 = MIMEText(text, "plain")
part2 = MIMEText(html, "html")
message.attach(part1)
message.attach(part2)
 ```
 
 ### Conexión bajo el protocolo SMTP
 El  codigo propuesto en esta parte utiliza la libreria smtp de python, esta libreria a su vez utiliza el protocolo de comunicación smtp, que es el mismo que usualmente usan los servicios tradicionales de correos para enviar y recibir mensajes.
 
```python
context = ssl.create_default_context()
with smtplib.SMTP_SSL("smtp.gmail.com", 465, context=context) as server:
    server.login(sender_email, password)
    server.sendmail(
        sender_email, receiver_email, message.as_string()
    )
 
```
la sentencia `context = ssl.create_default_context()` genera una conexion segura a la capa de aplicación, pues es en esta en la que se pueden utilizar directamente los protocolos de comunicación (telnet, http, smtp, ftp), una vez generado este acceso, se envia una señal utlizando el protocolo SMTP y el puerto 465.
Para la configuración de este envío se necesitan parámetros como el correo que se  utilizará como sender, su contraseña, y el mensaje.

## Automatización de envío masivo de correos
Una vez entendido el uso básico del protocolo smtp y de la librería MIME para enviar mensajes entonces solo queda definir un codigo que permita iterar consecutivamente y enviar mensajes masivamente, obviamente la aplicación que en este repositorio se le dará, será el envio de los correos con la información personal que se requiere en un torneo de debate (link personal, estado de su inscripción). Obviamente a partir de este repositorio se pueden enviar una serie  de otros archivos, documentos, flyers, sonidos y toda clase de cosa que pueda ser enviada bajo el estandar MIME, para más información respecto a los límites en el envío de correos puede consultar la documentación en los siguientes links: [MIME Documentation](https://docs.python.org/2/library/email.mime.html),[RFC Editor](https://www.rfc-editor.org/search/rfc_search_detail.php)
Para esta tercera parte, explicaré solo la parte que fue adherida para este rol, el código lo peude encontrar en el archivo `FinalSender.ipynb` :

```python

#MADE BY: Jürgen Anders Guerra Ramos
#Active this https://myaccount.google.com/lesssecureapps in your google account and accept that you are who is doing this activity
#Based in: https://realpython.com/python-send-email/#sending-fancy-emails
import smtplib, ssl
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
import csv    


sender_email = 'correo@gmail.com'
password = 'clave'
path=r'C:\dondeestentusdatos\datos.csv'

def  send_mail(subject, msg, receiver_email):
    message = MIMEMultipart("alternative")
    message["Subject"] = subject
    message["From"] = sender_email
    message["To"] = receiver_email

    # Create the plain-text and HTML version of your message
    part1 = MIMEText(msg, "plain")

    # Add HTML/plain-text parts to MIMEMultipart message
    message.attach(part1)
    # Create secure connection with server and send email
    context = ssl.create_default_context()
    with smtplib.SMTP_SSL("smtp.gmail.com", 465, context=context) as server:
        server.login(sender_email, password)
        server.sendmail(
            sender_email, receiver_email, message.as_string()
        )
        
        
file = open(path, newline = '')
reader=csv.reader(file)
header = next(reader) #extract the first line as header
data = [row for row in reader]
a=len(data)
done =0

for lel in range(a):
    subject= 'Tema del correo'
    Name =  data[lel][0] #lel is important cause it made me spent more than 4 hours debugging this app
    kul=lel
    lol=lel
    Link = data[kul][2]
    receiver = data[lol][1]
    msg =  """
Este tambien puede estar en html.

Aqui va tu contenido
"""
    send_mail(subject,msg,receiver)
    done = done +1
    print(Name + " " + receiver+ " ............. Done [" +str(done)+"/"+str(a)+"]")
```
Este código a su vez trae 3 partes nuevas, extracción de datos de un archivo .csv, formulación del mensaje, definición de una función para poder enviar mensajes repetidas veces.
### extracción de datos de un archivo .csv
Se extraen los datos utilizando las sentencias usadas en diferentes partes del código.
 Primero se define un path(dirección) donde se encuentran los datos `path=r'C:\dondeestentusdatos\datos.csv'`
Después se utiliza la sentencia `open()` para pasar los datos a una matriz en python:
``` python
file = open(path, newline = '')
reader=csv.reader(file)
header = next(reader) #extract the first line as header
data = [row for row in reader]
a=len(data)
done =0
```
Se define la primera columna como la cabecera que contiene los titulos de cada parámetro de los registros ingresados. Los demás datos son guardados en la matriz data. En la base de datos que yo administre solo se tenían los parámetros nombre, correo y link personal pero dependiendo de la aplicación se pueden aumentar más parámetros en los registros.

### formulación del mensaje
El mensaje se genera adjuntando datos que hemos extraido previamente del archivo csv, por ejemplo para la aplicación que yo utilice requería el nombre y el link personal de cada registro(ambos estarían incluidos en el mensaje) y el correo electrónico que es utilizado como recipiente del mensaje. Este mensaje puede tambien estar escrito en html y puede igual poder ser enviado fácilmente.

``` python
for lel in range(a):
    subject= 'Tema del correo'
    Name =  data[lel][0] #lel is important cause it made me spent more than 4 hours debugging this app
    kul=lel
    lol=lel
    Link = data[kul][2]
    receiver = data[lol][1]
    msg =  """
Este tambien puede estar en html.

Aqui va tu contenido
"""
```
Una vez diseñado el mensaje adecuado para cada uno de los correos se envían iterativamente, es por eso que se utiliza un bucle for para cada uno de los mensajes.
```python
    send_mail(subject,msg,receiver)
    done = done +1
    print(Name + " " + receiver+ " ............. Done [" +str(done)+"/"+str(a)+"]")
```
Estas útimas sentencias que también van dentro del bucle for utilizan una función definida líneas más arriba, después de eso, dependiendo del éxito del envío muestran en consola el estado de cada uno de los mensajes. 
### Función send_mail
Para poder enviar mensajes iterativamente se definió una función para enviar los emails, esta función requiere como variable de entrada el asunto, el mensaje y el correo al que se le debe de enviar el mensaje.
```python
def  send_mail(subject, msg, receiver_email):
    message = MIMEMultipart("alternative")
    message["Subject"] = subject
    message["From"] = sender_email
    message["To"] = receiver_email

    # Create the plain-text and HTML version of your message
    part1 = MIMEText(msg, "plain")

    # Add HTML/plain-text parts to MIMEMultipart message
    message.attach(part1)
    # Create secure connection with server and send email
    context = ssl.create_default_context()
    with smtplib.SMTP_SSL("smtp.gmail.com", 465, context=context) as server:
        server.login(sender_email, password)
        server.sendmail(
            sender_email, receiver_email, message.as_string()
        )
        
```
La lógica existente dentro de esta función responde a lo que previamente explicamos en la sección anterior, es basicamente el primer código explicado, con unas ligeras modificaciones para poder recibir las variables que se requieren para su funcionamiento.

## Alternativa de Wsp con selenium y WebDriver

# UNDER CONSTRUCTION . . .
