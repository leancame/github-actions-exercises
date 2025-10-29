# Variables y outputs

---

## Documentación

Las **variables** se utilizan para almacenar y reutilizar valores en un workflow. Existen diferentes tipos de variables:

- **Variables de entorno (env):** Definidas globalmente o a nivel de job, son accesibles por todos los steps dentro de su ámbito.

- **Entradas (inputs):** Utilizadas principalmente con workflow_dispatch, permiten que el usuario proporcione valores al iniciar manualmente un workflow.

>[Documentación Variables](https://docs.github.com/es/actions/writing-workflows/choosing-what-your-workflow-does/store-information-in-variables)

Los **outputs** son valores generados por un step o job que pueden ser utilizados por steps o jobs posteriores. Esto facilita el paso de información y la creación de flujos de trabajo dinámicos.

- **Step Outputs:** Salidas de un step que pueden ser utilizadas dentro del mismo job.

- **Job Outputs:** Salidas de un job que pueden ser utilizadas en otros jobs dentro del mismo workflow.

>[Documentación Outputs](https://docs.github.com/es/actions/writing-workflows/choosing-what-your-workflow-does/passing-information-between-jobs)


>[Pasar información entre jobs](https://docs.github.com/es/actions/writing-workflows/choosing-what-your-workflow-does/passing-information-between-jobs)

---

## Ejemplos

### Variables

```yaml
name: Greeting on variable day

on:
  workflow_dispatch

env:
  DAY_OF_WEEK: Monday

jobs:
  greeting_job:
    runs-on: ubuntu-latest
    env:
      Greeting: Hello
    steps:
      - name: "Say Hello Mona it's Monday"
        run: echo "$Greeting $First_Name. Today is $DAY_OF_WEEK!"
        env:
          First_Name: Mona

```

### Output

```yaml
jobs:
  job1:
    runs-on: ubuntu-latest
    # Map a step output to a job output
    outputs:
      output1: ${{ steps.step1.outputs.test }}
      output2: ${{ steps.step2.outputs.test }}
    steps:
      - id: step1
        run: echo "test=hello" >> "$GITHUB_OUTPUT"
      - id: step2
        run: echo "test=world" >> "$GITHUB_OUTPUT"
  job2:
    runs-on: ubuntu-latest
    needs: job1
    steps:
      - env:
          OUTPUT1: ${{needs.job1.outputs.output1}}
          OUTPUT2: ${{needs.job1.outputs.output2}}
        run: echo "$OUTPUT1 $OUTPUT2"
```

---

## Ejercicios

- [Ejercicio 1](ejercicio1.md)
- [Ejercicio 2](ejercicio2.md)
- [Ejercicio 3](ejercicio3.md)
- [Ejercicio 4](ejercicio4.md)
- [Ejercicio 5](ejercicio5.md)
