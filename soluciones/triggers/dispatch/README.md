# Triggers (Dispatch) - Ejercicios


# Ejercicio 1
### 1. Configura un workflow para que se ejecute manualmente usando el evento workflow_dispatch.
### 2. Define un input llamado _"message"_ que permita al usuario ingresar un mensaje personalizado.
### 3. El workflow debe ser capaz de imprimir ese mensaje.

```yml
name: Triggers (Dispatch) - Ejercicio 1  # Nombre del flujo de trabajo para identificación

on:  # Define el evento que activará este flujo de trabajo
  workflow_dispatch:  # Especifica que se activa manualmente mediante un despacho de flujo
    inputs:  # Define los parámetros que puede aceptar el flujo al despacharse
      message:  # Nombre del parámetro
        description: 'Message'  # Descripción del parámetro para los usuarios
        required: true  # Indica que este parámetro es obligatorio
        type: string  # Especifica que el tipo del parámetro es una cadena de texto
        default: 'I love pdf'  # Valor predeterminado si el usuario no proporciona uno

jobs:  # Define los trabajos que se ejecutarán en este flujo
  print-message:  # Nombre del trabajo
    runs-on: [self-hosted, labs-runner]  # Indica el tipo de runner donde se ejecutará el trabajo
    steps:  # Lista los pasos que se ejecutarán en el trabajo
      - name: Print message  # Nombre del paso para identificación
        run: echo "${{ github.event.inputs.message }}"  # Comando para imprimir el mensaje proporcionado por el usuario
```

En el primer ejercicio se nos solicita añadir un mensaje a un workflow manualmente. En mi caso, he solicitado que el mensaje sea "I love pdf", aunque este sea por defecto puede ser modificado al gusto del consumidor. Este mensaje se imprime en la consola con el trabajo "print-message". Para la creación del mensaje he usado `imputs` y para mostralo en pantalla he usado `echo ${{ github.event.inputs.message }}` siendo ${{ github.event.inputs.message }} la variable que contiene el mensaje.


### Ejecución

![Captura sobre el código](../../../datos/triggers/dispatch%201/triggers%20dispatch%201%20parte%201.png)

![Captura sobre el código](../../../datos/triggers/dispatch%201/triggers%20dispatch%201%20parte%203.png)


# Ejercicio 2
### 1. Configura un workflow para que se ejecute manualmente usando el evento workflow_dispatch.
### 2. Define un input llamado "_environment_" que permita seleccionar entre "_production_" y "_staging_".
### 3. Imprime el entorno seleccionado en la consola.

```yml
name: Triggers (Dispatch) - Ejercicio 2

on:  # Define el evento que activará este flujo de trabajo
  workflow_dispatch:  # Especifica que se activa manualmente mediante un despacho de flujo
    inputs:  # Define los parámetros que puede aceptar el flujo al despacharse
      environment:  # Nombre del parámetro
        description: 'Environment'  # Descripción del parámetro para los usuarios
        required: true  # Indica que este parámetro es obligatorio
        type: choice  # Especifica que el parámetro es una lista de opciones
        options:  # Lista de valores permitidos para este parámetro
          - production  # Opción 1: producción
          - staging  # Opción 2: entorno de pruebas

jobs:  # Define los trabajos que se ejecutarán en este flujo
  print-environment:  # Nombre del trabajo
    runs-on: [self-hosted, labs-runner]  # Indica el tipo de runner donde se ejecutará el trabajo
    steps:  # Lista los pasos que se ejecutarán en el trabajo
      - name: Print environment  # Nombre del paso para identificación
        run: echo "${{ github.event.inputs.environment }}"  # Comando para imprimir el entorno seleccionado por el usuario
```

Siguiendo los triggers manuales he creado un input para seleccionar un entorno. En este caso, he elegido entre "production" y "staging" usando un menu desplegable con dos opciones a través de `type: choice`. Para la impresión he usado `echo ${{ github.event.inputs.environment }}` siendo ${{ github.event.inputs.environment }} la variable que contiene el entorno seleccionada por el usuario.


### Ejecución

![Captura sobre el código](../../../datos/triggers/dispatch%202/trigger%20dispatch%202%20parte%201.png)

![Captura sobre el código](../../../datos/triggers/dispatch%202/trigger%20dispatch%202%20parte%203.png)

# Ejercicio 3
### 1. Configura un workflow para que se ejecute manualmente usando el evento workflow_dispatch.
### 2. Define un input llamado _"confirm"_ que requiera una _confirmación_ ("true" o "false").
### 3. Imprime un mensaje, dependiendo de si la confirmación es positiva o negativa.


```yml
name: Triggers (Dispatch) - Ejercicio 3  # Nombre del flujo de trabajo para identificación

on:  # Define el evento que activará este flujo de trabajo
  workflow_dispatch:  # Especifica que se activa manualmente mediante un despacho de flujo
    inputs:  # Define los parámetros que puede aceptar el flujo al despacharse
      confirm:  # Nombre del parámetro
        description: '¿Confirmas la operación? (true o false)'  # Descripción del parámetro para los usuarios
        required: true  # Indica que este parámetro es obligatorio
        default: 'false'  # Valor predeterminado si el usuario no proporciona uno

jobs:  # Define los trabajos que se ejecutarán en este flujo
  job1:  # Nombre del trabajo
    runs-on: [self-hosted, labs-runner]  # Indica el tipo de runner donde se ejecutará el trabajo
    steps:  # Lista los pasos que se ejecutarán en el trabajo
      - name: Print message  # Nombre del paso para identificación
        run: |  # Comando multi-línea para ejecutar
          if [ "${{ inputs.confirm }}" == "true" ]; then  # Comprueba si el parámetro 'confirm' es 'true'
            echo "Confirmed"  # Imprime 'Confirmed' si el usuario confirmó la operación
          else  # Si el parámetro no es 'true'
            echo "Not confirmed"  # Imprime 'Not confirmed' si el usuario no confirmó la operación
          fi  # Fin de la estructura condicional
```

El último ejercicio de este apartado se nos solicita elegir entre true o false y dependiendo del que elejamos se imprime un mensaje. Para ello, he usado un `if` para comprobar si el parámetro `confirm` es igual a "true". En caso afirmativo, se imprime "Confirmed", en caso contrario se imprime "Not confirmed".


### Ejecución

![Captura sobre el código](../../../datos/triggers/dispatch%203/trigger%20dispatch%203%20parte%201.png)

![Captura sobre el código](../../../datos/triggers/dispatch%203/trigger%20dispatch%203%20parte%203.png)