![Patrone de diseño para humanos](https://cloud.githubusercontent.com/assets/11269635/23065273/1b7e5938-f515-11e6-8dd3-d0d58de6bb9a.png)

***

<p align="center">
🎉 ¡Una explicación ultra sencilla de los patrones de diseño! 🎉
</p>
<p align="center">
Este es un tema que puede hacer que la mente de cualquiera se tambalee fácilmente. Aquí trato de hacer que se introduzcan a tu mente (y tal vez en la mía) explicándolos de la manera <i> más sencilla </ i> posible.
</p>

***

<p align="center"><b> ¿Te gustó esta guía y quieres más contenido similar? </b><br>
¿Te gustó esta guía y quieres más contenido similar? </ b> <br> Suscríbase para el lanzamiento de <a href="http://hugobots.com"> Hugobots </a> o <a href="http://twitter.com/kamranahmedse">¡Sígueme en Twitter</a>!</p>

***

🚀 Introducción
=================

Los patrones de diseño nacen como soluciones para problemas recurrentes; **pautas sobre cómo abordar ciertos problemas**. No son clases, paquetes o bibliotecas que puedes conectar a tu aplicación y esperar a que suceda la magia. Estas son, más bien, pautas sobre cómo abordar ciertos problemas en ciertas situaciones.

> Los patrones de diseño son soluciones para problemas recurrentes; pautas sobre cómo abordar ciertos problemas

Wikipedia los describe como

> En ingeniería del software, un patrón de diseño de software es una solución general reutilizable para un problema común en un contexto dado en el diseño. No es un diseño terminado que se puede transformar directamente en código fuente o máquina. Es una descripción o plantilla de cómo resolver un problema que se puede utilizar en muchas situaciones diferentes.

⚠️ ¡Cuidado!
-----------------
- Los patrones de diseño no son la panacea para todos los problemas.
- No hay que intentar forzarlos; Es posible que sucendan cosas inesperadas si se fuerzan. Hay que tener en cuenta que los patrones de diseño son soluciones **a** problemas, no soluciones **para encontrar problemas**; así que, no pienses demasiado.
- Si se usan en el lugar correcto y de manera correcta, pueden demostrar ser una gran ventaja; o, de lo contrario, pueden dar lugar a un lío horrible en el código fuente.

> También hay que tener en cuenta que los ejemplos del código están en PHP 7, sin embargo, esto no debería ser un condicionante porque los conceptos son los mismos de todos modos. Además, **está en curso el soporte para otros lenguajes**.

Tipos de Patrones de Diseño
-----------------

* [Creación](#creational-design-patterns)
* [Estructura](#structural-design-patterns)
* [Comportamiento](#behavioral-design-patterns)

Patrones de Diseño de Creación
==============================

De forma sencilla
> Los patrones de creación se centran en cómo crear una instancia de un objeto o grupo de objetos relacionados.

Wikipedia dice
> En ingeniería de software, los patrones de diseño de creación son patrones de diseño que se ocupan de los mecanismos de creación de objetos, tratando de crear objetos de una manera adecuada a la situación. La forma básica de creación de objetos podría ocasionar problemas de diseño o agregar complejidad al diseño. Los patrones de diseño de creación resuelven este problema controlando de algún modo la creación de este objeto.

 * [Simple Factory](#-simple-factory)
 * [Factory Method](#-factory-method)
 * [Abstract Factory](#-abstract-factory)
 * [Builder](#-builder)
 * [Prototype](#-prototype)
 * [Singleton](#-singleton)

🏠 Simple Factory
-----------------
Ejemplo del mundo real
> Considera la siguiente situación, estás construyendo una casa y necesitas puertas. Sería un desastre si cada vez que necesitas una puerta, te pones la ropa de carpintero y comienzas a hacer una puerta en tu casa. En cambio, lo haces desde una fábrica.

De forma sencilla
> Simple Factory simplemente genera una instancia para el cliente sin exponer ninguna lógica de creación de instancias al cliente.

Wikipedia dice
> En la programación orientada a objetos (OOP), una fábrica es un objeto para crear otros objetos: formalmente, una fábrica es una función o método que devuelve objetos de un prototipo o clase variable de alguna llamada a método, que se supone que es "nueva".

**Código de ejemplo**

En primer lugar, tenemos una interfaz de puerta y la implementación
```php
interface Door
{
    public function getWidth(): float;
    public function getHeight(): float;
}

class WoodenDoor implements Door
{
    protected $width;
    protected $height;

    public function __construct(float $width, float $height)
    {
        $this->width = $width;
        $this->height = $height;
    }

    public function getWidth(): float
    {
        return $this->width;
    }

    public function getHeight(): float
    {
        return $this->height;
    }
}
```
Entonces tenemos nuestra fábrica de puertas que hace la puerta y la devuelve
```php
class DoorFactory
{
    public static function makeDoor($width, $height): Door
    {
        return new WoodenDoor($width, $height);
    }
}
```
Y luego puede usarse como
```php
$door = DoorFactory::makeDoor(100, 200);
echo 'Width: ' . $door->getWidth();
echo 'Height: ' . $door->getHeight();
```

**¿Cuándo usarlo?**

Cuando la creación de un objeto no es simplemente asignar e implica algo de lógica, tiene sentido colocarlo en una fábrica dedicada en lugar de repetir el mismo código en todas partes.

🏭 Factory Method
-----------------

Ejemplo del mundo real
> Considere el caso de un gerente de recursos humanos. Es imposible que una persona entreviste para cada una de las posiciones. Según la vacante, debe decidir y delegar los pasos de la entrevista a diferentes personas.

De forma sencilla
> Proporciona una forma de delegar la lógica de creación de instancias a clases secundarias.

Wikipedia dice
> En la programación basada en clases, el patrón Factory Method es un patrón creacional que usa métodos de fábrica para tratar el problema de crear objetos sin tener que especificar la clase exacta del objeto que se creará. Esto se hace creando objetos llamando a un método de la fábrica, ya sea especificado en una interfaz e implementado por clases secundarias, o implementado en una clase base y opcionalmente anulado por clases derivadas, en lugar de llamar a un constructor.

 **Código de ejemplo**

Tomando nuestro ejemplo de gerente de contratación anterior. En primer lugar, tenemos una interfaz de entrevistador y algunas implementaciones para ella

```php
interface Interviewer
{
    public function askQuestions();
}

class Developer implements Interviewer
{
    public function askQuestions()
    {
        echo 'Asking about design patterns!';
    }
}

class CommunityExecutive implements Interviewer
{
    public function askQuestions()
    {
        echo 'Asking about community building';
    }
}
```

Now let us create our `HiringManager`

```php
abstract class HiringManager
{

    // Factory method
    abstract protected function makeInterviewer(): Interviewer;

    public function takeInterview()
    {
        $interviewer = $this->makeInterviewer();
        $interviewer->askQuestions();
    }
}

```
Ahora cualquier niño puede extenderlo y proporcionar el entrevistador requerido
```php
class DevelopmentManager extends HiringManager
{
    protected function makeInterviewer(): Interviewer
    {
        return new Developer();
    }
}

class MarketingManager extends HiringManager
{
    protected function makeInterviewer(): Interviewer
    {
        return new CommunityExecutive();
    }
}
```
y luego puede usarse como
```php
$devManager = new DevelopmentManager();
$devManager->takeInterview(); // Output: Asking about design patterns

$marketingManager = new MarketingManager();
$marketingManager->takeInterview(); // Output: Asking about community building.
```

**¿Cuándo usarlo?**

Útil cuando hay algún procesamiento genérico en una clase pero la subclase requerida se decide dinámicamente en tiempo de ejecución. O poniéndolo en otras palabras, cuando el cliente no sabe qué subclase exacta podría necesitar.

🔨 Abstract Factory
-------------------

Ejemplo del mundo real
> Vamos a extender nuestro ejemplo de las puertas desde el patrón Simple Factory. Según sus necesidades, puede obtener una puerta de madera de una tienda de puertas de madera, una puerta de hierro de una tienda de hierro o una puerta de PVC de la tienda correspondiente. Además, puede necesitar un profesional con diferentes tipos de especialidades para encajar en la puerta, por ejemplo, un carpintero para puerta de madera, un soldador para puerta de hierro, etc. Como puede ver, ahora hay una dependencia entre las puertas, la puerta de madera necesita carpintero, la puerta de hierro necesita un soldador, etc.

De forma sencilla
> Una fábrica de fábricas; una fábrica que agrupa a las fábricas individuales pero relacionadas / dependientes juntas sin especificar sus clases concretas.

Wikipedia dice
> El patrón abstracto de fábrica proporciona una manera de encapsular un grupo de fábricas individuales que tienen un tema común sin especificar sus clases concretas.

**Código de ejemplo**

Traduciendo el ejemplo de la puerta de arriba. En primer lugar, tenemos nuestra interfaz `Door` y alguna implementación para ello

```php
interface Door
{
    public function getDescription();
}

class WoodenDoor implements Door
{
    public function getDescription()
    {
        echo 'I am a wooden door';
    }
}

class IronDoor implements Door
{
    public function getDescription()
    {
        echo 'I am an iron door';
    }
}
```
Luego tenemos algunos expertos en adaptación para cada tipo de puerta
```php
interface DoorFittingExpert
{
    public function getDescription();
}

class Welder implements DoorFittingExpert
{
    public function getDescription()
    {
        echo 'I can only fit iron doors';
    }
}

class Carpenter implements DoorFittingExpert
{
    public function getDescription()
    {
        echo 'I can only fit wooden doors';
    }
}
```

Ahora tenemos nuestra fábrica abstracta que nos permitiría hacer una familia de objetos relacionados, es decir, una fábrica de puertas de madera crearía una puerta de madera y un experto en accesorios de puertas de madera y una fábrica de puertas de hierro crearía una puerta de hierro y un experto en herrajes para puertas
```php
interface DoorFactory
{
    public function makeDoor(): Door;
    public function makeFittingExpert(): DoorFittingExpert;
}

// Wooden factory to return carpenter and wooden door
class WoodenDoorFactory implements DoorFactory
{
    public function makeDoor(): Door
    {
        return new WoodenDoor();
    }

    public function makeFittingExpert(): DoorFittingExpert
    {
        return new Carpenter();
    }
}

// Iron door factory to get iron door and the relevant fitting expert
class IronDoorFactory implements DoorFactory
{
    public function makeDoor(): Door
    {
        return new IronDoor();
    }

    public function makeFittingExpert(): DoorFittingExpert
    {
        return new Welder();
    }
}
```
Y luego puede usarse como
```php
$woodenFactory = new WoodenDoorFactory();

$door = $woodenFactory->makeDoor();
$expert = $woodenFactory->makeFittingExpert();

$door->getDescription();  // Output: I am a wooden door
$expert->getDescription(); // Output: I can only fit wooden doors

// Same for Iron Factory
$ironFactory = new IronDoorFactory();

$door = $ironFactory->makeDoor();
$expert = $ironFactory->makeFittingExpert();

$door->getDescription();  // Output: I am an iron door
$expert->getDescription(); // Output: I can only fit iron doors
```

Como se puede ver, la fábrica de puertas de madera ha encapsulado al "carpintero" y la "puerta de madera", también la fábrica de puertas de hierro ha encapsulado la "puerta de hierro" y al "soldador". Y así nos ayudó a asegurarnos de que para cada una de las puertas creadas, no recibimos a un experto en ajuste incorrecto.
**¿Cuándo usarlo?**

Cuando hay dependencias interrelacionadas con la lógica de creación

👷 Builder
----------
Ejemplo del mundo real
> Imagina que estás en la hamburguesería Hardee's y pides una hamburguesa específica, digamos, la "Big Hardee" y la entregan sin *ninguna pregunta*; este es el ejemplo de Simple Factory. Pero hay casos en que la lógica de creación puede implicar más pasos. Por ejemplo, si desea personalizar los ingredientes, tiene varias opciones sobre cómo se hace la hamburguesa, por ejemplo, ¿qué pan desea? ¿Qué tipo de salsas te gustaría? ¿Qué queso quieres? etc. En tales casos, el patrón  Builder es el adecuado.
De forma sencilla
> Le permite crear diferentes sabores de un objeto mientras evita la contaminación del constructor. Útil cuando puede haber varios sabores de un objeto. O cuando hay muchos pasos involucrados en la creación de un objeto.

Wikipedia dice
> El patrón Builder es un patrón de diseño de software de creación de objetos con la intención de encontrar una solución al anti-patrón del constructor telescópico.

Habiendo dicho eso, permítanme agregar un poco sobre qué es el anti-patrón del constructor telescópico. En un momento u otro, todos hemos visto un constructor como el siguiente:

```php
public function __construct($size, $cheese = true, $pepperoni = true, $tomato = false, $lettuce = true)
{
}
```

Como puedes ver; el número de parámetros del constructor puede irse rápidamente y puede ser difícil de entender la disposición de los parámetros. Además, esta lista de parámetros podría seguir creciendo si desea agregar más opciones en el futuro. Esto se llama anti-patrón del constructor telescópico.

**Código de ejemplo**

La mejor alternativa es usar el patrón Builder. Antes que nada tenemos nuestra hamburguesa que queremos hacer

```php
class Burger
{
    protected $size;

    protected $cheese = false;
    protected $pepperoni = false;
    protected $lettuce = false;
    protected $tomato = false;

    public function __construct(BurgerBuilder $builder)
    {
        $this->size = $builder->size;
        $this->cheese = $builder->cheese;
        $this->pepperoni = $builder->pepperoni;
        $this->lettuce = $builder->lettuce;
        $this->tomato = $builder->tomato;
    }
}
```

Y luego tenemos el constructor
```php
class BurgerBuilder
{
    public $size;

    public $cheese = false;
    public $pepperoni = false;
    public $lettuce = false;
    public $tomato = false;

    public function __construct(int $size)
    {
        $this->size = $size;
    }

    public function addPepperoni()
    {
        $this->pepperoni = true;
        return $this;
    }

    public function addLettuce()
    {
        $this->lettuce = true;
        return $this;
    }

    public function addCheese()
    {
        $this->cheese = true;
        return $this;
    }

    public function addTomato()
    {
        $this->tomato = true;
        return $this;
    }

    public function build(): Burger
    {
        return new Burger($this);
    }
}
```
And then it can be used as:

```php
$burger = (new BurgerBuilder(14))
                    ->addPepperoni()
                    ->addLettuce()
                    ->addTomato()
                    ->build();
```

**¿Cuándo usarlo?**

Cuando podría haber varios sabores de un objeto y evitar que el constructor sea demasiado grande. La diferencia clave del patrón Simple Factory es eso; El patrón Factory se debe usar cuando la creación es un proceso de un paso, mientras que el patrón de construcción se debe usar cuando la creación es un proceso de varios pasos.

🐑 Prototype
------------
Ejemplo del mundo real
> ¿Recuerdas Dolly? ¡La oveja que fue clonada! No entremos en los detalles, pero el punto clave aquí es que se trata de la clonación

De forma sencilla
> Crear objeto basado en un objeto existente mediante clonación.

Wikipedia dice
> El patrón Prototype es un patrón de diseño de creación en el desarrollo de software. Se usa cuando el tipo de objetos para crear está determinado por una instancia prototípica, que se clona para producir nuevos objetos.

En resumen, le permite crear una copia de un objeto existente y modificarlo según sus necesidades, en lugar de tomarse la molestia de crear un objeto desde cero y configurarlo.

**Código de ejemplo**

En PHP, se puede hacer fácilmente usando `clon`

```php
class Sheep
{
    protected $name;
    protected $category;

    public function __construct(string $name, string $category = 'Mountain Sheep')
    {
        $this->name = $name;
        $this->category = $category;
    }

    public function setName(string $name)
    {
        $this->name = $name;
    }

    public function getName()
    {
        return $this->name;
    }

    public function setCategory(string $category)
    {
        $this->category = $category;
    }

    public function getCategory()
    {
        return $this->category;
    }
}
```
Entonces puede ser clonado como abajo
```php
$original = new Sheep('Jolly');
echo $original->getName(); // Jolly
echo $original->getCategory(); // Mountain Sheep

// Clone and modify what is required
$cloned = clone $original;
$cloned->setName('Dolly');
echo $cloned->getName(); // Dolly
echo $cloned->getCategory(); // Mountain sheep
```

También podría usar el método mágico `__clone` para modificar el comportamiento de clonación.

**¿Cuándo usarlo?**

Cuando se requiere un objeto que es similar al objeto existente o cuando la creación sería costosa en comparación con la clonación.

💍 Singleton
------------
Ejemplo del mundo real
> Solo puede haber un presidente de un país a la vez. El mismo presidente tiene que ponerse en acción cada vez que el deber le llama. El presidente aquí es singleton.

De forma sencilla
> Asegura que solo se crea un objeto de una clase en particular.

Wikipedia dice
> En ingeniería de software, el patrón Singleton es un patrón de diseño de software que restringe la creación de instancias de una clase a un objeto. Esto es útil cuando se necesita exactamente un objeto para coordinar acciones en todo el sistema.

El patrón Singleton en realidad se considera un antipatrón y se debe evitar su uso excesivo. No es necesariamente malo y podría tener algunos casos de uso válidos, pero debe usarse con precaución porque introduce un estado global en su aplicación y cambiarlo en un lugar podría afectar en las otras áreas siendo bastante difícil de depurar. La otra cosa mala de ellos es que hace que tu código esté bien conectado y burlarse del Singleton podría ser difícil.

**Código de ejemplo**

Para crear un Singleton haga que el constructor sea privado, desactive la clonación, deshabilite la extensión y cree una variable estática para albergar la instancia
```php
final class President
{
    private static $instance;

    private function __construct()
    {
        // Hide the constructor
    }

    public static function getInstance(): President
    {
        if (!self::$instance) {
            self::$instance = new self();
        }

        return self::$instance;
    }

    private function __clone()
    {
        // Disable cloning
    }

    private function __wakeup()
    {
        // Disable unserialize
    }
}
```
Entonces para usar
```php
$president1 = President::getInstance();
$president2 = President::getInstance();

var_dump($president1 === $president2); // true
```

Patrones de Diseño de Estructura
================================
De forma sencilla
> Los patrones estructurales están principalmente relacionados con la composición de objetos o, en otras palabras, cómo las entidades pueden usarse entre sí. O bien, otra explicación sería, ayudan a responder "¿Cómo construir un componente de software?"

Wikipedia dice
> En ingeniería de software, los patrones de diseño estructural son patrones de diseño que facilitan el diseño al identificar una forma simple de realizar relaciones entre entidades.

 * [Adapter](#-adapter)
 * [Bridge](#-bridge)
 * [Composite](#-composite)
 * [Decorator](#-decorator)
 * [Facade](#-facade)
 * [Flyweight](#-flyweight)
 * [Proxy](#-proxy)

🔌 Adapter
----------
Ejemplo del mundo real
> Considere que tiene algunas imágenes en su tarjeta de memoria y que necesita transferirlas a su computadora. Para transferirlos necesita algún tipo de adaptador que sea compatible con los puertos de su computadora para que pueda conectar la tarjeta de memoria a su computadora. En este caso, el lector de tarjetas es un adaptador.
> Otro ejemplo sería el famoso adaptador de corriente; un enchufe de tres patas no se puede conectar a un toma de corriente de dos patillas, necesita usar un adaptador de corriente que lo haga compatible con el enchufe de dos patillas.
> Otro ejemplo más sería un traductor que traduce palabras que una persona habla a otra

De forma sencilla
> El patrón de adaptador le permite envolver un objeto incompatible en un adaptador para hacerlo compatible con otra clase.

Wikipedia dice
> En ingeniería de software, el patrón Adapter es un patrón de diseño de software que permite utilizar la interfaz de una clase existente como otra interfaz. A menudo se usa para hacer que las clases existentes funcionen con otras sin modificar su código fuente.

**Código de ejemplo**

Considera un juego donde hay un cazador al que le gusta cazar leones.

Primero tenemos una interfaz del tipo León donde caben todos los tipos de leones que se pueden implementar

```php
interface Lion
{
    public function roar();
}

class AfricanLion implements Lion
{
    public function roar()
    {
    }
}

class AsianLion implements Lion
{
    public function roar()
    {
    }
}
```
El cazador espera que cualquier implementación de la interfaz `Lion` pueda ser cazada.
```php
class Hunter
{
    public function hunt(Lion $lion)
    {
    }
}
```

Ahora digamos que tenemos que agregar un perro salvaje en nuestro juego para que el cazador pueda cazar eso también. Pero no podemos hacer eso directamente porque el perro tiene una interfaz diferente. Para que sea compatible con nuestro cazador, tendremos que crear un adaptador que sea compatible

```php
// This needs to be added to the game
class WildDog
{
    public function bark()
    {
    }
}

// Adapter around wild dog to make it compatible with our game
class WildDogAdapter implements Lion
{
    protected $dog;

    public function __construct(WildDog $dog)
    {
        $this->dog = $dog;
    }

    public function roar()
    {
        $this->dog->bark();
    }
}
```
Y ahora el perro salvaje se puede usar en nuestro juego usando `WildDogAdapter`.
```php
$wildDog = new WildDog();
$wildDogAdapter = new WildDogAdapter($wildDog);

$hunter = new Hunter();
$hunter->hunt($wildDogAdapter);
```

🚡 Bridge
---------
Ejemplo del mundo real
> Considere que tiene un sitio web con diferentes páginas y se supone que debe permitir que el usuario cambie el tema. ¿Qué harías? Cree copias múltiples de cada una de las páginas para cada tema o simplemente cree un tema separado y cárguelo según las preferencias del usuario. El patrón Bridge le permite implementar la segundoa opción, es decir

![With and without the bridge pattern](https://cloud.githubusercontent.com/assets/11269635/23065293/33b7aea0-f515-11e6-983f-98823c9845ee.png)

De forma sencilla
> El patrón de puente se trata de preferir la composición a la herencia. Los detalles de implementación se envían desde una jerarquía a otro objeto con una jerarquía separada.

Wikipedia dice
> El patrón de puente es un patrón de diseño utilizado en ingeniería de software que tiene por objeto "desacoplar una abstracción de su implementación para que los dos puedan variar de forma independiente"

**Código de ejemplo**

Traduciendo nuestro ejemplo de página web desde arriba. Aquí tenemos la jerarquía `WebPage`

```php
interface WebPage
{
    public function __construct(Theme $theme);
    public function getContent();
}

class About implements WebPage
{
    protected $theme;

    public function __construct(Theme $theme)
    {
        $this->theme = $theme;
    }

    public function getContent()
    {
        return "About page in " . $this->theme->getColor();
    }
}

class Careers implements WebPage
{
    protected $theme;

    public function __construct(Theme $theme)
    {
        $this->theme = $theme;
    }

    public function getContent()
    {
        return "Careers page in " . $this->theme->getColor();
    }
}
```
And the separate theme hierarchy
```php

interface Theme
{
    public function getColor();
}

class DarkTheme implements Theme
{
    public function getColor()
    {
        return 'Dark Black';
    }
}
class LightTheme implements Theme
{
    public function getColor()
    {
        return 'Off white';
    }
}
class AquaTheme implements Theme
{
    public function getColor()
    {
        return 'Light blue';
    }
}
```
And both the hierarchies
```php
$darkTheme = new DarkTheme();

$about = new About($darkTheme);
$careers = new Careers($darkTheme);

echo $about->getContent(); // "About page in Dark Black";
echo $careers->getContent(); // "Careers page in Dark Black";
```

🌿 Composite
-----------------

Ejemplo del mundo real
> Pensemos en una organización que está compuesta de empleados. Cada uno de los empleados tiene las mismas características, es decir, tiene un salario, tiene algunas responsabilidades, puede o no informar a alguien, puede o no tener subordinados, etc.

De forma sencilla
> El patrón Composite permite a los clientes tratar los objetos individuales de manera uniforme.

Wikipedia dice
> En ingeniería de software, el patrón Composite es un patrón de diseño de partición. El patrón Composite describe que un grupo de objetos se debe tratar de la misma manera que una instancia única de un objeto. La intención de un compuesto es "componer" objetos en estructuras de árbol para representar jerarquías de parte-todo. La implementación del patrón compuesto permite a los clientes tratar objetos individuales y composiciones de manera uniforme.
**Código de ejemplo**

Tomando el ejemplo de nuestros empleados de arriba. Aquí tenemos diferentes tipos de empleados
```php
interface Employee
{
    public function __construct(string $name, float $salary);
    public function getName(): string;
    public function setSalary(float $salary);
    public function getSalary(): float;
    public function getRoles(): array;
}

class Developer implements Employee
{
    protected $salary;
    protected $name;
    protected $roles;

    public function __construct(string $name, float $salary)
    {
        $this->name = $name;
        $this->salary = $salary;
    }

    public function getName(): string
    {
        return $this->name;
    }

    public function setSalary(float $salary)
    {
        $this->salary = $salary;
    }

    public function getSalary(): float
    {
        return $this->salary;
    }

    public function getRoles(): array
    {
        return $this->roles;
    }
}

class Designer implements Employee
{
    protected $salary;
    protected $name;
    protected $roles;

    public function __construct(string $name, float $salary)
    {
        $this->name = $name;
        $this->salary = $salary;
    }

    public function getName(): string
    {
        return $this->name;
    }

    public function setSalary(float $salary)
    {
        $this->salary = $salary;
    }

    public function getSalary(): float
    {
        return $this->salary;
    }

    public function getRoles(): array
    {
        return $this->roles;
    }
}
```

Entonces tenemos una organización que consiste en varios tipos diferentes de empleados

```php
class Organization
{
    protected $employees;

    public function addEmployee(Employee $employee)
    {
        $this->employees[] = $employee;
    }

    public function getNetSalaries(): float
    {
        $netSalary = 0;

        foreach ($this->employees as $employee) {
            $netSalary += $employee->getSalary();
        }

        return $netSalary;
    }
}
```

Y luego puede usarse como
```php
// Prepare the employees
$john = new Developer('John Doe', 12000);
$jane = new Designer('Jane Doe', 15000);

// Add them to organization
$organization = new Organization();
$organization->addEmployee($john);
$organization->addEmployee($jane);

echo "Net salaries: " . $organization->getNetSalaries(); // Net Salaries: 27000
```

☕ Decorator
-------------

Ejemplo del mundo real

> Imagine que tiene un taller de servicio de automóviles que ofrece múltiples servicios. Ahora, ¿cómo se calcula la factura que se cobrará? Usted elige un servicio y continúa agregando dinámicamente los precios de los servicios prestados hasta que obtiene el costo final. Aquí cada tipo de servicio es un decorador.

De forma sencilla
> El patrón Decorator le permite cambiar dinámicamente el comportamiento de un objeto en tiempo de ejecución envolviéndolo en un objeto de una clase de decorador.

Wikipedia dice
> En la programación orientada a objetos, el patrón de Decorator es un patrón de diseño que permite agregar un comportamiento a un objeto individual, ya sea estática o dinámicamente, sin afectar el comportamiento de otros objetos de la misma clase. El patrón decorador es a menudo útil para cumplir con el Principio de Responsabilidad Única, ya que permite que la funcionalidad se divida entre clases con áreas de preocupación únicas.

**Código de ejemplo**

Vamos a tomar café, por ejemplo. En primer lugar, tenemos un café simple que implementa la interfaz del café
```php
interface Coffee
{
    public function getCost();
    public function getDescription();
}

class SimpleCoffee implements Coffee
{
    public function getCost()
    {
        return 10;
    }

    public function getDescription()
    {
        return 'Simple coffee';
    }
}
```
Queremos que el código sea extensible para permitir que las opciones lo modifiquen si es necesario. Vamos a hacer algunos complementos (decoradores)
```php
class MilkCoffee implements Coffee
{
    protected $coffee;

    public function __construct(Coffee $coffee)
    {
        $this->coffee = $coffee;
    }

    public function getCost()
    {
        return $this->coffee->getCost() + 2;
    }

    public function getDescription()
    {
        return $this->coffee->getDescription() . ', milk';
    }
}

class WhipCoffee implements Coffee
{
    protected $coffee;

    public function __construct(Coffee $coffee)
    {
        $this->coffee = $coffee;
    }

    public function getCost()
    {
        return $this->coffee->getCost() + 5;
    }

    public function getDescription()
    {
        return $this->coffee->getDescription() . ', whip';
    }
}

class VanillaCoffee implements Coffee
{
    protected $coffee;

    public function __construct(Coffee $coffee)
    {
        $this->coffee = $coffee;
    }

    public function getCost()
    {
        return $this->coffee->getCost() + 3;
    }

    public function getDescription()
    {
        return $this->coffee->getDescription() . ', vanilla';
    }
}
```

Vamos a hacer un café ahora
```php
$someCoffee = new SimpleCoffee();
echo $someCoffee->getCost(); // 10
echo $someCoffee->getDescription(); // Simple Coffee

$someCoffee = new MilkCoffee($someCoffee);
echo $someCoffee->getCost(); // 12
echo $someCoffee->getDescription(); // Simple Coffee, milk

$someCoffee = new WhipCoffee($someCoffee);
echo $someCoffee->getCost(); // 17
echo $someCoffee->getDescription(); // Simple Coffee, milk, whip

$someCoffee = new VanillaCoffee($someCoffee);
echo $someCoffee->getCost(); // 20
echo $someCoffee->getDescription(); // Simple Coffee, milk, whip, vanilla
```

📦 Facade
----------------

Ejemplo del mundo real
> ¿Cómo enciendes la computadora? "¡Presiona el botón de encendido!" Eso es lo que crees porque estás usando una interfaz simple que la computadora proporciona en el exterior, internamente tiene que hacer muchas cosas para que suceda. Esta interfaz simple para el subsistema complejo es una fachada.

De forma sencilla
> El patrón Facade proporciona una interfaz simplificada para un subsistema complejo.

Wikipedia dice
> Una fachada es un objeto que proporciona una interfaz simplificada para un cuerpo de código más grande, como una biblioteca de clases.

**Código de ejemplo**

Tomando nuestro ejemplo de computadora desde arriba. Aquí tenemos la clase de computadora
```php
class Computer
{
    public function getElectricShock()
    {
        echo "Ouch!";
    }

    public function makeSound()
    {
        echo "Beep beep!";
    }

    public function showLoadingScreen()
    {
        echo "Loading..";
    }

    public function bam()
    {
        echo "Ready to be used!";
    }

    public function closeEverything()
    {
        echo "Bup bup bup buzzzz!";
    }

    public function sooth()
    {
        echo "Zzzzz";
    }

    public function pullCurrent()
    {
        echo "Haaah!";
    }
}
```
Aquí tenemos la fachada
```php
class ComputerFacade
{
    protected $computer;

    public function __construct(Computer $computer)
    {
        $this->computer = $computer;
    }

    public function turnOn()
    {
        $this->computer->getElectricShock();
        $this->computer->makeSound();
        $this->computer->showLoadingScreen();
        $this->computer->bam();
    }

    public function turnOff()
    {
        $this->computer->closeEverything();
        $this->computer->pullCurrent();
        $this->computer->sooth();
    }
}
```
Ahora para usar la fachada
```php
$computer = new ComputerFacade(new Computer());
$computer->turnOn(); // Ouch! Beep beep! Loading.. Ready to be used!
$computer->turnOff(); // Bup bup buzzz! Haah! Zzzzz
```

🍃 Flyweight
---------

Ejemplo del mundo real
> ¿Alguna vez tomaste té fresco de algún puesto? A menudo hacen más de una taza y guardan el resto para cualquier otro cliente para ahorrar los recursos, p. gas, etc. El patrón de peso mosca se trata de eso, es decir, compartir.

De forma sencilla
> Se usa para minimizar el uso de la memoria o los gastos computacionales al compartir tanto como sea posible con objetos similares.

Wikipedia dice
> En programación de computadoras, flyweight es un patrón de diseño de software. Un flyweight es un objeto que minimiza el uso de memoria al compartir tantos datos como sea posible con otros objetos similares; es una forma de usar objetos en grandes cantidades cuando una representación repetida simple usaría una cantidad inaceptable de memoria.

**Código de ejemplo**

Traduciendo nuestro ejemplo de té de arriba. Antes que nada, tenemos tipos de té y teteras

```php
// Anything that will be cached is flyweight.
// Types of tea here will be flyweights.
class KarakTea
{
}

// Acts as a factory and saves the tea
class TeaMaker
{
    protected $availableTea = [];

    public function make($preference)
    {
        if (empty($this->availableTea[$preference])) {
            $this->availableTea[$preference] = new KarakTea();
        }

        return $this->availableTea[$preference];
    }
}
```

Luego tenemos el `TeaShop` que toma pedidos y los sirve
```php
class TeaShop
{
    protected $orders;
    protected $teaMaker;

    public function __construct(TeaMaker $teaMaker)
    {
        $this->teaMaker = $teaMaker;
    }

    public function takeOrder(string $teaType, int $table)
    {
        $this->orders[$table] = $this->teaMaker->make($teaType);
    }

    public function serve()
    {
        foreach ($this->orders as $table => $tea) {
            echo "Serving tea to table# " . $table;
        }
    }
}
```
Y puede ser utilizado como abajo
```php
$teaMaker = new TeaMaker();
$shop = new TeaShop($teaMaker);

$shop->takeOrder('less sugar', 1);
$shop->takeOrder('more milk', 2);
$shop->takeOrder('without sugar', 5);

$shop->serve();
// Serving tea to table# 1
// Serving tea to table# 2
// Serving tea to table# 5
```

🎱 Proxy
-------------------
Ejemplo del mundo real
> ¿Alguna vez ha usado una tarjeta de acceso para pasar por una puerta? Hay varias opciones para abrir esa puerta, es decir, se puede abrir con una tarjeta de acceso o presionando un botón que pasa por alto la seguridad. La funcionalidad principal de la puerta es abrir pero hay un proxy agregado encima para agregar alguna funcionalidad. Permítanme explicarlo mejor usando el siguiente ejemplo de código.

De forma sencilla
> Usando el patrón de proxy, una clase representa la funcionalidad de otra clase.

Wikipedia dice
> Un proxy, en su forma más general, es una clase que funciona como una interfaz para otra cosa. Un proxy es un envoltorio o un objeto de agente que el cliente está llamando para acceder al objeto de servicio real detrás de las escenas. El uso del proxy simplemente puede reenviarse al objeto real, o puede proporcionar una lógica adicional. En el proxy, se puede proporcionar una funcionalidad adicional, por ejemplo, el almacenamiento en caché cuando las operaciones en el objeto real consumen muchos recursos, o la comprobación de precondiciones antes de invocar operaciones en el objeto real.

**Código de ejemplo**

Tomando nuestro ejemplo de puerta de seguridad desde arriba. En primer lugar tenemos la interfaz de la puerta y una implementación de la puerta
```php
interface Door
{
    public function open();
    public function close();
}

class LabDoor implements Door
{
    public function open()
    {
        echo "Opening lab door";
    }

    public function close()
    {
        echo "Closing the lab door";
    }
}
```
Entonces tenemos un proxy para asegurar las puertas que queremos
```php
class Security
{
    protected $door;

    public function __construct(Door $door)
    {
        $this->door = $door;
    }

    public function open($password)
    {
        if ($this->authenticate($password)) {
            $this->door->open();
        } else {
            echo "Big no! It ain't possible.";
        }
    }

    public function authenticate($password)
    {
        return $password === '$ecr@t';
    }

    public function close()
    {
        $this->door->close();
    }
}
```
Y así es como se puede usar
```php
$door = new Security(new LabDoor());
$door->open('invalid'); // Big no! It ain't possible.

$door->open('$ecr@t'); // Opening lab door
$door->close(); // Closing lab door
```
Otro ejemplo más sería algún tipo de implementación del mapeador de datos. Por ejemplo, recientemente hice un ODM (Object Data Mapper) para MongoDB usando este patrón donde escribí un proxy alrededor de las clases de mongo mientras utilizaba el método mágico `__call ()`. Todas las llamadas a métodos fueron transferidas a la clase mongo original y el resultado recuperado se devolvió tal cual, pero en el caso de los datos `find` o` findOne` se asignaron a los objetos de clase requeridos y se devolvió el objeto en lugar de `Cursor`.

Patrones de Diseño de Comportamiento
====================================

De forma sencilla
> Se ocupa de la asignación de responsabilidades entre los objetos. Lo que los hace diferentes de los patrones estructurales es que no solo especifican la estructura, sino que también delinean los patrones de transmisión / comunicación de mensajes entre ellos. O en otras palabras, ayudan a responder "¿Cómo ejecutar un comportamiento en el componente de software?"

Wikipedia dice
> En ingeniería de software, los patrones de diseño de comportamiento son patrones de diseño que identifican patrones de comunicación comunes entre objetos y realizan estos patrones. Al hacerlo, estos patrones aumentan la flexibilidad al llevar a cabo esta comunicación.

* [Chain of Responsibility](#-chain-of-responsibility)
* [Command](#-command)
* [Iterator](#-iterator)
* [Mediator](#-mediator)
* [Memento](#-memento)
* [Observer](#-observer)
* [Visitor](#-visitor)
* [Strategy](#-strategy)
* [State](#-state)
* [Template Method](#-template-method)

🔗 Chain of Responsibility
--------------------------

Ejemplo del mundo real
> Por ejemplo, tiene tres métodos de pago ("A", "B" y "C") configurados en su cuenta; cada uno tiene una cantidad diferente en él. `A` tiene 100 USD,` B` tiene 300 USD y `C` tiene 1000 USD y la preferencia de pago se elige como` A`, luego `B` y` C`. Intenta comprar algo que valga 210 USD. Usando Chain of Responsibility, antes que nada, la cuenta `A` se verificará si puede hacer la compra; si es así, se realizará la compra y la cadena se romperá. De lo contrario, la solicitud avanzará a la cuenta `B` que comprueba la cantidad si la cadena sí se romperá; de lo contrario, la solicitud seguirá reenviando hasta que encuentre el controlador adecuado. Aquí `A`,` B` y `C` son enlaces de la cadena y todo el fenómeno es Chain of Responsibility.

De forma sencilla
> Ayuda a construir una cadena de objetos. La solicitud ingresa desde un extremo y continúa yendo de un objeto a otro hasta encontrar el controlador adecuado.

Wikipedia dice
> En el diseño orientado a objetos, el patrón de cadena de responsabilidad es un patrón de diseño que consiste en una fuente de objetos de comando y una serie de objetos de procesamiento. Cada objeto de procesamiento contiene lógica que define los tipos de objetos de comando que puede manejar; el resto pasa al siguiente objeto de procesamiento en la cadena.

**Código de ejemplo**

Traduciendo nuestro ejemplo de cuenta arriba. En primer lugar, tenemos una cuenta base que tiene la lógica para encadenar las cuentas juntas y algunas cuentas
```php
abstract class Account
{
    protected $successor;
    protected $balance;

    public function setNext(Account $account)
    {
        $this->successor = $account;
    }

    public function pay(float $amountToPay)
    {
        if ($this->canPay($amountToPay)) {
            echo sprintf('Paid %s using %s' . PHP_EOL, $amountToPay, get_called_class());
        } elseif ($this->successor) {
            echo sprintf('Cannot pay using %s. Proceeding ..' . PHP_EOL, get_called_class());
            $this->successor->pay($amountToPay);
        } else {
            throw new Exception('None of the accounts have enough balance');
        }
    }

    public function canPay($amount): bool
    {
        return $this->balance >= $amount;
    }
}

class Bank extends Account
{
    protected $balance;

    public function __construct(float $balance)
    {
        $this->balance = $balance;
    }
}

class Paypal extends Account
{
    protected $balance;

    public function __construct(float $balance)
    {
        $this->balance = $balance;
    }
}

class Bitcoin extends Account
{
    protected $balance;

    public function __construct(float $balance)
    {
        $this->balance = $balance;
    }
}
```

Ahora preparemos la cadena utilizando los enlaces definidos anteriormente (es decir, Bank, Paypal, Bitcoin)
```php
// Let's prepare a chain like below
//      $bank->$paypal->$bitcoin
//
// First priority bank
//      If bank can't pay then paypal
//      If paypal can't pay then bit coin

$bank = new Bank(100);          // Bank with balance 100
$paypal = new Paypal(200);      // Paypal with balance 200
$bitcoin = new Bitcoin(300);    // Bitcoin with balance 300

$bank->setNext($paypal);
$paypal->setNext($bitcoin);

// Let's try to pay using the first priority i.e. bank
$bank->pay(259);

// Output will be
// ==============
// Cannot pay using bank. Proceeding ..
// Cannot pay using paypal. Proceeding ..:
// Paid 259 using Bitcoin!
```

👮 Command
----------

Ejemplo del mundo real
> Un ejemplo genérico sería ordenar comida en un restaurante. Usted (es decir, "Cliente") le pide al camarero (es decir, "Invitador") que traiga algo de comida (es decir, "Mando") y el mesero simplemente reenvía la solicitud al Chef (es decir, "Receptor") que sabe qué y cómo cocinar. .
> Otro ejemplo sería usted (es decir, 'Cliente') encendiendo (es decir, 'Command') el televisor (es decir, 'Receptor') utilizando un control remoto ('Invoker').

De forma sencilla
> Le permite encapsular acciones en objetos. La idea clave detrás de este patrón es proporcionar los medios para desacoplar al cliente del receptor.

Wikipedia dice
> En la programación orientada a objetos, el patrón de comando es un patrón de diseño de comportamiento en el cual un objeto se usa para encapsular toda la información necesaria para realizar una acción o desencadenar un evento en un momento posterior. Esta información incluye el nombre del método, el objeto que posee el método y los valores para los parámetros del método.

**Código de ejemplo**

En primer lugar, tenemos el receptor que tiene la implementación de cada acción que se puede realizar
```php
// Receiver
class Bulb
{
    public function turnOn()
    {
        echo "Bulb has been lit";
    }

    public function turnOff()
    {
        echo "Darkness!";
    }
}
```
entonces tenemos una interfaz que cada uno de los comandos va a implementar y luego tenemos un conjunto de comandos
```php
interface Command
{
    public function execute();
    public function undo();
    public function redo();
}

// Command
class TurnOn implements Command
{
    protected $bulb;

    public function __construct(Bulb $bulb)
    {
        $this->bulb = $bulb;
    }

    public function execute()
    {
        $this->bulb->turnOn();
    }

    public function undo()
    {
        $this->bulb->turnOff();
    }

    public function redo()
    {
        $this->execute();
    }
}

class TurnOff implements Command
{
    protected $bulb;

    public function __construct(Bulb $bulb)
    {
        $this->bulb = $bulb;
    }

    public function execute()
    {
        $this->bulb->turnOff();
    }

    public function undo()
    {
        $this->bulb->turnOn();
    }

    public function redo()
    {
        $this->execute();
    }
}
```
Luego tenemos un `Invoker` con quien el cliente interactuará para procesar cualquier comando
```php
// Invoker
class RemoteControl
{
    public function submit(Command $command)
    {
        $command->execute();
    }
}
```
Finally let's see how we can use it in our client
```php
$bulb = new Bulb();

$turnOn = new TurnOn($bulb);
$turnOff = new TurnOff($bulb);

$remote = new RemoteControl();
$remote->submit($turnOn); // Bulb has been lit!
$remote->submit($turnOff); // Darkness!
```

El patrón de comando también se puede usar para implementar un sistema basado en transacciones. Donde mantiene el historial de comandos tan pronto como los ejecuta. Si el comando final se ejecuta con éxito, todo lo bueno, de lo contrario, simplemente iterar a través del historial y seguir ejecutando el `deshacer 'en todos los comandos ejecutados.

➿ Iterator
-----------

Ejemplo del mundo real
> Un viejo conjunto de radio será un buen ejemplo de iterador, donde el usuario podría comenzar en algún canal y luego usar los botones siguiente o anterior para ir a través de los canales respectivos. O tome un ejemplo de reproductor de MP3 o un televisor donde puede presionar los botones siguiente y anterior para pasar por los canales consecutivos o, en otras palabras, todos ellos proporcionan una interfaz para iterar a través de los respectivos canales, canciones o estaciones de radio.

De forma sencilla
> Presenta una forma de acceder a los elementos de un objeto sin exponer la presentación subyacente.

Wikipedia dice
> En la programación orientada a objetos, el patrón de iterador es un patrón de diseño en el que se utiliza un iterador para atravesar un contenedor y acceder a los elementos del contenedor. El patrón de iterador desacopla los algoritmos de los contenedores; en algunos casos, los algoritmos son necesariamente específicos del contenedor y, por lo tanto, no se pueden desacoplar.

**Código de ejemplo**

En PHP, es bastante fácil de implementar utilizando SPL (Standard PHP Library). Traduciendo nuestro ejemplo de estaciones de radio desde arriba. Primero que nada tenemos `RadioStation`
```php
class RadioStation
{
    protected $frequency;

    public function __construct(float $frequency)
    {
        $this->frequency = $frequency;
    }

    public function getFrequency(): float
    {
        return $this->frequency;
    }
}
```
Entonces tenemos nuestro iterador

```php
use Countable;
use Iterator;

class StationList implements Countable, Iterator
{
    /** @var RadioStation[] $stations */
    protected $stations = [];

    /** @var int $counter */
    protected $counter;

    public function addStation(RadioStation $station)
    {
        $this->stations[] = $station;
    }

    public function removeStation(RadioStation $toRemove)
    {
        $toRemoveFrequency = $toRemove->getFrequency();
        $this->stations = array_filter($this->stations, function (RadioStation $station) use ($toRemoveFrequency) {
            return $station->getFrequency() !== $toRemoveFrequency;
        });
    }

    public function count(): int
    {
        return count($this->stations);
    }

    public function current(): RadioStation
    {
        return $this->stations[$this->counter];
    }

    public function key()
    {
        return $this->counter;
    }

    public function next()
    {
        $this->counter++;
    }

    public function rewind()
    {
        $this->counter = 0;
    }

    public function valid(): bool
    {
        return isset($this->stations[$this->counter]);
    }
}
```
Y luego puede usarse como
```php
$stationList = new StationList();

$stationList->addStation(new RadioStation(89));
$stationList->addStation(new RadioStation(101));
$stationList->addStation(new RadioStation(102));
$stationList->addStation(new RadioStation(103.2));

foreach($stationList as $station) {
    echo $station->getFrequency() . PHP_EOL;
}

$stationList->removeStation(new RadioStation(89)); // Will remove station 89
```

👽 Mediator
===========

Ejemplo del mundo real
> Un ejemplo general sería cuando habla con alguien en su teléfono móvil, hay un proveedor de red sentado entre usted y ellos y su conversación pasa por él en lugar de ser enviado directamente. En este caso, el proveedor de la red es mediador.

De forma sencilla
> El patrón del mediador agrega un objeto de terceros (llamado mediador) para controlar la interacción entre dos objetos (llamados colegas). Ayuda a reducir el acoplamiento entre las clases que se comunican entre sí. Porque ahora no necesitan tener el conocimiento de la implementación del otro.

Wikipedia dice
> En ingeniería de software, el patrón del mediador define un objeto que encapsula cómo interactúa un conjunto de objetos. Este patrón se considera un patrón de comportamiento debido a la forma en que puede alterar el comportamiento de ejecución del programa.

**Código de ejemplo**

Este es el ejemplo más simple de una sala de chat (es decir, un mediador) con usuarios (es decir, colegas) que se envían mensajes entre ellos.
Antes que nada, tenemos el mediador, es decir, la sala de chat

```php
interface ChatRoomMediator
{
    public function showMessage(User $user, string $message);
}

// Mediator
class ChatRoom implements ChatRoomMediator
{
    public function showMessage(User $user, string $message)
    {
        $time = date('M d, y H:i');
        $sender = $user->getName();

        echo $time . '[' . $sender . ']:' . $message;
    }
}
```

Entonces tenemos nuestros usuarios, es decir, colegas
```php
class User {
    protected $name;
    protected $chatMediator;

    public function __construct(string $name, ChatRoomMediator $chatMediator) {
        $this->name = $name;
        $this->chatMediator = $chatMediator;
    }

    public function getName() {
        return $this->name;
    }

    public function send($message) {
        $this->chatMediator->showMessage($this, $message);
    }
}
```
Y el uso
```php
$mediator = new ChatRoom();

$john = new User('John Doe', $mediator);
$jane = new User('Jane Doe', $mediator);

$john->send('Hi there!');
$jane->send('Hey!');

// Output will be
// Feb 14, 10:58 [John]: Hi there!
// Feb 14, 10:58 [Jane]: Hey!
```

💾 Memento
----------
Ejemplo del mundo real
> Tome el ejemplo de calculadora (es decir, originador), donde cada vez que realice algún cálculo, el último cálculo se guarda en la memoria (es decir, recuerdo) para poder volver y recuperarlo utilizando algunos botones de acción (es decir, cuidador).

De forma sencilla
> El patrón Memento se trata de capturar y almacenar el estado actual de un objeto de una manera que se puede restaurar más tarde de una manera suave.

Wikipedia dice
> El patrón de recuerdo es un patrón de diseño de software que proporciona la capacidad de restaurar un objeto a su estado anterior (deshacer por revertir).

Por lo general, es útil cuando necesita proporcionar algún tipo de funcionalidad de deshacer.

**Código de ejemplo**

Tomemos un ejemplo de editor de texto que guarda el estado de vez en cuando y que puede restaurar si lo desea.

En primer lugar, tenemos nuestro objeto de recuerdo que podrá mantener el estado del editor

```php
class EditorMemento
{
    protected $content;

    public function __construct(string $content)
    {
        $this->content = $content;
    }

    public function getContent()
    {
        return $this->content;
    }
}
```

Entonces tenemos nuestro editor, es decir, el autor que va a usar el objeto de recuerdo

```php
class Editor
{
    protected $content = '';

    public function type(string $words)
    {
        $this->content = $this->content . ' ' . $words;
    }

    public function getContent()
    {
        return $this->content;
    }

    public function save()
    {
        return new EditorMemento($this->content);
    }

    public function restore(EditorMemento $memento)
    {
        $this->content = $memento->getContent();
    }
}
```

Y luego puede usarse como
```php
$editor = new Editor();

// Type some stuff
$editor->type('This is the first sentence.');
$editor->type('This is second.');

// Save the state to restore to : This is the first sentence. This is second.
$saved = $editor->save();

// Type some more
$editor->type('And this is third.');

// Output: Content before Saving
echo $editor->getContent(); // This is the first sentence. This is second. And this is third.

// Restoring to last saved state
$editor->restore($saved);

$editor->getContent(); // This is the first sentence. This is second.
```

😎 Observer
--------
Ejemplo del mundo real
> Un buen ejemplo serían los buscadores de empleo donde se suscriben a algún sitio de publicación de trabajo y se les notifica cada vez que hay una oportunidad de trabajo coincidente.

De forma sencilla
> Define una dependencia entre objetos para que cada vez que un objeto cambie su estado, se notifiquen todos sus dependientes.

Wikipedia dice
> El patrón Observer es un patrón de diseño de software en el que un objeto, llamado sujeto, mantiene una lista de sus dependientes, llamados observadores, y los notifica automáticamente sobre cualquier cambio de estado, generalmente llamando a uno de sus métodos.

**Código de ejemplo**

Traduciendo nuestro ejemplo de arriba En primer lugar, tenemos personas que buscan trabajo y que necesitan ser notificadas para un puesto de trabajo
```php
class JobPost
{
    protected $title;

    public function __construct(string $title)
    {
        $this->title = $title;
    }

    public function getTitle()
    {
        return $this->title;
    }
}

class JobSeeker implements Observer
{
    protected $name;

    public function __construct(string $name)
    {
        $this->name = $name;
    }

    public function onJobPosted(JobPost $job)
    {
        // Do something with the job posting
        echo 'Hi ' . $this->name . '! New job posted: '. $job->getTitle();
    }
}
```
Luego tenemos nuestras ofertas de trabajo a las que los solicitantes de empleo se suscribirán
```php
class JobPostings implements Observable
{
    protected $observers = [];

    protected function notify(JobPost $jobPosting)
    {
        foreach ($this->observers as $observer) {
            $observer->onJobPosted($jobPosting);
        }
    }

    public function attach(Observer $observer)
    {
        $this->observers[] = $observer;
    }

    public function addJob(JobPost $jobPosting)
    {
        $this->notify($jobPosting);
    }
}
```
Entonces se puede usar como
```php
// Create subscribers
$johnDoe = new JobSeeker('John Doe');
$janeDoe = new JobSeeker('Jane Doe');

// Create publisher and attach subscribers
$jobPostings = new JobPostings();
$jobPostings->attach($johnDoe);
$jobPostings->attach($janeDoe);

// Add a new job and see if subscribers get notified
$jobPostings->addJob(new JobPost('Software Engineer'));

// Output
// Hi John Doe! New job posted: Software Engineer
// Hi Jane Doe! New job posted: Software Engineer
```

🏃 Visitor
----------
Ejemplo del mundo real
> Considere a alguien visitando Dubai. Solo necesitan una forma (es decir, una visa) para ingresar a Dubai. Después de su llegada, pueden venir y visitar cualquier lugar en Dubái por su cuenta sin tener que pedir permiso o hacer un trabajo de piernas para visitar cualquier lugar aquí; solo hágales saber de un lugar y pueden visitarlo. El patrón de visitante le permite hacer eso, le ayuda a agregar lugares para visitar, de modo que puedan visitar todo lo que puedan sin tener que hacer ningún trabajo preliminar.

De forma sencilla
> Patrón de visitante le permite agregar operaciones adicionales a los objetos sin tener que modificarlos.

Wikipedia dice
> En la programación orientada a objetos y la ingeniería de software, el patrón de diseño de visitante es una forma de separar un algoritmo de una estructura de objeto en la que opera. Un resultado práctico de esta separación es la capacidad de agregar nuevas operaciones a estructuras de objeto existentes sin modificar esas estructuras. Es una forma de seguir el principio abierto / cerrado.

**Código de ejemplo**

Tomemos el ejemplo de una simulación de zoológico donde tenemos diferentes tipos de animales y tenemos que hacerlos Sonar. Vamos a traducir esto usando un patrón de visitante

```php
// Visitee
interface Animal
{
    public function accept(AnimalOperation $operation);
}

// Visitor
interface AnimalOperation
{
    public function visitMonkey(Monkey $monkey);
    public function visitLion(Lion $lion);
    public function visitDolphin(Dolphin $dolphin);
}
```
Entonces tenemos nuestras implementaciones para los animales
```php
class Monkey implements Animal
{
    public function shout()
    {
        echo 'Ooh oo aa aa!';
    }

    public function accept(AnimalOperation $operation)
    {
        $operation->visitMonkey($this);
    }
}

class Lion implements Animal
{
    public function roar()
    {
        echo 'Roaaar!';
    }

    public function accept(AnimalOperation $operation)
    {
        $operation->visitLion($this);
    }
}

class Dolphin implements Animal
{
    public function speak()
    {
        echo 'Tuut tuttu tuutt!';
    }

    public function accept(AnimalOperation $operation)
    {
        $operation->visitDolphin($this);
    }
}
```
Implementemos a nuestro visitante
```php
class Speak implements AnimalOperation
{
    public function visitMonkey(Monkey $monkey)
    {
        $monkey->shout();
    }

    public function visitLion(Lion $lion)
    {
        $lion->roar();
    }

    public function visitDolphin(Dolphin $dolphin)
    {
        $dolphin->speak();
    }
}
```

Y luego puede usarse como
```php
$monkey = new Monkey();
$lion = new Lion();
$dolphin = new Dolphin();

$speak = new Speak();

$monkey->accept($speak);    // Ooh oo aa aa!    
$lion->accept($speak);      // Roaaar!
$dolphin->accept($speak);   // Tuut tutt tuutt!
```
Podríamos haber hecho esto simplemente al tener una jerarquía de herencia para los animales, pero luego tendríamos que modificar los animales cada vez que tuviéramos que agregar nuevas acciones a los animales. Pero ahora no tendremos que cambiarlos. Por ejemplo, digamos que se nos pide que agreguemos el comportamiento de salto a los animales, simplemente podemos agregarlo creando un nuevo visitante, es decir,

```php
class Jump implements AnimalOperation
{
    public function visitMonkey(Monkey $monkey)
    {
        echo 'Jumped 20 feet high! on to the tree!';
    }

    public function visitLion(Lion $lion)
    {
        echo 'Jumped 7 feet! Back on the ground!';
    }

    public function visitDolphin(Dolphin $dolphin)
    {
        echo 'Walked on water a little and disappeared';
    }
}
```
Y su uso es el siguiente
```php
$jump = new Jump();

$monkey->accept($speak);   // Ooh oo aa aa!
$monkey->accept($jump);    // Jumped 20 feet high! on to the tree!

$lion->accept($speak);     // Roaaar!
$lion->accept($jump);      // Jumped 7 feet! Back on the ground!

$dolphin->accept($speak);  // Tuut tutt tuutt!
$dolphin->accept($jump);   // Walked on water a little and disappeared
```

💡 Strategy
-----------

Ejemplo del mundo real
> Vamos a cosiderar el método de ordanación de la burbuja como ejemplo. Al implementar el ordenamiento de burbuja, los datos pueden comenzar a crecer y el ordenamiento se puede hacer muy lento. Para hacer frente a esto, implementamos la ordenación rápida. Pero ahora, aunque el algoritmo de ordenación rápida funcionaba mejor para grandes conjuntos de datos, es muy lento para conjuntos de datos más pequeños. Con el fin de manejar esto, implementamos una estrategia donde para conjuntos de datos pequeños, se usará sort de burbuja y para ordenación más grande y rápida.

De forma sencilla
> El patrón Strategy le permite cambiar el algoritmo o la estrategia según la situación.

Wikipedia dice
> En la programación de computadoras, el patrón Strategy (también conocido como patrón de política) es un patrón de diseño de software conductual que permite seleccionar el comportamiento de un algoritmo en tiempo de ejecución.

**Código de ejemplo**

Traduciendo nuestro ejemplo de arriba. En primer lugar, tenemos nuestra interfaz de estrategia y diferentes implementaciones de estrategia
```php
interface SortStrategy
{
    public function sort(array $dataset): array;
}

class BubbleSortStrategy implements SortStrategy
{
    public function sort(array $dataset): array
    {
        echo "Sorting using bubble sort";

        // Do sorting
        return $dataset;
    }
}

class QuickSortStrategy implements SortStrategy
{
    public function sort(array $dataset): array
    {
        echo "Sorting using quick sort";

        // Do sorting
        return $dataset;
    }
}
```

Y luego tenemos a nuestro cliente que va a usar cualquier estrategia
```php
class Sorter
{
    protected $sorter;

    public function __construct(SortStrategy $sorter)
    {
        $this->sorter = $sorter;
    }

    public function sort(array $dataset): array
    {
        return $this->sorter->sort($dataset);
    }
}
```
Y puede ser utilizado como
```php
$dataset = [1, 5, 4, 3, 2, 8];

$sorter = new Sorter(new BubbleSortStrategy());
$sorter->sort($dataset); // Output : Sorting using bubble sort

$sorter = new Sorter(new QuickSortStrategy());
$sorter->sort($dataset); // Output : Sorting using quick sort
```

💢 State
--------
Ejemplo del mundo real
> Imagina que estás usando alguna aplicación de dibujo, escoges el pincel para dibujar. Ahora el pincel cambia su comportamiento en función del color seleccionado, es decir, si ha elegido el color rojo, dibujará en rojo, si es azul, en azul, etc.

De forma sencilla
> Te permite cambiar el comportamiento de una clase cuando el estado cambia.
Wikipedia dice
> El patrón State es un patrón de diseño de software de comportamiento que implementa una máquina de estado de una manera orientada a objetos. Con el patrón de estado, se implementa una máquina de estado implementando cada estado individual como una clase derivada de la interfaz de patrón de estado e implementando transiciones de estado invocando métodos definidos por la superclase del patrón.
> El patrón State se puede interpretar como un patrón de estrategia que puede cambiar la estrategia actual a través de invocaciones de métodos definidos en la interfaz del patrón.

**Código de ejemplo**

Tomemos un ejemplo de editor de texto, le permite cambiar el estado del texto que se escribe, es decir, si ha seleccionado negrita, comienza a escribir en negrita, en cursiva, en cursiva, etc.
En primer lugar, tenemos nuestra interfaz de estado y algunas implementaciones de estado
```php
interface WritingState
{
    public function write(string $words);
}

class UpperCase implements WritingState
{
    public function write(string $words)
    {
        echo strtoupper($words);
    }
}

class LowerCase implements WritingState
{
    public function write(string $words)
    {
        echo strtolower($words);
    }
}

class Default implements WritingState
{
    public function write(string $words)
    {
        echo $words;
    }
}
```
Entonces tenemos nuestro editor
```php
class TextEditor
{
    protected $state;

    public function __construct(WritingState $state)
    {
        $this->state = $state;
    }

    public function setState(WritingState $state)
    {
        $this->state = $state;
    }

    public function type(string $words)
    {
        $this->state->write($words);
    }
}
```
Y se puede utilizar de la siguiente forma
```php
$editor = new TextEditor(new Default());

$editor->type('First line');

$editor->setState(new UpperCase());

$editor->type('Second line');
$editor->type('Third line');

$editor->setState(new LowerCase());

$editor->type('Fourth line');
$editor->type('Fifth line');

// Output:
// First line
// SECOND LINE
// THIRD LINE
// fourth line
// fifth line
```

📒 Template Method
------------------

Ejemplo del mundo real
> Supongamos que estamos construyendo una casa. Los pasos para construir podrían parecerse
> - Preparar la base de la casa
> - Construye las paredes
> - Agregar techo
> - Agregar otros pisos

> El orden de estos pasos nunca se puede cambiar, es decir, no se puede construir el techo antes de construir las paredes, etc., pero cada uno de los pasos se puede modificar, por ejemplo, las paredes pueden ser de madera o poliéster o piedra.

De forma sencilla
> El método de plantilla define el esqueleto de cómo se podría realizar cierto algoritmo, pero difiere la implementación de esos pasos a las clases de niños.

Wikipedia dice
> En ingeniería de software, el patrón Template Method es un patrón de diseño de comportamiento que define el esqueleto del programa de un algoritmo en una operación, difiriendo algunos pasos a subclases. Le permite a uno redefinir ciertos pasos de un algoritmo sin cambiar la estructura del algoritmo.

**Código de ejemplo**

Imagine que tenemos una herramienta de compilación que nos ayuda a probar, crear pelusas, generar, generar informes de compilación (es decir, informes de cobertura de código, informe de borrado, etc.) y desplegar nuestra aplicación en el servidor de prueba.

En primer lugar, tenemos nuestra clase base que especifica el esqueleto para el algoritmo de compilación
```php
abstract class Builder
{

    // Template method
    final public function build()
    {
        $this->test();
        $this->lint();
        $this->assemble();
        $this->deploy();
    }

    abstract public function test();
    abstract public function lint();
    abstract public function assemble();
    abstract public function deploy();
}
```

Entonces podemos tener nuestras implementaciones
```php
class AndroidBuilder extends Builder
{
    public function test()
    {
        echo 'Running android tests';
    }

    public function lint()
    {
        echo 'Linting the android code';
    }

    public function assemble()
    {
        echo 'Assembling the android build';
    }

    public function deploy()
    {
        echo 'Deploying android build to server';
    }
}

class IosBuilder extends Builder
{
    public function test()
    {
        echo 'Running ios tests';
    }

    public function lint()
    {
        echo 'Linting the ios code';
    }

    public function assemble()
    {
        echo 'Assembling the ios build';
    }

    public function deploy()
    {
        echo 'Deploying ios build to server';
    }
}
```
Y se puede utilizar de la siguiente forma
```php
$androidBuilder = new AndroidBuilder();
$androidBuilder->build();

// Output:
// Running android tests
// Linting the android code
// Assembling the android build
// Deploying android build to server

$iosBuilder = new IosBuilder();
$iosBuilder->build();

// Output:
// Running ios tests
// Linting the ios code
// Assembling the ios build
// Deploying ios build to server
```

## 🚦 Conclusiones

Esto ha sido todo. Continuaré mejorando, por lo que es posible que desee marcar este repositorio para volver a visitar. Además, tengo planes para escribir lo mismo sobre los patrones arquitectónicos, estad atentos.

## 👬 Contribution

- Informar problemas
- Solicitud de extracción abierta con mejoras
- Difundir la palabra
- Póngase en contacto con cualquier comentario [![Twitter URL](https://img.shields.io/twitter/url/https/twitter.com/kamranahmedse.svg?style=social&label=Follow%20%40kamranahmedse)](https://twitter.com/kamranahmedse)

## License

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
