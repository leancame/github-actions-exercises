# Triggers - Ejercicios


# Ejercicio 1
## Configura un workflow para que se ejecute cuando se abre un Pull Request

```yml
name: Pull Request Workflow # Nombre del workflow, visible en la lista de acciones.

# Evento que activa el workflow
on:
  pull_request: # Se activa cuando ocurre un evento relacionado con una Pull Request.
    types: [opened] # El tipo de evento específico que lo activa: cuando se abre una Pull Request.

jobs: # Bloque que contiene los trabajos (jobs) del workflow.
  mensaje_bienvenida: # Nombre del job.
    runs-on: labs-runner # Especifica el runner donde se ejecutará el job. Aquí se usa un runner llamado 'labs-runner'.

    steps: # Lista de pasos que ejecutará el job.
      - name: Añadir un comentario al Triggers # Nombre del paso, descriptivo para identificarlo.
        run: echo "Pull Request se ha detectado." # Comando que se ejecutará en este paso. En este caso, imprime un mensaje en la consola.
```

En este código se activa el workflow cuando se abre una Pull Request. El job 'mensaje_bienvenida' simplemente imprime un mensaje en la consola. Añadimos un nombre con `name`, usamos `on` para especificar el evento que activa el workflow y `jobs` para definir los trabajos (jobs) del workflow. En el caso del evento que activa el workflow, usamos `pull_request` y `types: [opened]` para especificar que se activa cuando se abre una Pull Request. En el bloque `jobs`, creamos un job llamado `mensaje_bienvenida` que se ejecuta en un runner llamado `labs-runner`. En el `steps` bloque, creamos un paso que imprime un mensaje en la consola.

### Ejecución

![Captura sobre el código](../../datos/triggers/ejercicio%201/ejercicio%201%20parte%201.png)

![Captura sobre el código](../../datos/triggers/ejercicio%201/ejercicio%201%20parte%202.png)

![Captura sobre el código](../../datos/triggers/ejercicio%201/ejercicio%201%20parte%203.png)

En estas capturas, observamos la consecución de los hechos que hemos definido. En primer lugar, se activa el workflow cuando se abre una Pull Request. En segundo lugar, se ejecutan los pasos del job 'mensaje_bienvenida'. En tercer lugar, se imprime el mensaje en la consola. 

# Ejercicio 2
## Configura un workflow para que se ejecute cuando se haga un push en la rama develop y simplemente imprima "Hello, World!" en la consola

```yml
name: Triggers # Nombre del workflow, visible en la lista de acciones.

# Evento que activa el workflow
on: 
  push: # Se activa cuando ocurre un evento de "push".
    branches: 
      - develop # Específicamente, se activa solo si el push se realiza en la rama 'develop'.

jobs: # Bloque que contiene los trabajos (jobs) del workflow.
  build: # Nombre del job, en este caso 'build'.
    runs-on: [self-hosted, labs-runner] # Especifica el entorno o runner donde se ejecutará el trabajo. Usa un runner self-hosted llamado 'labs-runner'.

    steps: # Lista de pasos que ejecutará el job.
      - name: Imprimir "Hello, World!" # Nombre descriptivo del paso.
        run: echo "Hello, World!" # Comando que se ejecutará en este paso. En este caso, imprime "Hello, World!" en la consola.
```

El segundo ejercicio es similar al primero, pero ahora se activa cuando se hace un push en la rama 'develop'. El job 'build' simplemente imprime "Hello, World!" en la consola.

### Ejecución

![Captura sobre el código](../../datos/triggers/ejercicio%202/triggers%202%20parte%201.png)

![Captura sobre el código](../../datos/triggers/ejercicio%202/triggers%202%20parte%202.png)

![Captura sobre el código](../../datos/triggers/ejercicio%202/triggers%202%20parte%203.png)

Ocurrirá la ejecución cuando se haga un push en la rama 'develop'.


# Ejercicio 3
## Configura un workflow para que se ejecute cuando se cree una nueva Issue

```yml
name: triggers_3 Issue # Nombre del workflow, visible en la lista de acciones.

# Evento que activa el workflow
on:
  issues: # Se activa cuando ocurre un evento relacionado con Issues.
    types: [opened] # Específicamente, se activa cuando se abre un nuevo Issue.

jobs: # Bloque que contiene los trabajos (jobs) del workflow.
  build: # Nombre del job, en este caso 'build'.
    runs-on: [self-hosted, labs-runner] # Especifica el entorno o runner donde se ejecutará el trabajo. Usa un runner self-hosted llamado 'labs-runner'.

    steps: # Lista de pasos que ejecutará el job.
      - name: Imprimir error # Nombre descriptivo del paso.
        run: echo "Ha surgido un problema" # Comando que se ejecutará en este paso. En este caso, imprime un mensaje indicando que ha surgido un problema.
```

El tercer ejercicio es similar al primero, pero ahora se activa cuando se abre una nueva Issue. En este ya si hay cierta diferencia ya que usamos el `issues` dentrod del `on` añadiendo que solo afecte en su apertura con `types: [opened]`. En el job 'build' simplemente imprime un mensaje indicando que ha surgido un problema.

### Ejecución


![Captura sobre el código](../../datos/triggers/ejercicio%203/triggers%203%20parte%202.png)

![Captura sobre el código](../../datos/triggers/ejercicio%203/triggers%203%20parte%203.png)

![Captura sobre el código](../../datos/triggers/ejercicio%203/triggers%203%20parte%204.png)

La ejecución solo se dará cuando se inicialice un nuevo Issue en el Github

# Ejercicio 4
## Configura un workflow para que se ejecute todos los días a las 12:00 UTC y que imprima "Scheduled job executed!" en la consola

```yml
name: Hora programada # Nombre del workflow, visible en la lista de acciones.

# Evento que activa el workflow
on:
  schedule: # Se activa según una programación definida por cron.
    - cron: '0 12 * * *' # La expresión cron define que el workflow se ejecutará todos los días a las 12:00 PM (hora UTC).

jobs: # Bloque que contiene los trabajos (jobs) del workflow.
  build: # Nombre del job, en este caso 'build'.
    runs-on: [self-hosted, labs-runner] # Especifica el entorno o runner donde se ejecutará el trabajo. Usa un runner self-hosted llamado 'labs-runner'.

    steps: # Lista de pasos que ejecutará el job.
      - name: Imprimir mensaje # Nombre descriptivo del paso.
        run: echo "Scheduled job executed!" # Comando que se ejecutará en este paso. En este caso, imprime un mensaje indicando que el trabajo programado ha sido ejecutado.
```

El cuarto ejercicio se activa todos los días a las 12:00 UTC.  Para ello usamos, el comando `schedule` y la expresión `cron: '0 12 * * *'`. En el job 'build' simplemente imprime un mensaje indicando que el trabajo programado ha sido ejecutado cuando se ejecute el workflow a la hora programada.

### Ejecución

![Captura sobre el código](../../datos/triggers/ejercicio%204/triggers%204%20parte%203.png)


