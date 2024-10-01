# Política de contraseñas

## Contexto
Por defecto, la mayoría de sistemas operativos, vienene con una política de contraseña muy laxa, una contraseña segura presenta una barrera más al atacante, que en su intento de acceder al sistema, indudablemete dejará más rastro y en algunos casos a provocar el abandondo de ataque.

## Objetivos
* Entender el sistema de contraseñas del sistema operativo linux
* Entender el PAM (Plugable authentication Module)
* Instalar y configurar la libreria _pwquality_
* Comprobar y verificar que se ha llevado la práctica de forma correcta
* Documentar la práctica.

* * *

## Introducción

El sistema de contraseñas en Linux es seguro y funciona de la siguiente manera:

1. **Archivos clave**:

   - **/etc/passwd**: Contiene información básica del usuario, pero no las contraseñas.
   - **/etc/shadow**: Almacena contraseñas cifradas usando algoritmos de hash (SHA-512, por ejemplo).

3. **Hashing y salting**:

   - Las contraseñas se cifran con un hash (SHA-512, etc.) y se les añade una **sal** (valor aleatorio) para hacerlas únicas, incluso si dos usuarios tienen la misma contraseña.

4. **Autenticación PAM**:

   - Linux usa **PAM (Pluggable Authentication Modules)** para gestionar la autenticación y aplicar políticas de seguridad. PAM es un sistema modular en Linux que gestiona la autenticación de usuarios, permitiendo personalizar políticas de seguridad.

      PAM utiliza módulos que definen cómo se autentican los usuarios. Cada servicio (como SSH o login) tiene su propia configuración en /etc/pam.d/.

      Las categorías principales son los grupos que organizan los módulos según su función dentro del proceso de autenticación y manejo de cuentas:
   
        - auth: Verifica contraseñas.
        - account: Gestiona permisos de cuenta.
        - password: Reglas para cambiar contraseñas.
        - session: Controla inicio/cierre de sesión.

4. **Políticas de seguridad**:

   - Se pueden configurar reglas como la expiración de contraseñas, número de intentos fallidos y requisitos de complejidad.

## Desarrollo

1. Lo fundamental para empezar a trabajar es instalar lo siguiente.

```bash
    $ sudo apt install libpwquality-tools
```

2. Para empezar a asignar políticas debemos acceder al fichero **/etc/security/pwquality.conf**. En este encontraremos varias líneas comentadas, explicándonos las distintas configuraciones y como usarlas.

```bash
    $ sudo nano /etc/security/pwquality.conf
```

3. Para demostrar su correcto uso yo voy a usar la siguiente configuración, descomentando las líneas que me interesan y ajustandole a los parámetros que necesite.

 ```bash
    minlen = 12
    minclass = 3
    maxrepeat = 2
    difok = 3
```
Con esto habremos configurado que la contraseña sea de mínimo 12 caracteres, que requiere de al menos 3 clases de caracteres diferentes, un carácter solo se puede usar 2 veces de forma consecutiva y al cambiar la contraseña debe haber 3 caracteres diferentes a la anterior.

## Comprobación

Su comprobación es fácil, simplemente usando el comando pwscore podremos probar diferentes contraseñas

```bash
    $ pwscore
        h0lAh0lAh0lA
        Falló la comprobación de calidad de la contraseña:
        La contraseña no supera la verificación de diccionario - No contiene suficientes caracteres DIFERENTES
    $ pwscore
        h0lAqU3T4l
        Falló la comprobación de calidad de la contraseña:
        La contraseña tiene menos de 12 caracteres.
    $ pwscore
        h0lAqU3T4l3st4m0s
        63
    $ pwscore
        38jd73n10dj38df11SDF12
        100
```
