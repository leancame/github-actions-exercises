# Contexts - Ejercicios


# Ejercicio 1
## Configura un workflow que imprima información sobre el runner en el que se está ejecutando el job, como el nombre del sistema operativo, la arquitectura, y el espacio en disco disponible

```yml
name: Contexts - Ejercicio 1  # Nombre del flujo de trabajo para identificar su propósito

on:  # Define el evento que activará este flujo de trabajo
  workflow_dispatch:  # Permite ejecutar el flujo de trabajo manualmente desde la interfaz de GitHub

jobs:  # Define los trabajos que se ejecutarán en este flujo
  Informacion:  # Nombre del trabajo
    runs-on: [self-hosted, labs-runner]  # Especifica el tipo de runner donde se ejecutará el trabajo
    steps:  # Lista los pasos que se ejecutarán en este trabajo
      - name: Imprimir información del runner  # Nombre del paso
        run: |  # Comando multi-línea para ejecutar
          echo "Nombre del runner: ${{ runner.name }}"  # Imprime el nombre del runner en los logs
          echo "Sistema operativo: ${{ runner.os }}"  # Imprime el sistema operativo del runner en los logs
          echo "Arquitectura: ${{ runner.arch }}"  # Imprime la arquitectura del runner (x64, ARM, etc.) en los logs
          df -h  # Muestra el uso de disco en un formato legible para humanos
```

En este ejercicio, se utiliza el contexto runner para obtener información sobre el runner en el que se ejecuta el job. La información obtenida se imprime en la salida de los logs del job. Para ello, usamos comandos como `runner.name`, `runner.os`, `runner.arch`, y `df -h`. El comando `df -h` muestra el uso de disco en un formato legible para humanos.


### Ejecución

![Captura sobre el código](../../datos/contexts/ejercicio%201/contexts%201%20parte%201.png)

![Captura sobre el código](../../datos/contexts/ejercicio%201/contexts%201%20parte%202.png)



# Ejercicio 2
## Configura un workflow que utilice un secreto llamado PROD_API_KEY para realizar un despliegue ficticio si se ejecuta en la rama main, y otro secreto llamado STAGING_API_KEY para cualquier otra rama

**El workflow debería:**

- Utilizar el contexto secrets para inyectar la API Key correspondiente en un comando de despliegue ficticio.
- Imprimir un mensaje indicando cuál API Key se utilizó.

```yml
name: Contexts - Ejercicio 2  # Nombre del flujo de trabajo para identificar su propósito

on:  # Define el evento que activará este flujo de trabajo
  workflow_dispatch:  # Permite ejecutar el flujo de trabajo manualmente desde la interfaz de GitHub

jobs:  # Define los trabajos que se ejecutarán en este flujo
  Despliegue:  # Nombre del trabajo
    runs-on: [self-hosted, labs-runner]  # Especifica el tipo de runner donde se ejecutará el trabajo

    steps:  # Lista los pasos que se ejecutarán en este trabajo
      - name: Imprimir API Key  # Nombre del paso
        run: |  # Comando multi-línea para ejecutar
          if [ "${{ github.ref }}" == "refs/heads/main" ]; then  # Verifica si la rama actual es 'main'
            echo "Despliegue en producción con la API Key: ${PROD_API_KEY}"  # Mensaje para despliegue en producción con clave secreta de producción
          else  # Si no es la rama 'main'
            echo "Despliegue en staging con la API Key: ${STAGING_API_KEY}"  # Mensaje para despliegue en staging con clave secreta de staging
          fi

    env:  # Define las variables de entorno que se usarán en este trabajo
      PROD_API_KEY: ${{ secrets.PROD_API_KEY }}  # Asigna el valor de la clave secreta de producción desde los secretos del repositorio
      STAGING_API_KEY: ${{ secrets.STAGING_API_KEY }}  # Asigna el valor de la clave secreta de staging desde los secretos del repositorio
```

Continuamos con el ejercicio, donde utilizamos el contexto secrets para inyectar la API Key correspondiente en un comando de despliegue ficticio y imprimimos un mensaje indicando cuál API Key se utilizó. El condicional `if` junto con la variable de entorno `github.ref` se utiliza para verificar si la rama actual es 'main' y utiliza la clave secreta de producción, si no lo es, utiliza la clave secreta de staging.


### Ejecución

![Captura sobre el código](../../datos/contexts/ejercicio%202/contexts%202%20parte%201.png)

![Captura sobre el código](../../datos/contexts/ejercicio%202/contexts%202%20parte%202.png)


# Ejercicio 3
## Configura un workflow que realice una tarea ficticia y luego envíe un mensaje de notificación a una URL (simulada con echo) usando el estado del job y el nombre del evento que lo desencadenó

```yml
name: Contexts - Ejercicio 3  # Nombre del flujo de trabajo para identificar su propósito

on:  # Define el evento que activará este flujo de trabajo
  workflow_dispatch:  # Permite ejecutar el flujo de trabajo manualmente desde la interfaz de GitHub

jobs:  # Define los trabajos que se ejecutarán en este flujo
  ficticia:  # Nombre del trabajo
    runs-on: [self-hosted, labs-runner]  # Especifica el tipo de runner donde se ejecutará el trabajo
    steps:  # Lista los pasos que se ejecutarán en este trabajo
      - name: Ejecutar tarea ficticia  # Nombre del paso
        run: echo "Ejecutando tarea ficticia..."  # Comando que simula la ejecución de una tarea

      - name: Enviar notificación  # Nombre del paso
        run: |  # Comando multi-línea para ejecutar
          echo "Enviando notificación a la URL..."  # Mensaje indicando que se enviará una notificación
          echo "Estado del job: ${{ job.status }}"  # Imprime el estado del trabajo (por ejemplo: success, failure)
          echo "Nombre del evento: ${{ github.event_name }}"  # Imprime el nombre del evento que activó el flujo de trabajo (en este caso, 'workflow_dispatch')
          echo "Notificación enviada."  # Mensaje final indicando que la notificación se envió
```

Seguimos con el ejercicio 3, en este se nos pide simular una notificación en una URL. Siguiendo esta estela, usamos comandos como `job.status`o `github.event_name` para mostrar el estado del job y el nombre del evento que lo desencadenó.

### Ejecución

![Captura sobre el código](../../datos/contexts/ejercicio%203/contexts%203%20parte%202.png)

![Captura sobre el código](../../datos/contexts/ejercicio%203/contexts%203%20parte%203.png)


# Ejercicio 4
## Configura un workflow que se ejecute cuando se abra una pull_request. El workflow debería verificar si el título del pull request contiene una palabra clave específica (urgent). Si la palabra se encuentra en el título, imprime el título y cuerpo de la PR, si la palabra no se encuentra en el título, haz que la ejecución falle.

```yml
name: Contexts - Ejercicio 4  # Nombre del workflow

on:  # Define el evento que activará este workflow
  pull_request:  # Se ejecuta cuando ocurre un evento relacionado con pull requests
    types: [opened]  # Específicamente, cuando se abre un nuevo pull request

jobs:  # Define los trabajos que se ejecutarán en este workflow
  # Comprobar si el pull request contiene la palabra "urgent"
  urgent:  # Nombre del trabajo
    runs-on: labs-runner  # Especifica el tipo de runner donde se ejecutará el trabajo
    steps:  # Lista los pasos que se ejecutarán en este trabajo
      - name: Comprobar si el PR contiene la palabra "urgent"  # Nombre del paso
        run: |  # Comando multi-línea para ejecutar
          if [[ "${{ github.event.pull_request.title }}" == *"urgent"* ]]; then  # Comprueba si el título del PR contiene la palabra "urgent"
            echo "Titulo: ${{ github.event.pull_request.title }}"  # Imprime el título del pull request
            echo "Cuerpo: ${{ github.event.pull_request.body }}"  # Imprime el cuerpo del pull request
          else  # Si no contiene la palabra "urgent"
            exit 1  # Finaliza el job con error
          fi
```

Por último, mostraremos el título y el cuerpo del pull request si contiene la palabra "urgent". En caso contrario, la ejecución fallará. Esto se logra con comandos como `github.event.pull_request.title` y `github.event.pull_request.body` cuando la condición se cumple dentro del bloque `if` comparando en el título del pull request con la palabra "urgent" donde usamos la expresión regular `*urgent*`.

### Ejecución

![Captura sobre el código](../../datos/contexts/ejercicio%204/context%204%20parte%201.png)

![Captura sobre el código](../../datos/contexts/ejercicio%204/context%204%20parte%202.png)

![Captura sobre el código](../../datos/contexts/ejercicio%204/context%204%20parte%203.png)

![Captura sobre el código](../../datos/contexts/ejercicio%204/context%204%20parte%204ç.png)