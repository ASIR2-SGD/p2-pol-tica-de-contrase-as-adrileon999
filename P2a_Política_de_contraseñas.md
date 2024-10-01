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

2. Para empezar a asignar políticas debemos acceder al fichero **/etc/security/pwquality.conf**. En este encontraremos varias líneas comentadas, explicándonos las distintas configuraciones y como usarlas. Para aplicar una política descomentamos su linea y cambiamos los valores a nuestro gusto.

```bash
    $ sudo nano /etc/security/pwquality.conf
```

3. Una configuración de ejemplo sería la siguiente, que es la que voy a usar para hacer el ejemplo de contraseña de dificultad media en el apartado posterior.

 ```bash
    ucredit = -1
    dcredit = -1
    minlen = 6
```

## Comprobación

Su comprobación es fácil, simplemente usando el comando pwscore y ajustando los parámetros de configuración podremos probar diferentes contraseñas.

| Dificultad   | Score | Longitud Mínima | Requisitos                       | Ejemplos              |
|--------------|-------|-----------------|----------------------------------|-----------------------|
| Fácil        | 40    | Sin mínimo      | Sin requisitos                   | Hola1234              |
| Media        | 40-50 | 6               | `ucredit = -1`, `dcredit = -1`  | Pdj3wqd               |
| Difícil      | 50-80 | 8               | `ucredit = -1`, `ocredit = -1`  | Secr3tP@ss        |
| Muy Difícil  | > 80  | 10              | `ucredit = -1`, `dcredit = -1`  | Str0ngP@ssw0rd2024    |

Requisitos usados:

   - ucredit: Mayúsculas requeridas (-1: al menos 1).
   - ocredit: Caracteres especiales (-1: al menos 1).
   - dcredit: Dígitos requeridos (-1: al menos 1).

