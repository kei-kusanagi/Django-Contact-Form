Crearemos una forma de contacto apra mandar correos con Django atraves del servicio "Mailtrap"

comenzamos inicializando el git atra vez del comando 
``git init``

Luego creamos nuestro entorno virtual con el comando 
``virtualenv -p python3 env``

lo inicializamos y ahora isntalamos Django y despues iniciamos nuestro proyecto ``django-admin startproject djangocontactform``

damos ``cd djangocontactform`` y dentro creamos nuestra app con 
``python manage.py startapp contact``

Ya creadoesto podemos seguir en Visual Studio con un ``code .``  (esta bien en este directorio) y se vera mas o menos asi
![image](README%20img/Pasted%20image%2020220621224422.png)

y vamos a settings y en INSTALLED_APPS ponemos nuestra aplicacion 'contact'
![image](README%20img/Pasted%20image%2020220622120330.png)

si corremos esto en nuestro buscador nos dara un error ya que aun no tenemos nuestro template, ais que vamos a crearlo en '/contact/templates/contact/index.html'

ahora vamos a creas una views simple para nuestra formato de contacto, para esto vamos  a contact/views y agregamnos la siguiente funcion

```
from django.shortcuts import render

  

def index(request):

    return render(request, 'contact/index.html')
```

y luego en /djangocontactform/urls.py registramos su path, el que va sin nada es porque es el index y hay que importarlo de cviews con from contact.views

```
from django.contrib import admin

from django.urls import path

  
from contact.views import index
  

urlpatterns = [

    path('', index, name='index'),

    path('admin/', admin.site.urls),
```
![image](README%20img/Pasted%20image%2020220622121601.png)

ahora crearemos nuestra forma para guardar los datos en la base de datos, en /contact/forms.py

```
from django import forms

class ContactForm(forms.Form):

    name = forms.CharField(max_length=255)

    email = forms.EmailField()

    content = forms.CharField(widget=forms.Textarea)
```

Salvamos esto y ahora pasamos a /contact/views.py  y agregamos lasiguiente funcion para que nos pueda arrojar ese index y que tambien reciva form

```
from django.shortcuts import render

from.forms import ContactForm

def index(request):

    form = ContactForm()

    return render(request, 'contact/index.html', {

        'form': form

    })
```

ahora vamos a nuestro template y escrivimos lo siguiente para mandar a traer nuestra form y se vera asi

```
<form method="post" action=".">

            {% csrf_token %}


            {{ form.as_p }}

            <button>Submit</button>

        </form>
```
![image](README%20img/Pasted%20image%2020220622183010.png)

se ve bonito epro falta al validacion, asi que regresamos a nuestro archivo /contact/views.py y le agregamos los siguientes if's

```
from django.shortcuts import render

from.forms import ContactForm

def index(request):

    if request.method == 'POST':

        form = ContactForm(request.POST)


        if form.is_valid():

            print('the form was valid')
            return redirect('index')

    else:

        form = ContactForm()

    return render(request, 'contact/index.html', {

        'form': form

    })
```

ahora para hacer una validacion de Email vamos a /djangocontactform/settings.py y ponemos lo siguiente arriba de INSTALLED_APPS

``EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'``

yy en nuestro archivo views.py agregamos el siguiente codigo para establecer el cuerpo del E-mail

```
from django.core.mail import send_mail

from django.shortcuts import render, redirect

  
from.forms import ContactForm

def index(request):

    if request.method == 'POST':

        form = ContactForm(request.POST)
  

        if form.is_valid():

            print('the form was valid')
  

            send_mail('The contact form subject', 'This is the message', 'noreply@velvetit.com', ['kei.kusanagi.99@gmail.com'])


            return redirect('index')

    else:

        form = ContactForm()

    return render(request, 'contact/index.html', {

        'form': form

    })
```

actualizamos nuestra pagina y llenamos unos datos de prueba
![image](README%20img/Pasted%20image%2020220622194326.png)

al darle en Submit nos deve llevar a la pagina de inicio y en la consola nos saldra lo siguiente
![image](README%20img/Pasted%20image%2020220622200700.png)

validando que se si se enviaria el correo corespondiente, ahora usaremos html para enviar estos mensajes, para esto vamos a views.py y añadamos lo siguiente dentro de la funcion index
```
from django.core.mail import send_mail

from django.shortcuts import render, redirect

from django.template.loader import render_to_string


from.forms import ContactForm
  

def index(request):

    if request.method == 'POST':

        form = ContactForm(request.POST)
  

        if form.is_valid():

            name = form.cleaned_data['name']

            email = form.cleaned_data['email']

            content = form.cleaned_data['content']


            html = render_to_string('contact/emails/contactform.html', {

                'name' : name,

                'email' : email,

                'content' : content

            })

            send_mail('The contact form subject', 'This is the message', 'noreply@velvetit.com', ['kei.kusanagi.99@gmail.com'], html_message=html)

  

            return redirect('index')

    else:

        form = ContactForm()

    return render(request, 'contact/index.html', {

        'form': form

    })
```



crearemos una template vacia solo para apuntar alli en /contact/templates/contact/emails/contactform.html
![image](README%20img/Pasted%20image%2020220622213618.png)

ahora salvamos y corremos todo nuevamente y llenamos la forma de contacto, y en la terminal nos mostrara hola hola hola si todo funciono...

![image](README%20img/Pasted%20image%2020220622214015.png)
Perfecto 😀 ahora podemos quitar ls hola's y agragar una verdadera template para nuestra forma de contacto, vamos entonces a contactform.html y añadamos lo siguiente:

```
<h2>Contact form submission</h2>


<p><b>Name: </b> {{ name }}</p>

<p><b>E-mail: </b> {{ email }}</p>

<p><b>Message: </b> {{ content }}</p>
```

llenamos nuevamente nuestra forma y la enviamos y deve salir asi:
![image](README%20img/Pasted%20image%2020220622214415.png)

Bien parece que todo esta configurado, asi que ahora iremos a www.mailtrap.io y nos registramos para pdoer crear una cuenta gratis y usar su servicio y poder testear nuestra app y el envio de los correos, ya logeados en la configuracion de inbox elejimos Python/Django...
![image](README%20img/Pasted%20image%2020220622214810.png)
copiamos esas cuatro lineas y settings.py remplazamos nuestro ``EMAIL_BACKEND`` con esto
![image](README%20img/Pasted%20image%2020220622214906.png)

salvamos y volvemos a enviar nuestra forma de contacto y veamos la magia 📩
![image](README%20img/Pasted%20image%2020220622215352.png)
![image](README%20img/Pasted%20image%2020220622215519.png)

Y listoooooooooooo no se olviden de suscribirse 🔺 dar like 👍y activar la campanita 🔔... a no cierto, bueno eso es todo 😜