Crearemos una forma de contacto apra mandar correos con Django atraves del servicio "Mailtrap"

comenzamos inicializando el git atra vez del comando 
``git init``

Luego creamos nuestro entorno virtual con el comando 
``virtualenv -p python3 env``

lo inicializamos y ahora isntalamos Django y despues iniciamos nuestro proyecto ``django-admin startproject djangocontactform``

damos ``cd djangocontactform`` y dentro creamos nuestra app con 
``python manage.py startapp contact``

Ya creadoesto podemos seguir en Visual Studio con un ``code .``  (esta bien en este directorio) y se vera mas o menos asi
![[Pasted image 20220621224422.png]]

y vamos a settings y en INSTALLED_APPS ponemos nuestra aplicacion 'contact'
![[Pasted image 20220622120330.png]]

si corremos esto en nuestro buscador nos dara un error ya que aun no tenemos nuestro template, ais que vamos a crearlo en '/contact/templates/contact/index.html'

ahora vamos a creas una views simple para nuestra formato de contacto, para esto vamos  a contact/views y agregamnos la siguiente funcion

```
from django.shortcuts import render

  

def index(request):

    return render(request, 'contact/index.html')
```

y luego en /djangocontactform/urls.py registramos su path

```
from django.contrib import admin

from django.urls import path

  
from contact.views import index
  

urlpatterns = [

    path('', index, name='index'),

    path('admin/', admin.site.urls),
```
![[Pasted image 20220622121601.png]]