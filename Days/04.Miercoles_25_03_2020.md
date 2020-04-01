# Miércoles 25/03/2020

## WORK

- Paso de variables de entorno a los docker de un POD. https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/

  ```bash
  command: [ "sh", "-c"]
        args:
        - while true; do
            echo -en '\n';
            printenv MY_NODE_NAME MY_POD_NAME MY_POD_NAMESPACE;
            printenv MY_POD_IP MY_POD_SERVICE_ACCOUNT;
            sleep 10;
          done;
        env:
          - name: MY_NODE_NAME
            valueFrom:
              fieldRef:
                fieldPath: spec.nodeName
          - name: MY_POD_NAME
            valueFrom:
              fieldRef:
                fieldPath: metadata.name
  ```

  

- Documentación del despliegue de infraestructura.

- Análisis de RASA.

  

##  Blogs

- Aceptémoslo el estilo de vida que conociamos no va a volver nunca: https://www.technologyreview.es/s/12034/aceptemoslo-el-estilo-de-vida-que-conociamos-no-va-volver-nunca
- El coronavirus podría empeorar el cambio climático: https://www.technologyreview.es/s/12011/el-coronavirus-podria-empeorar-el-cambio-climatico-largo-plazo
- A complete guide about how to break the data monolith. https://medium.com/packlinkeng/a-complete-guide-about-how-to-break-the-data-monolith-caa2ab2d01f6
- Migración a Google Cloud Platform (GCP). https://blog.hike.in/migration-to-google-cloud-platform-gcp-17c397e564b8
- Infraestructura reactiva con autoescalado preventivo. https://blog.hike.in/autoscaling-6fb431693b91
- Recursos de Kubernetes: Pods: https://www.josedomingo.org/pledin/2018/06/recursos-de-kubernetes-pods/
- 





## Clases de inglés.

- Your Digital footprint.

  

## (Book) The Phoenix Project

**CHAPTER 14 Tuesday, September 16**

“You know, I’ve been thinking,” Chris says. “Maybe my group being outsourced wouldn’t be the worst thing in the world. I’ve been in software development for virtually my entire career. I’m used to everyone demanding miracles, expecting the impossible, people changing requirements at the last minute, but, after living through this latest nightmare project, I wonder if it might be time for a change…”

Shaking his head, he continues, “It’s harder than ever to convince the business to do the right thing. They’re like kids in a candy store. They read in an airline magazine that they can manage their whole supply chain in the cloud for $499 per year, and suddenly that’s the main company initiative. When we tell them it’s not actually that easy, and show them what it takes to do it right, they disappear. Where did they go?



**CHAPTER 15, Thursday, September 17**

Unplanned work is what prevents you from doing it. Like matter and antimatter, in the presence of unplanned work, all planned work ignites with incandescent fury, incinerating everything around it. Like Phoenix.

“Very good,” he says. “You’ve put together tools to help with the visual management of work and pulling work through the system. This is a critical part of the First Way, which is creating fast flow of work through
Development and IT Operations. Index cards on a kanban board is one of the best mechanisms to do this, because everyone can see WIP. Now you must continually eradicate your largest sources of unplanned work, per the Second Way.

As I jot a quick reminder to look for it, he continues, “Goldratt taught us that in most plants, there are a very small number of resources, whether it’s men, machines, or materials, that dictates the output of the entire system. We call this the constraint—or bottleneck. Either term works. Whatever you call it, until you create a trusted system to manage the flow of work to the constraint, the constraint is constantly wasted, which means that the constraint is likely being drastically underutilized.

“That means you’re not delivering to the business the full capacity available to you. It also likely means that you’re not paying down technical debt, so your problems and amount of unplanned work continues to
increase over time,” he says.
He continues, “You’ve identified this Brent person as a constraint to restore service. Trust me, you’ll find that he constrains many other important flows of work, as well.”

I try to interrupt to ask a question, but he continues headlong, “There are five focusing steps which Goldratt describes in The Goal: Step 1 is to identify the constraint. You’ve done that, so congratulations. Keep challenging yourself to really make sure that’s your organizational constraint, because if you’re wrong, nothing you do will matter. Remember, any improvement not made at the constraint is just an illusion, yes?
“Step 2 is to exploit the constraint,” he continues. “In other words, make sure that the constraint is not allowed to waste any time. Ever.
It should never be waiting on any other resource for anything, and it should always be working on the highest priority commitment the IT Operations organization has made to the rest of the enterprise. Always.”
I hear him say encouragingly, “You’ve done a good job exploiting the constraint on several fronts. You’ve reduced reliance on Brent forunplanned work and outages. You’ve even started to figure out how to
exploit Brent better for the three other types of work: business and IT projects and changes. Remember, unplanned work kills your ability to do planned work, so you must always do whatever it takes to eradicate
it. Murphy does exist, so you’ll always have unplanned work, but it must be handled efficiently. You’ve still got a long way to go.”
In a more stern voice, he says, “But you’re ready to start thinking about Step 3, which is to subordinate the constraint. In the Theory of Constraints, this is typically implemented by something called Drum- Buffer Rope. In The Goal, the main character, Alex, learns about this when he discovers that Herbie, the slowest Boy Scout in the troop, actually dictates the entire group’s marching pace. Alex moved Herbie to the front of the line to prevent kids from going on too far ahead. Later at Alex’s plant, he started to release all work in accordance to the rate it could be consumed by the heat treat ovens, which was his plant’s bottleneck.
That was his real-life Herbie.

But before you get a big head, I’ll tell you that there’s still a big piece of the First Way that you’re missing. Jimmy’s problem with the auditors shows that he can’t distinguish what work matters to the business versus what doesn’t. And incidentally, you have the same problem, too. Remember, it goes beyond
reducing WIP. Being able to take needless work out of the system is more important than being able to put more work into the system. To do that, you need to know what matters to the achievement of the business objectives, whether it’s projects, operations, strategy, compliance with laws and regulations, security, or whatever.”

*“Remember, outcomes are what matter—not the process, not controls, or, for that matter, what work you complete.”*

