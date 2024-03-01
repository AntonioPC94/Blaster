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


