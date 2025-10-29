# Jobs y steps

## Documentación

Los jobs en GitHub Actions son bloques dentro de un flujo de trabajo que agrupan un conjunto de pasos (steps) para ejecutar tareas específicas. Cada job se ejecuta en un entorno separado, como una máquina virtual, y puede contener múltiples pasos que se ejecutan en secuencia. Los jobs pueden ejecutarse de manera independiente o depender unos de otros, lo que permite crear flujos de trabajo complejos y organizados.

[Documentación acerca de los Jobs](https://docs.github.com/es/actions/writing-workflows/choosing-what-your-workflow-does/using-jobs-in-a-workflow)

[Ejecución de variaciones de un job en el mismo workflow](https://docs.github.com/es/actions/writing-workflows/choosing-what-your-workflow-does/running-variations-of-jobs-in-a-workflow)

---

## Ejemplos

### Jobs

```yaml
jobs:
  my_first_job:
    name: My first job
  my_second_job:
    name: My second job
```

### Dependencia entre jobs

```yaml
jobs:
  job1:
  job2:
    needs: job1
  job3:
    needs: [job1, job2]
```

---

## Ejercicios

- [Ejercicio 1](ejercicio1.md)
- [Ejercicio 2](ejercicio2.md)
- [Ejercicio 3](ejercicio3.md)
- [Ejercicio 4](ejercicio4.md)
