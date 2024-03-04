# Máquina: Blaster

**Tryhackme: Blaster**

Lo primero que haremos, será lanzar un NMAP para ver qué puertos tiene abiertos la máquina:

![BLTR1](https://github.com/AntonioPC94/Blaster/blob/c36ac88f386589d0809b235a4b29ac9e8803823c/Img/BLTR1.png)
![BLTR2](https://github.com/AntonioPC94/Blaster/blob/c36ac88f386589d0809b235a4b29ac9e8803823c/Img/BLTR2.png)

En la imagen anterior podemos ver tres puertos abiertos:

- Puerto 80: Servicio HTTP
- Puerto 3389: Servicio RDP
- Puerto 5985: Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)

A continuación, lanzaremos de nuevo NMAP pero cargando su módulo de detección de vulnerabilidades (vuln) para intentar encontrar alguna vulnerabilidad en los servicios anteriormente mencionados.

![BLTR3](https://github.com/AntonioPC94/Blaster/blob/c36ac88f386589d0809b235a4b29ac9e8803823c/Img/BLTR3.png)

Como se observa en la imagen anterior, a primera vista ninguno de los servicios anteriormente mencionados son vulnerables.

Aun así, vamos a empezar a escarbar en cada uno de ellos a ver qué podemos sacar.

Empezando por el puerto 80, podemos observar que contiene una página web llamada: IIS Windows Server.

![BLTR4](https://github.com/AntonioPC94/Blaster/blob/c36ac88f386589d0809b235a4b29ac9e8803823c/Img/BLTR4.png)

Lo primero que vamos a hacer con esta página, es hacerle fuzzing con Dirbuster para ver si oculta algún fichero/directorio importante.

Tras configurar y lanzar Dirbuster contra la página, nos encontraremos con los siguientes ficheros/directorios ocultos:

![BLTR5](https://github.com/AntonioPC94/Blaster/blob/c36ac88f386589d0809b235a4b29ac9e8803823c/Img/BLTR5.png)

Encuentra muchos más, pero de momento nos quedaremos con el segundo directorio que nos ha mostrado, ya que este parece interesante.

Si colocamos la dirección de dicho directorio en la URL de la página, nos encontraremos con la siguiente página web:

![BLTR6](https://github.com/AntonioPC94/Blaster/blob/c36ac88f386589d0809b235a4b29ac9e8803823c/Img/BLTR6.png)

Mirando por la página, veremos que un usuario cuyo nombre no vamos a mostrar, ha sido el creador de las distintas publicaciones que hay en la página.

![BLTR7](https://github.com/AntonioPC94/Blaster/blob/c36ac88f386589d0809b235a4b29ac9e8803823c/Img/BLTR7.png)

Este usuario ha realizado algunos comentarios en la web y en uno de ellos, ha escrito lo que parece ser una contraseña.

![BLTR8](https://github.com/AntonioPC94/Blaster/blob/c36ac88f386589d0809b235a4b29ac9e8803823c/Img/BLTR8.png)

Tras haber encontrado el nombre de uno de los usuarios de la página y lo que supuestamente es una contraseña, vamos a probar dichas credenciales en el "login" de la página para ver si podemos acceder al panel de administración:

![BLTR9](https://github.com/AntonioPC94/Blaster/blob/c36ac88f386589d0809b235a4b29ac9e8803823c/Img/BLTR9.png)
![BLTR10](https://github.com/AntonioPC94/Blaster/blob/c36ac88f386589d0809b235a4b29ac9e8803823c/Img/BLTR10.png)

Como observamos en la siguiente imagen, estamos dentro del panel de administración de la página web.

![BLTR11](https://github.com/AntonioPC94/Blaster/blob/c36ac88f386589d0809b235a4b29ac9e8803823c/Img/BLTR11.png)

Si nos vamos al apartado "Usuarios" de la página, podremos encontrar el correo electrónico del único usuario creado y podremos verificar que este es el administrador de dicha página.

![BLTR12](https://github.com/AntonioPC94/Blaster/blob/805faac491494b1ac605ef85c1c3839fa8b020bf/Img/BLTR12.png)

Nota: A partir de aquí, la dirección IP de la máquina objetivo cambia porque la he abierto en otra red distinta.

Bien, ahora iremos a por el puerto 3389, al cual nos intentaremos conectar utilizando un programa llamado Remmina. Una vez lo instalemos, lo ejecutaremos e introduciremos la dirección IP de la máquina objetivo para poder conectarnos a ella.

![BLTR13](https://github.com/AntonioPC94/Blaster/blob/805faac491494b1ac605ef85c1c3839fa8b020bf/Img/BLTR13.png)

Cuando nos intentemos conectar, el programa nos pedirá que aceptemos el certificado del dominio si es que nos queremos conectar a él.

![BLTR14](https://github.com/AntonioPC94/Blaster/blob/805faac491494b1ac605ef85c1c3839fa8b020bf/Img/BLTR14.png)

Una vez hayamos aceptado el certificado, el programa nos pedirá las credenciales del usuario al que nos queremos conectar y el nombre del dominio al que pertenece.

Como las únicas credenciales que tenemos son las que nos encontramos anteriormente en la web, vamos a probarlas aquí también por si acaso funcionasen.

![BLTR15](https://github.com/AntonioPC94/Blaster/blob/805faac491494b1ac605ef85c1c3839fa8b020bf/Img/BLTR15.png)

Nota: Para encontrar el nombre del dominio, podemos utilizar el siguiente módulo de Metasploit: scanner/rdp/rdp_scanner

![BLTR16](https://github.com/AntonioPC94/Blaster/blob/805faac491494b1ac605ef85c1c3839fa8b020bf/Img/BLTR16.png)

Como observamos en la imagen anterior, hemos conseguido acceder al sistema objetivo. En el escritorio del usuario localizaremos un fichero de texto llamado "user.txt", el cual tendrá la primera flag.

![BLTR17](https://github.com/AntonioPC94/Blaster/blob/805faac491494b1ac605ef85c1c3839fa8b020bf/Img/BLTR17.png)

Indagando en el sistema objetivo, concretamente en el historial de navegación de Internet Explorer, encontramos que el usuario estuvo buscando información acerca de un CVE, el CVE-2019-1388. Esto nos podría venir bien a la hora de seguir elevando nuestros privilegios en el sistema.

También en el escritorio de la máquina vulnerada, encontraremos un ejecutable (hhupd.exe) el cual tiene que ver con el CVE que encontramos anteriormente. Este archivo malicioso nos abrirá el "Cuadro de Diálogo de Certificados de Windows" de una manera que no aplica correctamente las restricciones de privilegios.

Así que, vamos a utilizarlo para ver si podemos ganar una shell con privilegios elevados en el sistema objetivo.

![BLTR18]()

Lo primero que haremos, será asegurarnos de que el fichero tiene los permisos suficientes como para ser ejecutado en el sistema.

Una vez nos hayamos asegurado de lo anterior, lo abriremos con permisos de administrador, abriremos el desplegable que nos sale y seleccionaremos la opción marcada en la siguiente imagen:

![BLTR19]()

Bien, una vez hayamos clickado sobre dicha opción, se nos abrirá otra ventanita que nos mostrará información sobre el certificado de dicho ejecutable.

A continuación, le daremos a la siguiente opción y esperaremos a que se nos abra el navegador que traiga por defecto la máquina, que seguramente será Internet Explorer.

![BLTR20]()

La página que se nos abrirá a continuación, estará corriendo sobre un proceso con privilegios elevados.

![BLTR21]()

A pesar del error, vamos a descargarnos el contenido de la página web al completo yéndonos a las opciones del navegador (Engranaje) y luego al apartado "File", en el cual encontraremos la opción "Save as".

![BLTR22]()

Como se observa en la imagen anterior, nos salta un error que nos indica que la dirección donde queremos guardar dicho archivo, no está disponible, eso nos dará igual ya que lo que queremos nosotros es solicitar una shell con permisos elevados. Para ello, la llamaremos desde la misma ventana del "Guardar como".

![BLTR23]()

Una vez tengamos todos los ficheros de dicha carpeta a la vista, buscaremos el programa "cmd", le daremos click derecho y lo abriremos.

![BLTR24]()

![BLTR25]()

Una vez abierto, veremos como tendremos una shell con permisos elevados.

![BLTR26]()

Otra vía que se podría seguir para explotar esta máquina, sería utilizando un exploit de Metasploit llamado: 

