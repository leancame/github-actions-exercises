# Workflows reusables - Ejercicios

# Ejercicio 1
## Configura un workflow reutilizable que simplemente imprima un mensaje
## Luego, crea otro workflow que use ese workflow reutilizable


Workflow reusable:

```yml
name: Imprimir mensaje reusable  # Nombre del workflow reusable.

on:
  workflow_call:  # Define que este workflow es reusable y puede ser llamado por otros workflows.
    inputs:  # Sección para definir los parámetros (inputs) que acepta el workflow.
      message:  # Nombre del input que se usará dentro del workflow.
        description: 'Mensaje a imprimir'  # Descripción del input, útil para quienes lo usen.
        required: true  # Especifica que este input es obligatorio.
        type: string  # Indica que el input debe ser de tipo cadena (string).

jobs:
  imprimir-mensaje:  # Nombre del job dentro del workflow.
    runs-on: [self-hosted, labs-runner]  # Define el tipo de runner donde se ejecutará el job.

    steps:  # Lista de pasos que se ejecutarán dentro del job.
      - name: Imprimir mensaje  # Nombre descriptivo del paso.
        run: echo "${{ inputs.message }}"  # Comando para imprimir el valor del input 'message' en los logs.
```

Workflow que invoca al reusable:

```yml
name: Usar workflow reutilizable en otro workflow  # Nombre descriptivo del workflow principal.

on:
  push:  # Define que este workflow se ejecutará cuando haya un push.
    branches:  # Especifica las ramas en las que se activará.
      - ejercicios  # Rama en la que se disparará el workflow.

jobs:
  usar-workflow-reutilizable:  # Nombre del job dentro del workflow.
    uses: ./.github/workflows/reusable_1_1.yaml  # Especifica la ruta al workflow reutilizable.
    with:  # Sección para pasar parámetros al workflow reutilizable
      message: 'Hola, mi nombre es Leandro'  # Valor del parámetro 'message' definido en el workflow reutilizable.
```

El workflow reusable posee un parámetro denominado 'message' que se pasa al invocarlo en el workflow principal. En este caso el que invoca al reusable le pasa el msaje 'Hola, mi nombre es Leandro' y el reusable imprime ese mensaje en la consola. 


### Ejecución

![Captura sobre el código](../../datos/reusable/ejercicio%201/reusable%201%20parte%201.png)

![Captura sobre el código](../../datos/reusable/ejercicio%201/reusable%201%20parte%202.png)


# Ejercicio 2
## Configura un workflow reutilizable que primero valide si el nombre de la rama con la que se ejecuta empieza con "feature/". Solo si la validación es exitosa, se ejecutará la tarea principal

Workflow reusable:

```yml
name: Validar rama feature  # Nombre del workflow

on:  # Evento que activa el workflow
  workflow_call:  # Permite que este workflow sea reutilizable desde otros workflows

jobs:  # Definición de trabajos dentro del workflow
  validar:  # Nombre del primer trabajo
    runs-on: ubuntu-latest  # Se ejecuta en un runner con sistema operativo Ubuntu
    outputs:  # Salidas del trabajo
      es_feature: ${{ steps.rama-valida.outputs.resultado }}  # Define un output llamado 'es_feature' basado en el resultado del paso

    steps:  # Pasos a ejecutar en este trabajo

      - name: Extraer nombre de la rama  # Nombre descriptivo del paso
        id: rama-valida  # Identificador único del paso
        run: |  # Comandos a ejecutar
          BRANCH_NAME="${GITHUB_REF#refs/heads/}"  # Extrae el nombre de la rama eliminando el prefijo 'refs/heads/'
          echo "Rama actual: $BRANCH_NAME"  # Imprime el nombre de la rama

          if [[ "$BRANCH_NAME" == feature* ]]; then  # Verifica si la rama empieza con "feature"
            echo "Rama válida"  # Mensaje si la validación es exitosa
            echo "resultado=true" >> $GITHUB_OUTPUT  # Establece el resultado como 'true' en la salida
          else
            echo "Rama inválida. No empieza por feature/"  # Mensaje si la validación falla
            echo "resultado=false" >> $GITHUB_OUTPUT  # Establece el resultado como 'false' en la salida
          fi

  tarea-principal:  # Segundo trabajo, que depende del resultado del trabajo 'validar'
    runs-on: ubuntu-latest  # Se ejecuta en un runner con sistema operativo Ubuntu
    needs: validar  # Este trabajo depende del trabajo 'validar'
    if: needs.validar.outputs.es_feature == 'true'  # Solo se ejecuta si 'es_feature' es 'true'

    steps:  # Pasos a ejecutar en este trabajo
      - name: Ejecutar tarea principal  # Nombre descriptivo del paso
        run: echo "Ejecutando tarea principal en rama feature/*"  # Comando para ejecutar la tarea principal
```
Workflow principal:

```yml
name: Reusable workflow Ejercicio 2.2  # Nombre del workflow

on:  # Evento que activa el workflow
  workflow_dispatch:  # Permite que el workflow sea ejecutado manualmente desde la interfaz de GitHub

jobs:  # Definición de trabajos dentro del workflow
  tarea_principal:  # Nombre del trabajo
    uses: ./.github/workflows/reusable_2_1.yaml  # Usa un workflow reutilizable ubicado en la ruta especificada
```
En este supuesto la verdad es que he estado trabajando con varias pruebas y maneras ya que adquiría el nombre de la rama, pero no la comparaba correctamente con la que se nos solicitaba siendo la misma. Por lo que he investigado y también por parte de algunos compañeros a los que le ha pasado algo similar el problema radica en la propia rama de Github existiendo un error en algún momento de su desarrollo. He comparado varios códigos para ejecutar este reusable que han funcionado en otros proyectos y aún así no me ha funcionado en mi caso. No obstante, la solución a la que he optado es añadir la rama y un * para poder encontrarla sin problemas. Si la encuentra guardo en una variable verdadera y si no la encuentra guardo en una falsa. En caso de ser verdadera ejecuto la tarea principal.

### Ejecución

![Captura sobre el código](../../datos/reusable/ejercicio%202/ejercicio%202%20.png)


# Ejercicio 3

## En este ejercicio, configurarás un workflow reutilizable (deploy-environment.yml) que realizará tareas de despliegue basadas en un entorno específico.

## Este workflow reutilizable será llamado por otro workflow (trigger-deploy.yml) que se ejecutará automáticamente en función de la rama en la que se realice el push o mediante ejecución manual.

Cada entorno (development, staging, production) tendrá configuradas sus propias variables de entorno y secretos en GitHub, los cuales serán utilizados automáticamente por el workflow correspondiente.

Para este ejercicio se utilizarán dos repositorios, uno público, y el repositorio privado en el que se ha estado trabajando.

## Paso a paso:

> 1. Configuración de Environments en GitHub:
>
> - En el repositorio público, ve a Settings > Environments.
> - Crea tres environments: development, staging, y production.
> - Configura variables de entorno específicas para cada environment, como:
>   - API_URL
>   - LOG_LEVEL
> Estas variables de entorno tendrán valores diferentes dependiendo del environment seleccionado.

[Administración de entornos en GitHub](https://docs.github.com/es/actions/managing-workflow-runs-and-deployments/managing-deployments/managing-environments-for-deployment)

> 2. Definir el Workflow Reutilizable:
>
> Crea un archivo llamado deploy-environment.yml en el directorio .github/workflows/.
> Este workflow reutilizable recibirá un input llamado environment, el cual determinará el environment de despliegue.

> 3. Definir el Workflow que Invoca al Reusable:
>
> Crea un archivo llamado trigger-deploy.yml en el directorio .github/workflows/.
> Este workflow se activará en función de las ramas (main, develop, release/*) y llamará al workflow reutilizable para desplegar en el entorno correspondiente.


Workflow reutilizable:

```yml
name: Deploy Environment  # Nombre del workflow

on:  # Evento que activa el workflow
  workflow_call:  # Este workflow puede ser llamado desde otro workflow
    inputs:  # Definición de entradas requeridas
      environment:  # Nombre de la entrada
        required: true  # Es obligatorio proporcionar este valor
        type: string  # El tipo de entrada es una cadena de texto

jobs:  # Definición de trabajos dentro del workflow
  deploy:  # Nombre del trabajo
    runs-on: ubuntu-latest  # El trabajo se ejecutará en un runner con sistema operativo Ubuntu
    environment: ${{ inputs.environment }}  # Configura el entorno basado en la entrada proporcionada

    steps:  # Pasos que se ejecutan dentro del trabajo

      - name: Imprimir las variables de entorno  # Nombre descriptivo del paso
        env:  # Definición de variables de entorno
          API_URL: ${{ vars.API_URL }}  # Variable de entorno que contiene la URL de la API
          LOG_LEVEL: ${{ vars.LOG_LEVEL }}  # Variable de entorno que define el nivel de logs
        run: |  # Comandos que se ejecutan en este paso
          echo "Deploying to ${{ inputs.environment }} environment"  # Imprime el entorno de despliegue
          echo "API_URL: $API_URL"  # Muestra el valor de la variable API_URL
          echo "LOG_LEVEL: $LOG_LEVEL"  # Muestra el valor de la variable LOG_LEVEL
```

Workflow que invoca al reutilizable:

```yml
name: Trigger Deploy  # Nombre descriptivo del workflow.

on:
  push:  # Define que este workflow se ejecutará al hacer un push.
    branches:  # Especifica las ramas en las que se activará.
      - main  # Rama principal para producción.
      - develop  # Rama de desarrollo.
      - ejercicios  # Rama adicional para pruebas o staging.

jobs:
  trigger-deploy:  # Nombre del job dentro del workflow.
    uses: ./.github/workflows/deploy-environment.yml  # Especifica la ruta al workflow reutilizable para despliegues.
    with:  # Sección para pasar parámetros al workflow reutilizable.
      environment: ${{ github.ref_name == 'main' && 'production' || (github.ref_name == 'develop' && 'development' || 'staging') }}  # Determina el entorno de despliegue basado en la rama:
        # Si la rama es 'main', usa 'production'.
        # Si la rama es 'develop', usa 'development'.
        # Para otras ramas, usa 'staging'.
```

El workflow principal llama al workflow reutilizable para desplegar en el entorno correspondiente. Dependiendo de la rama, el entorno de despliegue será 'production', 'development', o 'staging'. En el caso de 'main', se usara 'production'. En el caso de 'develop', se usara 'development'. En el caso de otras ramas, se usara 'staging'. El workflow reutilizable imprime las variables de entorno y el entorno de despliegue. 


### Ejecución

Definición de los entornos:

![Captura sobre el código](../../datos/reusable/ejercicio%203/reusable%203%20parte%201.png)

![Captura sobre el código](../../datos/reusable/ejercicio%203/reusable%203%20parte%202.png)

Staging otras ramas:

![Captura sobre el código](../../datos/reusable/ejercicio%203/reusable%203%20parte%203.png)

Production rama main

![Captura sobre el código](../../datos/reusable/ejercicio%203/reusable%203%20parte%204.png)

Development rama develop:

![Captura sobre el código](../../datos/reusable/ejercicio%203/reusable%203%20parte%205.png)

