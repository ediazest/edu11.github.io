---
  layout: post
  title: Organizando tu repositorio Git
  tags: git management
  categories: 
  lang: es
  ref: managing-git
---

La correcta gestión del repositorio es una tarea que debería ser primordial durante el periodo de vida de todo proyecto, aunque no siempre se le dedica el tiempo necesario para implantar una nomenclatura y manera de actuar común entre todos los desarrolladores dentro de la misma empresa. Esta des-coordinación puede provocar errores por la diferentes interpretaciones de los desarrolladores, acrecentada por el tiempo de vida del proyecto y la entrada/salida de varios desarrolladores.

Una vez trabajé en un proyecto Android en el cual la rama máster llevaba años sin actualizarse, a pesar de haber publicado varias actualizaciones de la aplicación en GooglePlay. Debido a esto, un desarrollador externo contratado para realizar unos pequeños ajustes, partió de dicha rama para realizar su trabajo, perdiendo así años de actualizaciones disponibles en la rama de desarrollo. Cuando me contrataron, me comentaron que les daba la impresión de que había fallos que ya se habían solucionado y faltabas cosas...cómo no?! Ese es un claro ejemplo de cómo algo tan simple como actualizar nuestro repositorio puede costarnos tiempo (y dinero) a poco que nos despistemos.

Una herramienta que me ha ayudado a gestionar mejor el repositorio *(sí, uso un software para git en lugar de usar la consola)* es **[SourceTree, de Atlassian](https://www.sourcetreeapp.com/)**. Personalmente creo que es una de las mejores utilidades para un desarrollador, ya que en muy pocos clicks podemos clonar o crear un repositorio, pudiendo agruparlos por empresas, proyectos, visualizar todas las ramas existentes, crear un nuevo stash (guardado del estado actual de trabajo, por si mientras estás desarrollando esa nueva funcionalidad te das cuenta de un error grave que necesitas solucionarlo ya), utilizar Git-Flow fácilmente, buscar entre tus commits...Como dicen en su página,una herramienta simple para principiantes, potente para expertos. Para descargarlo simplemente tendremos que crear una cuenta en Atlassian y activar la licencia gratuita que nos proporciona.

![Ventana de SourceTree](../../../assets/images/managing-git/sourcetree-screen.png "Vista principal de SourceTree")

Desde hace un tiempo, en todos los proyectos en los que he trabajado hemos seguido GitFlow para la gestión del repositorio, originalmente idea por **[Vincent Driessen](http://nvie.com/posts/a-successful-git-branching-model/)**, que es la manera que yo recomendaría para tener un repositorio limpio y en el que esté claro lo que se ha hecho.

Con SourceTree, iniciar GitFlow en un proyecto son solo 3 clicks y automáticamente crea la rama de desarrollo y genera internamente las *"carpetas"* que almacenarán el resto de ramas temporales que necesitarás durante el proyecto (hotfix, release y feature). Si lo quisieras, puedes cambiar el nombre que hace referencia a dichas ramas. Yo suelo dejarlo por defecto porque así es más probable que el próximo desarrollador que trabaje en el proyecto sepa qué la utilidad de cada rama.

![Iniciando GitFlow con SourceTree](../../../assets/images/managing-git/creating-gitflow.png "Iniciando GitFlow")

La estrategia a seguir según GitFlow es la siguiente:
  
  - **Master:** Esta rama almacena el código que ha sido publicado oficialmente y siempre debería ser etiquetado para la fácil identificación de versiones.

  - **Develop:** Esta rama se utiliza para integrar las nuevas funcionalidades creadas.

  - **Feature:** Para nuevas funcionalidades, y solución de errores no urgentes. Estas ramas se crean siempre partiendo desde la rama develop, deberían tener un periodo de vida corto (para ello nos aseguramos que sólo vamos a efectuar una tarea) y se junta de nuevo en develop una vez esté lista. La nomenclatura que suelo seguir a la hora de nombrar estas ramas es *feature/{iniciales del desarrollador}-{nombre de la tarea}*, ej. *feature/ede-miNuevaFuncionalidad*.

  - **Release:** Cuando hemos completado todas las funcionalidades programadas y toca publicar una nueva versión, pasaremos a crear una rama desde develop en release. El código de esta rama se despliega en un entorno de pruebas, se prueba y si se descubriera un fallo se pasaría a resolver en la misma rama release. Esto es un proceso iterativo hasta que todos estemos contentos y dispuestos a publicar la nueva versión. Una vez llegado a ese punto, deberemos hacer un merge tanto a máster como a develop, para asegurarnos de que los cambios no serán sobrescritos accidentalmente por nuevos desarrollos. La nomenclatura a emplear aquí podría ser algo como *release/{versión a publicar}-{fecha}* ej. *release/1.2.0-11082017*.
  
  - **Hotfix:** Estas ramas son utilizadas para solucionar errores urgentes, ya que son errores que ocurren en una versión ya publicada *(normalmente son aquellos que pueden hacer la aplicación cerrarse)*. Por lo tanto, estas ramas solo se crean a partir de master y, al solucionar el problema, vuelven a master y a develop, para asegurarnos que no se pierde entre otros desarrollos.

![GitFlow workflow](../../../assets/images/managing-git/gitflow-diagram.png "Flujo de trabajo con GitFlow")  


Existen diferentes modelos para gestionar un repositorio git y, aunque GitFlow tiene sus pequeños inconvenientes, hoy en día es uno de los más extendidos en el mundo del desarrollo. Si actualmente no sigues una estrategia clara de organización, te recomiendo probarla!

<br>
<br>
<br>

***
Para más información sobre otros modelos y los problemas que puede tener GitFlow, puedes ampliar este blog con los siguientes enlaces
- **[Comparando flujos de trabajo, por Atlassian](https://www.atlassian.com/git/tutorials/comparing-workflows#gitflow-workflow)**
- **[Reconsiderando GitFlow](http://endoflineblog.com/gitflow-considered-harmful)**
- **[GitHub Flow](https://guides.github.com/introduction/flow/)**
