# Jobs y Steps - Ejercicios


# Ejercicio 1

### Configura un workflow (workflow_dispatch) con un solo job que haga las siguientes tareas:

- Mostrar la fecha y hora actual.
- Crear un archivo de texto.
- Listar los archivos en el directorio actual.
- Hacer un commit y push de cualquier fichero en el repositorio.

```yml
name: Jobs y Steps - Ejercicio 1  # Nombre del flujo de trabajo para identificación

on:  # Define el evento que activará este flujo de trabajo
  workflow_dispatch:  # Especifica que se activa manualmente mediante un despacho de flujo

permissions:  # Define los permisos requeridos para este flujo
  contents: write  # Permiso para escribir en el repositorio (opcional si ya está configurado en la UI)

jobs:  # Define los trabajos que se ejecutarán en este flujo
  main:  # Nombre del trabajo principal
    runs-on: [self-hosted, labs-runner]  # Indica el tipo de runner donde se ejecutará el trabajo

    steps:  # Lista los pasos que se ejecutarán en el trabajo
      # 1. Checkout del repositorio
      - name: Checkout code  # Nombre del paso
        uses: actions/checkout@v3  # Acción de GitHub para clonar el repositorio en el runner
        with:  # Configuración adicional para la acción
          token: ${{ secrets.GITHUB_TOKEN }}  # Token de autenticación para acceder al repositorio
          fetch-depth: 0  # Clona todo el historial del repositorio (sin limitaciones de profundidad)

      # 2. Mostrar fecha y hora
      - name: Print date and time  # Nombre del paso
        run: echo "Date and time $(date)"  # Comando para imprimir la fecha y hora actuales

      # 3. Crear un archivo de texto
      - name: Create file  # Nombre del paso
        run: echo "Hello, this is a new file." > file.txt  # Comando para crear un archivo `file.txt` con contenido

      # 4. Listar los archivos en el directorio actual
      - name: List files  # Nombre del paso
        run: ls -la  # Comando para listar todos los archivos y carpetas en el directorio actual con detalles

      # 5. Commit and push automático
      - name: Commit and push changes  # Nombre del paso
        uses: stefanzweifel/git-auto-commit-action@v5  # Acción para realizar commit y push automático
        with:  # Configuración adicional para la acción
          commit_message: "Add new file.txt"  # Mensaje personalizado para el commit (opcional, por defecto: "Apply automatic changes")
```

Este primer job nos propuso bastantes dificultades ya que nos permitía realizar un commit y push de cualquier archivo en el repositorio. Algunos de los compañeros lo han hecho con comandos de git a mano, mientras que en mi caso he decido hacerlo con la acción de GitHub `stefanzweifel/git-auto-commit-action`. Antes de este commit y push, hemos tenido que hacer un checkout del repositorio y luego hacer un commit y push de cualquier archivo en el repositorio. Además, hemos realizado las tareas solicitadas como mostrar la hora, crear un archivo y listar los archivos en el directorio actual. Al final de todo, por lo que hemos descubierto y tras mucho ensayo y error, la clave para poder hacer modificaciones de escritura al workflow ha sido el permiso `contents: write` lo que nos ha permitido tanto a aquellos que lo han hecho de forma manual como a los que hemos usado la acción personalizada el poder escribir en el repositorio.

### Ejecución

![Captura sobre el código](../../datos/jobs/ejercicio%201/job%201%20parte%201.png)

![Captura sobre el código](../../datos/jobs/ejercicio%201/job%201%20parte%202.png)

![Captura sobre el código](../../datos/jobs/ejercicio%201/job%201%20parte%203.png)

![Captura sobre el código](../../datos/jobs/ejercicio%201/job%201%20parte%204.png)

![Captura sobre el código](../../datos/jobs/ejercicio%201/job%201%20parte%205.png)
 



# Ejercicio 2

### 1. Configura dos jobs en un único workflow: "build" y "deploy"

- En el job deploy imprime un mensaje por la consola.
- El job deploy debe depender del éxito del job build.
- Si el job "build" falla, el job "deploy" **no debe ejecutarse.**

```yml
name: Jobs y Steps - Ejercicio 2  # Nombre del flujo de trabajo para identificación

on:  # Define el evento que activará este flujo de trabajo
  workflow_dispatch:  # Especifica que se activa manualmente mediante un despacho de flujo

jobs:  # Define los trabajos que se ejecutarán en este flujo
  build:  # Primer trabajo: "build"
    runs-on: [self-hosted, labs-runner]  # Indica el tipo de runner donde se ejecutará el trabajo
    steps:  # Lista los pasos que se ejecutarán en el trabajo
      - name: Build  # Nombre del paso
        run: echo "Building..."  # Comando para simular el proceso de construcción

  deploy:  # Segundo trabajo: "deploy"
    needs: build  # Indica que este trabajo depende del éxito del trabajo "build"
    runs-on: [self-hosted, labs-runner]  # Indica el tipo de runner donde se ejecutará el trabajo
    steps:  # Lista los pasos que se ejecutarán en el trabajo
      - name: Deploy  # Nombre del paso
        run: echo "Ha funcionado el build y se puede desplegar"  # Comando para simular el proceso de despliegue
```

Este ejercicio, en primer lugar es un disparador manual que activa el workflow, luego hay dos trabajos: "build" y "deploy". El job "deploy" depende del job "build", lo que significa que solo se ejecutará si el job "build" ha sido exitoso. Ambos trabajos mostrarán un mensaje por la consola para simular el proceso de construcción y despliegue.

### Ejecución 

![Captura sobre el código](../../datos/jobs/ejercicio%202/jobs%202%20parte%201.png)

![Captura sobre el código](../../datos/jobs/ejercicio%202/jobs%202%20parte%202.png)

![Captura sobre el código](../../datos/jobs/ejercicio%202/jobs%202%20parte%203.png)


# Ejercicio 3

### Configura un workflow que ejecute el mismo job en diferentes sistemas operativos

```yml
name: Jobs y Steps - Ejercicio 3  # Nombre del flujo de trabajo para identificación

on:  # Define el evento que activará este flujo de trabajo
  workflow_dispatch:  # Especifica que se activa manualmente mediante un despacho de flujo

jobs:  # Define los trabajos que se ejecutarán en este flujo
  test-matrix:  # Trabajo que utiliza una estrategia de matriz
    strategy:  # Define una estrategia de matriz para ejecutar en múltiples entornos
      matrix:  # Lista de combinaciones de variables a probar
        os: [labs-runner, windows-latest]  # Define dos sistemas operativos: Ubuntu (labs-runner) y Windows (windows-latest)

    runs-on: ${{ matrix.os }}  # Asigna el runner correspondiente según el sistema operativo actual de la matriz

    steps:  # Lista los pasos que se ejecutarán en el trabajo
      # Solo en Ubuntu (Bash)
      - name: Mensaje en Linux  # Nombre del paso para el entorno Linux
        if: matrix.os == 'labs-runner'  # Condición para ejecutar este paso solo en Ubuntu
        run: |  # Comando multi-línea para ejecutar
          echo "Ejecutando en Linux (Ubuntu)"  # Mensaje en la consola
          echo "Este es mi comando Linux personalizado"  # Comando Linux de ejemplo
      
      # Solo en Windows (PowerShell)
      - name: Mensaje en Windows  # Nombre del paso para el entorno Windows
        if: matrix.os == 'windows-latest'  # Condición para ejecutar este paso solo en Windows
        shell: pwsh  # Especifica que se utilizará PowerShell como shell
        run: |  # Comando multi-línea para ejecutar
          Write-Host "Ejecutando en Windows (PowerShell)"  # Mensaje en la consola
          Write-Host "Este es mi comando Windows personalizado"  # Comando Windows de ejemplo
```

Para este supuesto, se utiliza una estrategia de matriz que ejecuta el job en dos sistemas operativos: Ubuntu y Windows. En cada sistema operativo, se muestra un mensaje en Ubuntu y un mensaje en Windows. A diferencia de los demás casos usamos commandos como `strategy` y `matrix` para ejecutar el job en diferentes sistemas operativos.


### Ejecución 

![Captura sobre el código](../../datos/jobs/ejercicio%203/jobs%203%20parte%201.png)

![Captura sobre el código](../../datos/jobs/ejercicio%203/jobs%203%20parte%202.png)

![Captura sobre el código](../../datos/jobs/ejercicio%203/jobs%203%20parte%203.png)



# Ejercicio 4

### 1. Crea un job el cual cree un archivo .txt llamado "test"
### 2. Crea otro job que mueva el archivo creado anteriormente a otro directorio. ¿Qué es lo que ocurre?
### ¿Qué ocurre?


```yml
name: Jobs y Steps - Ejercicio 4  # Nombre del flujo de trabajo para identificación

on:  # Define el evento que activará este flujo de trabajo
  workflow_dispatch:  # Especifica que se activa manualmente mediante un despacho de flujo

jobs:  # Define los trabajos que se ejecutarán en este flujo
  creacion:  # Primer trabajo: "creacion"
    runs-on: [self-hosted, labs-runner]  # Indica el tipo de runner donde se ejecutará el trabajo
    steps:  # Lista los pasos que se ejecutarán en el trabajo
      - name: Crear archivo  # Nombre del paso
        run: touch test.txt  # Comando para crear un archivo vacío llamado `test.txt`

  mover:  # Segundo trabajo: "mover"
    runs-on: [self-hosted, labs-runner]  # Indica el tipo de runner donde se ejecutará el trabajo
    steps:  # Lista los pasos que se ejecutarán en el trabajo
      - name: Mover archivo  # Nombre del paso
        run: mv test.txt /home  # Comando para mover el archivo `test.txt` al directorio `/home`
```

Este workflow tendrá dos trabajos: "creacion" y "mover". El job "creacion" creará un archivo llamado "test.txt" en la raíz del directorio del runner. El job "mover" moverá el archivo "test.txt" al directorio "/home". A priori, no se debería poder mover el archivo debido a que cada trabajo en GitHub Actions se ejecuta en un entorno separado, lo que significa que el archivo creado en el primer trabajo (creacion) no estará disponible en el segundo trabajo (mover) y por tanto no podrá ser movido. 


### Ejecución 

![Captura sobre el código](../../datos/jobs/ejercicio%204/jobs%204%20parte%201.png)

![Captura sobre el código](../../datos/jobs/ejercicio%204/jobs%204%20parte%202.png)