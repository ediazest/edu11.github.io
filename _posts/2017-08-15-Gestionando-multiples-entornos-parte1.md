---
  layout: post
  title: Gestionando múltiples entornos
  subtitle: Parte 1
  tags: xcode swift
  categories: 
  lang: es
  ref: multiple-environments-part1
---

Si eres desarrollador de cualquier tipo de software, coincidirás conmigo en que tener varios entornos ese muy beneficioso a la hora de obtener feedback sobre nuevas funcionalidades y encontrar esos últimos errores y detalles que solucionar.

Esto es algo primordial en el desarrollo de aplicaciones para móvil, ya que es posible distribuir una beta de la aplicación a diferentes grupos de usuario para que realicen labores de testing y control de calidad mientras es posible continuar con el desarrollo de nuevas funcionalidades.

Hay un problema con esto, y es que, como bien sabrás, no es posible instalar dos aplicaciones con el mismo nombre de paquete/bundle en un dispositivo, lo que significa que únicamente podrás tener una de las dos (o más) versiones instaladas al mismo tiempo, haciendo engorroso el paso entre una y otra versión. Para alguien cuya tarea es solamente probar la beta, puede no ser un gran problema, pero es bastante frustrante para los desarrolladores, que constantemente tenemos que probar en desarrollo, beta y release...

Para colmo, cuando has solucionado ese *pequeño problema*, te das cuenta de tanto la versión de desarrollo, como la beta y la última versión validada, comparten tanto nombre como icono, por lo que acertar con la versión que quieres se vuelve tan complicado como encontrar la bola debajo de los vasos. 

Además, si la aplicación se comunica con un *CMS* (gestor de contenido), las probabilidades de que el CMS también tenga diferentes entornos es muy alta, complicando nuestra configuración. Hay que reconocerlo, todos hemos añadido una constante con la url del servidor en una clase de utilidades, que tiene varias líneas comentadas con las urls del resto de entornos. Es un buen comienzo, podría ser peor y podríamos haber puesto la url directamente cuando se realiza comunicación con el servidor, pero sabemos que no es la solución. No solo es poco elegante, si no que es altamente propensa a errores.

Si lo que has leído hasta ahora se parece a la configuración de tus proyectos y cada vez que tienes que generar una nueva versión pasas miedo, este artículo te gustará. Si por el contrario, ya te has enfrentado a estos problemas y lo has solucionado como un ninja, me encantaría escuchar tus comentarios sobre cómo lo has solucionado y ver si podemos mejorar cogiendo lo mejor de ambas soluciones!

Esta es la primera parte de dos artículos (uno por plataforma). Por tener un nivel de complejidad un poco mayor, empezaré con la configuración en xCode para proyectos iOS. Próximamente publicaré una versión equivalente para Android Studio.


#### Dónde está mi Beta

Por defecto, todo proyecto creado en xCode contiene los entornos de configuración release y debug, por lo que para tener nuestro proyecto completo únicamente tendremos que crear el entorno beta.

Para ello, tendrás que hacer click en tu proyecto, y nuevamente en el proyecto en el panel de la izquierda (el primero de la lista, no el target). En la pestaña *Info*, haz click en el *"+"* que hay dentro del bloque *"Configurations"* y selecciona *"Duplicate ‘Release’ Configuration”*. Cuando lo hagas, podrás renombrar esta configuración como quieras, pero por claridad la llamaremos *"Beta"*.

![Creando configuración Beta](../../../assets/images/multiple-env/part1/duplicating-release-configuration.png "Duplicando configuraciones en xCode")

#### Cada *Config* con su nombre

Ahora sí, selecciona el *target* de tu proyecto y haz click en la pestaña *"Build Settings"*.

Por facilidad, yo suelo utilizar el buscador que xCode nos proporciona arriba a la derecha, pero si no, dentro de la sección *"Packaging"*, encontrarás un campo *"Product Name*". Expándelo y encontrarás nuestras tres configuraciones. Nómbralas como quieras, yo suelo dejar la versión de desarrollo con el nombre del *target*, ya que normalmente tendrá un icono diferente de la versión a publicar (o incluso sin icono), y simplemente añadir el sufijo *Beta* para que aquellos destinados a probar la aplicación tengan claro cuál es cual.

![Cambiando Product Name](../../../assets/images/multiple-env/part1/changing-product-name.png "Añadiendo sufijo Beta a la configuración")

#### ¡Las quiero todas!

Como comentaba en la introducción, normalmente tanto desarrolladores como *testers* estamos interesados en poder tener disponibles las diferentes versiones generadas en el mismo dispositivo, ahorrándonos tiempo y problemas. Sin el paso que estamos a punto de realizar, cada vez que cambies de versión a instalar, el sistema operativo sobreescribirá la versión que tengas instalada (incluso si viene del AppStore).

Para conseguir esta independencia, crearemos un parámetro de configuración definido por nosotros mismos. La puedes llamar como quieras, pero encontrarás referencias por internet con el nombre *BUNDLE_ID_SUFFIX*. Una vez creado, abre el nuevo *dropdown* y añade los valores para la cada configuración, dejando *"Release"* en blanco.

![Sufijo para Bundle id ](../../../assets/images/multiple-env/part1/bundle-id-suffix.png "Añadiendo sufijo al Bundle id")

Lo siguiente será buscar el fichero *.plist* (normalmente *Info.plist* a no ser que lo hayas renombrado) y localizar la línea *"Bundle Identifier"*. Haz doble click y añade *"${BUNDLE_ID_SUFFIX}"*. De esta manera, automáticamente tendremos los tres identificadores funcionando sin tener que preocuparnos de sustituir ningún texto.

![Actualizando plist](../../../assets/images/multiple-env/part1/bundle-identifier.png "Actualizando plist")

#### Iconos, por favor

A partir de este punto, ya podemos tener instaladas todas las versiones en nuestro dispositivo, pero seguimos teniendo el inconveniente de que todas muestran el mismo icono.

Seguiremos el mismo procedimiento que con el *bundle id* par ahorrarnos tener que escribir el nombre exacto de cada icono.
Para ello volvemos a la vista de *target* y, en la pestaña de *"Build Settings"*, buscamos *“Asset Catalog App Icon Set Name”*, que se encuentra dentro del grupo *Asset Catalog Compiler — Options*. Esta vez no hace falta expandir para ver las tres configuraciones ya que simplemente añadiremos nuestro *${BUNDLE_ID_SUFFIX}* al final del valor actual (por defecto *AppIcon*).
Tras confirmar el cambio, automáticamente se desplegará y veremos nuestras tres configuraciones con su respectivo icono.

![Sufijo para app icon](../../../assets/images/multiple-env/part1/asset-catalog-icon.png "Añadiendo identificador a cada imagen")

Por último tendremos que añadir los nuevos iconos a nuestro proyecto.

Abre el archivo *Assets.xcassets* y abajo, en la esquina izquierda, haz click sobre el botón "+" y selecciona *"App icons & Launch images > New iOS App Icon"*. Repite este procedimiento y, finalmente, renombra a uno *"AppIcon.dev"* y al otro *"AppIcon.beta"*.

![Creando nuevos iconos](../../../assets/images/multiple-env/part1/creating-new-icons.png "Creando nuevo iconos")

Ahora es tu turno y tendrás que rellenar los huecos con esos iconos que ha generado por ti, con suerte tu diseñador.

#### ¿Y ahora cómo instalo cada versión?

No podemos continuar sin que introduzca el término *"Scheme"*, que lo podríamos traducir como el plan de juego de nuestra aplicación. Aquí se especifica qué configuración queremos que se utiliza cuando ejecutamos la aplicación en modo desarrollo, pruebas, creando el fichero *ipa*, etc.

Para acceder a estos *schemes* deberás ir a *Product > Scheme > Manage Schemes*, o bien hacer click en el selector de targets al lado del botón de *stop*, arriba a la izquierda.

En este punto, lo más fácil sería duplicar el primero de la lista, que es el que se utiliza por defecto (el resto seguramente sean debido a alguna librería externa que hayas importado).

![Duplicando xCode scheme](../../../assets/images/multiple-env/part1/duplicating-scheme.png "Duplicando xCode scheme")

Casi que por definición, la versión *beta* de la aplicación es aquella que está preparada para ser probada antes de publicarla en AppStore. Por lo tanto, únicamente nos interesa cambiar el comportamiento cuando la versión está a punto de ser *encapsulada*, por lo que hacemos click en *"Archive"* en el menú lateral y en *"Build Configurations" seleccionamos nuestra nueva configuración beta. 

![Seleccionando configuración Beta](../../../assets/images/multiple-env/part1/setting-beta-archive.png "Seleccionando configuración Beta")

Si por cualquier razón quisiéramos que se ejecutase dicha configuración mientras desarrollamos, simplemente habría que realizar el mismo paso pero en el menú *"Debug"*.

#### Configurando diferentes entornos

Para completar nuestro ejemplo, necesitamos ser capaces de cambiar aquellos parámetros específicos de cada entorno simplemente seleccionando un *scheme* u otro.

Existen diferentes opciones para conseguir esta tarea, pero a mí personalmente me gusta crear un fichero de configuración *plist* con aquellos campos dependientes, ya que muchas veces no solo la url es diferente.

De cualquier manera, el primer paso es común y consiste en añadir una nueva línea en el fichero *Info.plist*: necesitaremos añadir la clave *"Configuration*" con valor *"$(CONFIGURATION)"*. De esta manera, la configuración utilizada durante la compilación será accesible a través de la variable *Configuration*. Como imaginarás, esto nos facilita mucho las cosas, puesto que automáticamente seremos capaces de determinar la configuración en cualquier punto de la aplicación.

![configuration en Info.plist](../../../assets/images/multiple-env/part1/configuration-key.png "Añadiendo configuration a Info.plist")

Si quieres probar si funciona, puedes añadir la siguiente línea en el método *"didFinishLaunchingWithOptions"* de tu *AppDelegate*:
{% highlight swift %}
    print(Bundle.main.object(forInfoDictionaryKey:"Configuration")!)
{% endhighlight %}

El siguiente paso será crear un fichero *plist* que llamaremos *"Config"* y añadir un diccionario con los valores necesarios para cada entorno. Si quieres, puedes utilizar el siguiente ejemplo como punto de partida:

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Debug</key>
    <dict>
      <key>endpoint</key>
      <string>Development API Endpoint</string>
    </dict>
    <key>Release</key>
    <dict>
      <key>endpoint</key>
      <string>Release API Endpoint</string>
    </dict>
  </dict>
</plist>
{% endhighlight %}

Para que sea más fácil leer este nuevo fichero de configuración, crearemos una nueva clase *Singleton* en la que se cargará el fichero de configuración en un diccionario y se utilizará para acceder a los parámetros necesarios. 

{% highlight swift %}
import Foundation

class Config: NSObject {
    // Singleton instance
    static let sharedInstance = Config()
    
    var configs: NSDictionary!
    
    override init() {
        // (1) Finds the Configuration property from Info.plist
        let currentConfiguration = Bundle.main.object(forInfoDictionaryKey: "Configuration")!
        // (2) Reads the Config plist file
        let path = Bundle.main.path(forResource: "Config", ofType: "plist")!
        // (3) Find current configuration, rollout to Release if not found
        if let dictionary = NSDictionary(contentsOfFile: path)!.object(forKey: currentConfiguration) as? NSDictionary{
            configs = dictionary
        } else {
            configs = NSDictionary(contentsOfFile: path)!.object(forKey: "Release") as! NSDictionary
        }
    }
}

extension Config {
    func apiEndpoint() -> String {
        return configs.object(forKey: "endpoint") as! String
    }
}
{% endhighlight %}

De esta manera las propiedades de configuración son accesibles desde cualquier parte de tu aplicación.

{% highlight swift %}
Config.sharedInstance.apiEndpoint()
{% endhighlight %} 

Como nota importante, en el código anterior verás que se ha introducido un check para determinar si existe 
información específica para la configuración actual, pasando a utilizar la de *Release* en el caso contrario.

#### Conclusión

Siempre es una buena idea tratar de reducir errores al comienzo de los proyectos y tener claro cómo desplegar en los diferentes entornos es clave. Imagina que en lugar de tres entornos como en el ejemplo, tenemos cuatro o más, todos apuntando a diferentes servidores... Lo explicado en este post te permitirá crear múltiples esquemas y configuraciones para adaptarte a todos ellos. Además, si a esto le unes la integración con **[Fastlane](https://docs.fastlane.tools)** ahorrarás múltiples horas de sufrimiento! 

Espero que este post ayude en tu camino a desarrollar fantásticas aplicaciones iOS. Próximamente publicaré una versión equivalente para Android Studio.