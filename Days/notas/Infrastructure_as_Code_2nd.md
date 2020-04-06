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

  ![image-20200401224551321](./images/image-20200401224551321.png)

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



## Chapter 5. Building Infrastructure Stacks as Code.

-  **stack code** An Infrastructure Stack is a collection of infrastructure resources that you define, provision, and update as a unit.You write source code to define the elements of a stack, which are resources and services that your infrastructure platform provides.
- Each stack is defined by source code that declares what infrastructure elements it should include.
- **stack instance.** You can use a single stack project to provision more than one stack instance. When you run the stack tool for the project, it uses the platform API to ensure the stack instance exists, and to make it match the project code.
- **Configuring servers in a stack.** You should decouple code that builds servers from code that builds stacks. Doing this makes the code easier to understand, simplifies changes by decoupling them, and supports reusing and testing server code.

**Patterns and antipatterns for structuring stacks**

The description of each pattern and antipattern follows a set format, with each of these fields:

- **Name.** The name of the pattern or antipattern.
- **Also Known As.** Other names you may have heard used to describe this.
- **Motivation.** Why people may decide to implement this pattern or antipattern.
- **Applicability.** When this pattern is a good idea, and when it’s not.
- **Consequences.** What you should think about if you choose to implement this pattern. What happens if you implement this antipattern.
- **Implementation.** How to implement the pattern.
- **Related patterns.** Other patterns and antipatterns, particularly alternatives.

- Note: The term blast radius describes the potential damage a given change could make to a system. It’s usually based on the elements of the system you’re changing, what other elements depend on them, and what elements are shared.



**Antipattern: Monolithic Stack**

- A Monolithic Stack is an infrastructure stack that includes too many elements, making it difficult to maintain.
- Also known as: Spaghetti stack, big ball of mud.
- Motivation: People build monolithic stacks because the simplest way to add element to a system is to add it to the existing project. A single stack is simpler to manage
- Applicability: A monolithic stack may be appropriate when your system is small and simple. It's not appropriate when your system grows, taking longer to provision and update.
- Consequences: Changing a large stack is riskier than changing a smaller stack. More things can go wrong-it has a larger blast radius. the impact of a failed change may be broader since there are more services and applications within the stack. 
- Implementation: You build a monolithic stack by creating an infrastructure stack project and then continuously adding code, rather than splitting it into multiple stacks.



**Pattern: Application Group Stack**

- An Application Group Stack includes the infrastructure for multiple related applications or services. The infrastructure for all of these applications is provisioned and modified as a group.
- Also Known As: Combined stack, service group stack, multi-application stack.
- Motivation: Defining the infrastructure for multiple related services together can make it easier to manage the application as a single unit.
- Applicability: This pattern can work well when a single team owns the infrastructure and deployment of all of the pieces of the application. An application group stack can align the boundaries of the stack to the team’s responsibilities.
- Consequences: Grouping the infrastructure for multiple applications together also combines the time, risk, and pace of changes. The team needs to manage the risk to the entire stack for every change, even if only one part is changing. This pattern is inefficient if some parts of the stack change more frequently than others. The time to provision, change, and test a stack is based on the entire stack. So again, if it’s common to change only one part of a stack at a time, having it grouped adds unnecessary overhead and risk.
- Implementation: To create an application group stack, you define an infrastructure project that builds all of the infrastructure for a set of services. You can provision and destroy all of the pieces of the application with a single command.



**Pattern: Service Stack**

- A Service Stack manages the infrastructure for each deployable application component in a separate infrastructure stack.
- Also Known As: Stack per app, single service stack
- Motivation: Service stacks align the boundaries of infrastructure to the software that runs on it. This alignment limits the blast radius for a change to one service, which simplifies the process for scheduling changes. Service teams can own the infrastructure that relates to their software. 
- Applicability: Service stacks can work well with microservice application architectures. They also help organizations with autonomous teams to ensure each team owns its infrastructure.
- Consequences: If you have multiple applications, each with an infrastructure stack, there could be an unnecessary duplication of code. 
- Implementation: Each application or service has a separate infrastructure code project. When creating a new application, a team might copy code from another application’s infrastructure. Or the team could use a reference project, with boilerplate code for creating new stacks. 



**Pattern: Micro Stack**

- The Micro Stack pattern divides the infrastructure for a single service across multiple stacks.
- Motivation: Different parts of a service’s infrastructure may change at different rates. Or they may have different characteristics which make them easier to manage separately. 
- Consequences: Although smaller stacks are themselves simpler, handling the dependencies between them is more complicated.
- Implementation: Adding a new microstack involves creating a new stack project. You need to draw boundaries in the right places between stacks to keep them appropriately sized and easy to manage. 



## Chapter 6. Using Modules to Share Stack Code.

