# Workflow Dispatch

---

## Documentación

El **workflow_dispatch** es un tipo de trigger en GitHub Actions que permite **ejecutar un workflow de forma manual**. A diferencia de otros triggers que se activan automáticamente (como un push o un pull request), workflow_dispatch permite a los usuarios iniciar un workflow desde la interfaz de GitHub cuando lo deseen.

Además, es posible configurar entradas (inputs) para que el usuario proporcione parámetros personalizados al ejecutar el workflow, lo que permite mayor flexibilidad y control en la ejecución manual de tareas específicas.

[Documentación sobre workflows que se disparan manualmente](https://docs.github.com/es/actions/writing-workflows/choosing-when-your-workflow-runs/triggering-a-workflow#defining-inputs-for-manually-triggered-workflows)

[Uso de condiciones para evaluar la ejecución de jobs](https://docs.github.com/es/actions/writing-workflows/choosing-when-your-workflow-runs/using-conditions-to-control-job-execution)

---

## Ejemplos

```yaml
on:
  workflow_dispatch:
    inputs:
      logLevel:
        description: 'Log level'
        required: true
        default: 'warning'
        type: choice
        options:
          - info
          - warning
          - debug
      print_tags:
        description: 'True to print to STDOUT'
        required: true
        type: boolean
      tags:
        description: 'Test scenario tags'
        required: true
        type: string
      environment:
        description: 'Environment to run tests against'
        type: environment
        required: true

jobs:
  print-tag:
    runs-on: ubuntu-latest
    if:  ${{ inputs.print_tags }} 
    steps:
      - name: Print the input tag to STDOUT
        run: echo  The tags are ${{ inputs.tags }}
```

---

## Ejercicios

- [Ejercicio 1](ejercicio1.md)
- [Ejercicio 2](ejercicio2.md)
- [Ejercicio 3](ejercicio3.md)
