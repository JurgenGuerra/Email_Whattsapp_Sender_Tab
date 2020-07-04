# Alternativa a SendGrid para el envío automático y funcional de correos a los equipos de lxs debatientes utilizando Python y HTML:
Este repositorio contiene una alternativa al uso de SendGrid para enviar correos automaticamente. Esta iniciativa surge de la necesidad de enviar correos masivos y automáticos a lxs debatientes en los torneos de debate Parlamentario Británico, sin embargo su uso no esta *limitado* y se puede usar para practicamente cualquier aplicación que requiera enviar correos masivamente sin el uso de algún software de **paga**.
Asimismo se plantea una alternativa(igualmente automática), para aquellos casos en los que no se tiene el correo de lxs destinatarios o existe un error de tipeo. Esta alternativa usa mensajes automatizados a traves de **Whatsapp**, esta alternativa tambien puede ser de uso prioritario en caso se requiera una vía de comunicación más fluida que los correos.

Me centraré en explicar los módulos del código más importantes, los códigos completos esta en este repositorio a su **libre** disposición.
Las partes de más importantes en las que se dividirá este artículo serán:
- Librerías a Instalar
- Uso de smtp y MIME
- Automatización del envío masivo de correos
- Alternativa de Wsp con selenium y WebDriver

Vamos :3

## Librerías a Instalar
Para empezar tiene que tener en cuenta 2 cosas, este código esta escrito en pytho debido a su facilidad de programación y entendimiento pero eso no significa que se pueda replicar en otra clase de lenguajes, creo que en tanto el propósito se cumpla todo estará bien :3, lo segundo es que las librerias a utilizar solo facilitan el manejo de puertos y servidores necesarios para poder enviar un mensaje automaticamente. 

Antes de importar las librerias estas deben ser instaladas con el comando `pip install`, solo instalaremos smtplib ya que MIME y csv vienen por defecto con el paquete de instalación de python, en caso no fuere asi existen diversos repositorios para la instalación de estas otras librerías, basta con buscar: "como instalar [inserte nombre de libreria] en python" y le aparecera un sinfin de tutoriales y repositorios:
```python
pip install smtplib
```
Las librerías a instalar se utilizan para implementar protocolos de envio de correos a traves de los puertos del computador, **smtplib** se usa para usar los Protocolos de envio de correos SMTP, **MIME** para estandarizar los correos y poder enviar correos en formato HTML (con imagenes, videos y links), **csv** sirve para manejar datos en formato Coma Separated Value que pueden ser facilmente editables con excel.
Adicionalmente se debe instalar la librería **selenium** para el uso de WebDriver (en la parte de la alternativa se verá más a detalle esto).

```python
import smtplib, ssl 
from email.mime.text import MIMEText 
from email.mime.multipart import MIMEMultipart 
import csv 
```
una vez instalados e importados los paquetes de librería procederé a explicar el código.

## Uso del smtp y MIME 
Para esta parte es importante comprender cada parte del codigo en especial la parte de la apertura del server. Antes una aclaración importante, gran parte de este código es reusado, pues muchos de los temas aquí tratados son a su vez abordados en repositorios con temas parecidos, lo único que hago es diseccionar un poco y despues automatizar el envio de estos correos.
**IMPORTANTE** para el uso de los protocolos smtp es necesario que actives el acceso para aplicaciones menos seguras de gmail, puede parecer peligroso pero basicamente google tiene un sistema que solo permite el acceso a tu correo a muy pocas aplicaciones externas(por protección y por monopolización del mercado), obviamente una ves acabado el trabajo es necesario que la desactives nuevamente para evitar problemas.
La activacion se hace a traves de [este link](https://myaccount.google.com/lesssecureapps) una vez ya dentro de la cuenta de correo que usarás para el envío.
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
El codigo se divide en 3 partes principales, la declaración de correos(variables), declaración del mensaje a enviar(En formato HTML) y la creación de una conexión segura para el envio del correo mediante protocolo SMTP.

### Declaración de correos
En esta primera parte se introducen las variables a usar durante el resto del código y se importan las librerías necesarias:
```python

#MIME WORKS WITH ANY ASCII CHARACTER ALSO ALLOWS TO SEND HTML, IMAGES, VIDEOS, AND OTHER STUFFS
#FILL PASSWORD
import smtplib, ssl
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
```

Se definen las variables sender y receiver email donde guardaremos los correos de quien reciba y quien envia, asimismo la clave del correo gmail que sirve para enviar. Asimismo definir el asunto del mensaje.
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
Como se dijo desde el inicio de este repositorio el mensaje será codificado en HTML, esto nos permite introducir imágenes, listas, colores, links y una variedad de otros elementos que de manejarlos bien pueden dar un acabado muy profesional a los correos a enviar. Se codifican 2 mensajes(uno en texto plano `txt` y otro en html `html`)porque algunos servidores no procesan de manera adecuada el código html, cuando esto sucede el correo automaticamente muestra el mensaje en texto plano para evitar la pérdida del correo enviado.
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
Una vez ingresados el mensaje en ambas formas (como texto plano y como codigo html) se procede a integrarlos en un solo mensaje, para eso se utiliza la librería previamente importanda MIMEText, esta nos permite agrupar 2 partes diferentes de texto, una en html y la otra en texto plano.
 
```python
part1 = MIMEText(text, "plain")
part2 = MIMEText(html, "html")
message.attach(part1)
message.attach(part2)
 ```
 
 ### Conexión bajo el protocolo SMTP
 El  código propuesto en esta parte utiliza la libreria smtp de python, esta libreria a su vez utiliza el protocolo de comunicación smtp(Simple Mail Transfer Protocol), que es el mismo que usualmente usan los servicios tradicionales de correos para enviar y recibir mensajes.
 
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
Una vez entendido el uso básico del protocolo smtp y de la librería MIME para enviar mensajes entonces solo queda definir un codigo que permita iterar consecutivamente y enviar mensajes masivamente, obviamente la aplicación que en este repositorio se le dará, será el envio de los correos con la información personal que se requiere en un torneo de debate (link personal, estado de su inscripción). A partir de este repositorio se pueden enviar una serie  de otros archivos, documentos, flyers, sonidos y toda clase de cosa que pueda ser enviada bajo el estandar MIME, para más información respecto a los límites en el envío de correos puede consultar la documentación en los siguientes links: [MIME Documentation](https://docs.python.org/2/library/email.mime.html),[RFC Editor](https://www.rfc-editor.org/search/rfc_search_detail.php)
Para esta tercera parte, explicaré solo la parte que fue adherida para este rol, el código lo puede encontrar en el archivo `FinalSender.ipynb` :

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

for n in range(a):
    subject= 'Tema del correo'
    Name =  data[n][0] 
    Link = data[n][2]
    receiver = data[n][1]
    msg =  """
Este tambien puede estar en html.

Aqui va tu contenido
"""
    send_mail(subject,msg,receiver)
    done = done +1
    print(Name + " " + receiver+ " ............. Done [" +str(done)+"/"+str(a)+"]")
```
Este código a su vez trae 3 partes nuevas: extracción de datos de un archivo .csv, formulación del mensaje, definición de una función para poder enviar mensajes repetidas veces.
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
for n in range(a):
    subject= 'Tema del correo'
    Name =  data[n][0] #lel is important cause it made me spent more than 4 hours debugging this app
    Link = data[n][2]
    receiver = data[n][1]
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
Usualmente las personas no suelen revisar sus correos, usualmente los mensajes que se envían por correo suelen perderse fácilmente, incluso muchas veces un error en el tipeo hace imposible poder contactarse con lxs oradorxs, por esos motivos planteo la posibilidad de enviar los url's personales y demás info de un torneo por Whatsapp. Esto necesariamente requiere permiso de lxs participantes y tiene limitaciones éticas en el uso de los números (al igual que en el uso de los correos).
Hechas estas aclaraciones les presento el código, la parte final que automatiza el envío es identica a la que usa el código anterior. Probablemente lo explique con menos detalle que la idea inicial, pero en caso sea necesaria alguna alguna aclaración no duden en escribirme.
El código se basa en un paquete de python (`selenium` más especificamente `webdriver`)que permite el control automático de un navegador, esto a través de la busqueda de aspectos específicos en su código HTML así como de acciones que se le puede ingresar o condiciones para que se cumplan estas acciones.
Lo primero es importar las librerías (previamente se tuvo que descarlas con `pip install selenium`):
```python

from selenium import webdriver
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By
import csv    
import time
```
Se define una función de modo que el código sea reusable para un bucle que envíe masivamente mensajes. Estas líneas de código serán descritas brevemente dado que no es objetivo de este repositorio explicarlas del todo. Igual habrán pequeñas descripciones al lado de cada linea de código.
```python
#Close all Chromes
#http://web.whatsapp.com/send?phone=99999999999 Abre el chat con el número introducido
def senderwsp(Numero, msg, reps):
    webl= "http://web.whatsapp.com/send?phone="
    Numero = str(Numero)
    webl=webl+Numero #define la pagina web a abrir
    options = webdriver.ChromeOptions();
    options.add_argument('--user-data-dir=./User_Data')
    driver = webdriver.Chrome(chrome_options=options)
    driver.get(webl)
    element = WebDriverWait(driver,100).until(EC.presence_of_element_located((By.ID, "side"))) #WAITS condición necesaria para que continue con el resto del programa, es importante cuando existe latencia en el server https://selenium-python.readthedocs.io/waits.html
    
    inp_xpath = '//div[@class="_3FRCZ copyable-text selectable-text"][@contenteditable="true"][@data-tab="1"]'  #Busca la linea HTML que tenga la clase introducida
    #Esta clase depende de cada buscador y computadora así que será su deber buscarla (más abajito encontrará más info de como conseguirla)
    msg_box = driver.find_element_by_xpath(inp_xpath)
    for i in range(reps):
        msg_box.send_keys(msg+ Keys.ENTER)
    time.sleep(7) # espera en segundos para que el mensaje sea enviado antes de que se cierre la pestaña, depende de su velocidad de internet
    driver.close()
```
Aquí la explicación detallada de como conseguir la clase, primero, la explicación que aquí encontrará esta orientada a personas que tengan Google Chrome como navegador predeterminado.
Una vez dentro de whatsapp, haga click en algún contacto y luego ubique el cursor en la caja donde se introducen los mensajes a enviar. Luego anticlick y seleccione la opción de inspeccionar, lo enviará a la zona de desarrollador y se resaltará aquella seccion que referencia a la casilla donde se inserta en texto, busque la clase `class=` más cercana y será la que usará para esta sección del código.

La siguiente sección del código es un copia y pega de lo que se uso en el código para enviar mails. Note que lo único que cambia es la función final a llamar.
```python
path=r'C:\Users\jugue\Documents\SENDAUTOWSP\numbers.csv'
file = open(path, newline = '')
reader=csv.reader(file)
header = next(reader) #extract the first line as header
data = [row for row in reader]
a=len(data)
done =0

for n in range(a):
    Name =  data[n][0] #n is the counter
    celular = data[n][1]
    link = data[n][2]
    msge =  """."""

    senderwsp(celular, msge ,1)
    done = done +1
    print(Name + " " + celular+ " ............. Done [" +str(done)+"/"+str(a)+"]")
```
Este código también esta adjuntado en el repositorio con el nombre de `finalwspsender.ipynb`, esta diseñado para permitirle enviar mensajes a personas por whatsapp si tiene los datos necesarios para hacerlo. Espero haberlo explicado adecuadamente, igual si alguna parte del código no es del todo comprensible rogaría que me lo hagan saber para poder clarificarla uwu.


