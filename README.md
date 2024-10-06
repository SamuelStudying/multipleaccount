
## Primeros pasos

Vamos a partir de cero para que cualquier persona pueda seguir esta guía. Es por eso que lo primero que vamos a realizar será generar nuestras llaves privadas (ssh-keys). Lo ideal es crear una por cada cuenta.

### 1. Creando nuestras llaves privadas
   
El proceso es muy similar en Windows / Linux y Mac OS, por ahora sigue los pasos sin importar tu sistema operativo.

Este comando creará un par de llaves, una pública y una privada.

`ssh-keygen -t rsa -b 4096 -C "email"`

Corremos el siguiente comando para la validación del ***ssh-agent***

`eval $(ssh-agent -s)`
#

> [!WARNING]
> Aquí es cuando el proceso en MacOS es un poco diferente, ya que, lo que haríamos en un caso normal con una sola cuenta sería generar un archivo config, pero en este caso como vamos a agregar más de una y eso será un paso que nos vamos a saltar.

#

Por último agregamos la llave privada al sistema. En este caso los comandos son ligeramente distintos en Mac.

### Windows y Linux

`ssh-add ~/.ssh/id_rsa_personal`

### Mac OS

`ssh-add -K ~/.ssh/id_rsa_personal`

Ahora únicamente debemos copiar el contenido nuestra llave pública, (la que termina en .pub) y llevarlo a GitHub. Para ello solo debes de hacer el siguiente comando en tu terminal:

`cat ~/.ssh/id_rsa_personal.pub`

Ahora, vas a --> [GitHub > settings > SSH and GPG keys > New SSH key]

#

> [!NOTE]
> ¡Y listo! Ahora este mismo proceso deberás hacerlo por cada cuenta adicional que vayas a agregar, para efectos de este tutorial vamos a suponer que ya generaste tus llaves, la de tu cuenta personal, la de estudios y la de la cuenta de tu trabajo.

#

Te recomiendo cambiarles el nombre para que las puedas distinguir fácilmente. Por ejemplo así lo hice yo:

```
id_rsa_personal #llave privada para mi cuenta personal
id_rsa_personal.pub  #llave publica para mi cuenta personal

id_rsa_trabajo #llave privada para mi cuenta de trabajo
id_rsa_trabajo.pub #llave publica para mi cuenta de trabajo

id_rsa_estudio #llave privada para mi cuenta de estudio
id_rsa_estudio.pub #llave publica para mi cuenta de estudio
```

### 2. Editando el archivo .gitconfig
   
Por lo general deberíamos de tener un archivo en nuestro root que sea donde viven las configuraciones de git el cual luce más o menos así

```
[user]
  name        = <Your name>
  email       = <Your email>
  signingKey  = <Your signingKey> (ruta del archivo shh-key)

[init]
  defaultBranch = main

...
[other configs]
...
```

Lo que necesitaremos será remover todo el bloque **[user]** y dejar solamente las configuraciones generales.

Para ello puedes usar el siguiente comando:

`nano ~/.gitconfig`

Al final deberías de tener el archivo más o menos así:

```
[init]
  defaultBranch = main

...
[other configs]
...
```

## 3. Añadiendo nuestras propias configuraciones
   
Ahora debes crear un archivo por cada cuenta que quieras agregar, supongamos que quieres agregar tu cuenta de GitHub asociada a tu correo personal y otra cuenta asociada con el correo de tu trabajo.

Para crear los archivos podemos ejecutar el siguiente comando:

```
touch ~/.gitconfig.personal ~/.gitconfig.trabajo ~/.gitconfig.estudio
```

Cabe resaltar que el **.personal**, **.estudio** y **.trabajo** no tienen que ser exactamente estos, puedes ponerles el nombre que más te parezca, solo recuerda que deben ser distintos entre ellos.

Ahora debemos añadir las configuraciones para cada cuenta. Empecemos con el archivo **.gitconfig.personal**, aquí pondremos el bloque **[user]** que retiramos en el paso anterior.

`nano ~/.gitconfig.personal`

Así deberíamos de tener el archivo **.gitconfig.personal**

```
[user]
  name        = <Your name>
  email       = <Your email>
  signingKey  = <Your signingKey>
```

Ahora hacemos lo mismo para las otras cuentas, la de estudio y la del trabajo.

`nano ~/.gitconfig.trabajo`

Así deberíamos de tener el archivo **.gitconfig.trabajo**

```
[user]
  name        = <Your name>
  email       = <Your work@email>
  signingKey  = <Your signingKey>

[url "git@trabajo.github.com"]
  insteadOf = git@github.com
```

Y lo mismo para la otra.

`nano ~/.gitconfig.estudio`

Así quedaría el archivo **.gitconfig.estudio**

```
[user]
  name        = <Your name>
  email       = <Your study@email>
  signingKey  = <Your signingKey>

[url "git@estudio.github.com"]
  insteadOf = git@github.com
```

> _Aquí agregaremos algo adicional, y es un subhost personalizado. Debemos de tenerlo en cuenta para un paso que haremos más adelante._

¡Ya casi hemos acabado, falta poco!

## 4. Definiendo las rutas de trabajo
   
Ahora debemos de decirle a git que cuando creemos o descarguemos un proyecto, sepa que cuenta usar, y para esto debemos automatizarlo, para no tener que hacerlo manualmente cada vez que creemos o descargaremos proyectos.

Para esto debemos definir que cuando creemos o descargamos un proyecto en una ruta, tome una configuración de cuenta, y de hacerlo en otra ruta tome otra configuración de cuenta.
> Parecido a un bloque de condicional **if()**.


Puedes definirlo de la siguiente manera:

Vas a crear una carpeta en la ruta que tú prefieras para almacenar todos tus proyectos personales, para simplicidad en este tutorial lo haremos así, pero recuerda que tú puedes hacerlo en cualquier ruta.

- `~/Personal/` Esta será la ruta donde yo guardaré mis proyectos personales
- `~/Estudio/` Esta será la ruta donde yo guardaré los proyectos que tengan que ver los estudios
- `~/Trabajo/` Esta será la ruta donde yo guardaré los proyectos que tengan que ver con el trabajo

Para crear las carpetas de manera rápida puedes usar el siguiente comando en tu terminal:

`mkdir ~/Personal ~/Trabajo ~/Estudio`

Ahora a nuestro archivo **.gitconfig** el que es el general, el archivo base, le agregaremos las siguientes lineas:

```
[include]
  # Valor por default para git; de esta manera, por defecto siempre usaremos nuestra cuenta personal
  path = ~/.gitconfig.personal

[includeIf "gitdir:~/Personal/"]
  # Todos los proyectos que descarguemos o creemos a partir de esta ruta, usara tu cuenta personal
  # este bloque es opcional, pero debes de asegurarte de colocar el primer bloque.
  path = ~/.gitconfig.personal

[includeIf "gitdir:~/Estudio/"]
  # Todos los proyectos que descarguemos o creemos a partir de esta ruta, usara tu cuenta de estudio
  path = ~/.gitconfig.estudio

[includeIf "gitdir:~/Trabajo/"]
  # Todos los proyectos que descarguemos o creemos a partir de esta ruta, usara tu cuenta de tu trabajo
  path = ~/.gitconfig.trabajo

[init]
  defaultBranch = main

...
[other configs]
...
```
> [!CAUTION]
> En caso de Windows y de almacenar las carpetas de tus proyectos en un un disco duro distinto del raíz "C:", recuerda que debes añadir la ruta de esta forma `[includeIf "gitdir:D:/Projects/Estudio/"]` de lo contrario no lo encontrará y usará la configuración establecida en `[include]`.

## 5. Agregando la configuración de ssh
   
Ahora debemos modificar o crear un archivo **config** dentro de tu carpeta **~/.ssh** y escribir lo siguiente.

Puedes usar el siguiente comando:

`nano ~/.ssh/config`

```
Host github.com
  User          <your github username personal account>
  HostName      github.com
  IdentityFile  ~/.ssh/id_rsa_personal # La ruta a tu llave privada asociada a tu cuenta personal

# Este host tiene que ser el mismo que pusimos en el paso tres
Host estudio.github.com
  User          <your github username study account>
  HostName      github.com
  IdentityFile  ~/.ssh/id_rsa_estudio # La ruta a tu llave privada asociada a tu cuenta de estudio

# Este host tiene que ser el mismo que pusimos en el paso tres
Host trabajo.github.com
  User          <your github username work account>
  HostName      github.com
  IdentityFile  ~/.ssh/id_rsa_trabajo # La ruta a tu llave privada asociada a tu cuenta de trabajo

Host *
  AddKeysToAgent            yes
  IdentitiesOnly            yes
  UseKeychain               yes # Solo para MacOS
  Compression               yes
  PreferredAuthentications  publickey
```

¡Felicidades, eso sería todo! ¡No olvides agregar tus llaves públicas a tus cuentas de GitHub!

## Validaciones

Si quieres probar que todo este OK, lo podemos hacer desde nuestra terminal, solo debemos de correr los siguientes comandos:

```
ssh -T git@github.com
ssh -T git@trabajo.github.com
ssh -T git@estudio.github.com
```
Lo que debería de salirte sería algo así, para cada cuenta:

```
The authenticity of host github.com (#######) can not be established.
###### key fingerprint is SHA256
This key is not known by any other names

Are you sure you want to continue connecting (yes/no/[fingerprint])? **yes**
Warning: Permanently added github.com (####) to the list of known hosts.
Hi <Your github username>! You've successfully authenticated, but GitHub does not provide she
```
