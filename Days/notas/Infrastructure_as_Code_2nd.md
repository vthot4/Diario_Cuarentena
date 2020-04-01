# BOOK: Infrastructure as Code, 2nd Edition

by Kief Morris
Publisher: O'Reilly Media, Inc.
Release Date: December 2020
ISBN: 9781098114671



## Chapter 1. What is Infrastructure as Code?

- DevOps es un movimiento para reducir las barreras y la fricción entre los silos organizacionales: desarrollo, operaciones y otras partes interesadas involucradas en la planificación, construcción y ejecución de software. Aunque la tecnología es la cara más visible y, en cierto modo, más simple de DevOps, es la cultura, las personas y los procesos los que tienen el mayor impacto en el flujo y la efectividad. La tecnología y las prácticas de ingeniería, como la infraestructura como código, deben usarse para apoyar los esfuerzos para cerrar las brechas y mejorar la colaboración.
- Infraestructura como código es un enfoque para la automatización de infraestructura basado en prácticas del desarrollo de software. Enfatiza rutinas consistentes y repetibles para aprovisionar y cambiar sistemas y su configuración. Realiza cambios en el código, luego usa la automatización para probar y aplicar esos cambios a sus sistemas.
- Infraestructura como código aplica prácticas de ingeniería de software a la administración de infraestructura para una mejor confiabilidad, seguridad y calidad.
- Beneficios de la infraestructura como código:
  - Usando la infraestructura de TI como un facilitador para la entrega rápida de valor.
  - Reducir el esfuerzo y el riesgo de realizar cambios en la infraestructura.
  - Permitir a los usuarios de infraestructura obtener los recursos que necesitan, cuando lo necesitan.
  - Proporcionando herramientas comunes a través del desarrollo, las operaciones y otras partes interesadas.
  - Crear sistemas que sean confiables, seguros y rentables.
  - Hacer visibles los controles de gobierno, seguridad y cumplimiento.
  - Mejora de la velocidad para solucionar problemas y resolver incidencias.
- Razones por las que automatizar después es una mala idea:
  - La automatización debería permitir una entrega más rápida, incluso para cosas nuevas. La implementación de la automatización después de que se haya realizado la mayor parte del trabajo sacrifica muchos de los beneficios.
  - La automatización facilita la escritura de pruebas automatizadas para lo que construye. Y facilita la reparación y reconstrucción rápidas cuando encuentra problemas. Hacer esto como parte del proceso de construcción lo ayuda a construir una mejor infraestructura.
  - Automatizar un sistema existente es muy difícil. La automatización es parte del diseño y la implementación de un sistema. Para agregar automatización a un sistema construido sin él, debe cambiar significativamente el diseño y la implementación de ese sistema. Esto también es cierto para las pruebas y la implementación automatizadas.
- La velocidad permite calidad, y la calidad permite velocidad.
- Existen tres prácticas principales para implementar Infraestructura como código:
  - Define todo como código.
  - Validar continuamente todo el trabajo en progreso.
  - contruye piezas pequeñas y simples que puedes cambiar de forma independiente.

- Prácticas cores:

  - **Reusabilidad**. Si define una cosa como código, puede crear muchas instancias de la misma. Puede reparar y reconstruir sus cosas rápidamente. Otras personas pueden construir instancias idénticas de la cosa.

  - **Consistencia.** Si define una cosa como código, puede crear muchas instancias de la misma. Puede reparar y reconstruir sus cosas rápidamente. Otras personas pueden construir instancias idénticas de la cosa.

  - **Transparencia.** Todos pueden ver cómo se construye la cosa mirando el código. La gente puede revisar el código y sugerir mejoras. Pueden aprender cosas para usar en otro código. Obtienen información para usar al solucionar problemas. Pueden revisar y auditar el cumplimiento.

    

- Las partes de un sistema de infraestructura: Plataforma de infraestructura, Plataforma de tiempo de ejecución  de aplicación, Aplicaciones.



## Chapter 2. Principles of Cloud Age Infrastructure