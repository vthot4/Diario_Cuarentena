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

- Una forma de hacer que un sistema sea recuperable es asegurarse de que pueda reconstruir sus partes sin esfuerzo y de manera confiable.
- La reproducibilidad no sólo facilita la recuperación de un sistema fallido, sino que también le ayuda a:
  - Haga que los entornos de prueba sea consistentes con la producción.
  - Replicar sistemas en todas las regiones para disponibilidad.
  - Agregar instancias bajo demanda para hacer frente a la alta carga.
  - Replicar sistemas para dar a cada cliente una instancia dedicada.



- Paligros: sistemas de copos de nieve.

  - Un copo de nieve es una instancia de un sistema o parte de un sistema que es difícil de reconstruir.

- Principle: Assume systems are unreliable.

- Principle: Make everything reproducible. Debemos aplicar cualquier cambio que realicemos en todas las instancias del componente. De lo contrario crearemos una deriva en la cnfiguración.

- La deriva de configuración es una variación que ocurre con el tiempo en los sistemas que alguna vez fueron idénticos. La deriva de la configuración hace que sea más difícil mantener una automatización constante.

- The automation fear spiral. La espiral del miedo a la automatización describe cuántos equipos caen en la deriva de la configuración y la deuda técnica.

- La espiral del miedo de la automatización.

  ![image-20200401224551321](C:\Users\vthot4\AppData\Roaming\Typora\typora-user-images\image-20200401224551321.png)

- Principle: Ensure that you can repeat any process.

- Effective infrastructure teams have a strong scripting culture. If you can script a task, script it4. If it’s hard to script it, dig deeper. Maybe there’s a technique or tool that can help, or maybe you can simplify the task or handle it differently. Breaking work down into scriptable tasks usually makes it simpler, cleaner, and more reliable.



## Chapter 3. Infrastructure Platforms.

- La infraestructura como código requiere una plataforma de infraestructura dinámica, algo que puede usar para aprovisionar y cambiar recursos bajo demanda con una API.
- Las características clave de una plataforma de infraestructura dinámica son:
  - Proporcionar un conjunto de recursos informáticos, de redes y de almacenamiento.
  - Le permite aprovisionar y cambiar estos recursos bajo demanda.
  - Podemos aprovisionar, configurar y cambiar recursos mediante programación.
- Una malla de servicios es una red descentralizada de servicios que gestiona dinámicamente la conectividad entre partes de un sistema distribuido.
- Algunos ejemplos de mallas de servicio incluyen Hashicorp Consul , Envoy , Istio y Linkerd.



## Chapter 4. Core Practice : Define everything as code.

- Los beneficios de definir todo como código son:
  - Reusabiidad. 
  - Consistencia.
  - Transparencia.
- Las herramientas orientadas a la pila como Terraform y CloudFormation llegaron unos años más tarde, siguiendo el mismo modelo declarativo DSL.
- Recientemente 5 , existe una tendencia de nuevas herramientas de infraestructura que utilizan los lenguajes de programación de propósito general existentes para definir la infraestructura. Pulumi y AWS CDK (Cloud Development Kit) son ejemplos que admiten lenguajes como Typecript, Python y Java.
- Terraform, como la mayoría de las otras herramientas de aprovisionamiento de pila y herramientas de configuración del servidor, utiliza un lenguaje declarativo. En lugar de un lenguaje de procedimientos, que ejecuta una serie de instrucciones que utilizan la lógica de flujo de control, como si las declaraciones y mientras bucles, un lenguaje declarativo es un conjunto de sentencias que declaran el resultado que desea.
- IDEMPOTENCIA. Para sincronizar continuamente las definiciones del sistema, la herramienta que use para esto debe ser idempotente. No importa cuántas veces lo ejecutes, el resultado es el mismo. Si ejecuta una herramienta que no es idempotente varias veces, podría causar problemas.
- Leer ---> https://martinfowler.com/books/dsl.html
- Argumentos para volver a lenguajes de uso general:
  - La configuración no es código real. 
  - No necesitas aprender un nuevo lenguaje de desarrollo.
  - Los DSL no suelen tener entornos de desarrollo completos.
  - La posibilidad de usar bibliotecas.
  - Usar un lenguaje de programación común, permite combinar infraestructura y código de aplicación.
  - El testing en DSL suele ser más complejo.
- Principios de implementación del IaC:
  - Implementation Principle: Avoid mixing different types of code.
  - Implementation Principle: Separate infrastructure code concerns.
    - Especificación. Define la forma de la infraestructura.
    - Configuración. Define las cosas que varian cuando se aprovisionan diferentes instancias de infraestructura.
    -  Ejecución. Aplica la especificación y la configuración a los recursos de infraestructura.
    -  Orquestación. Combina múltiples especificaciones y configuraciones.
  - Separating specification and configuration. 
  - Implementation Principle: Treat infrastructure code like real code.