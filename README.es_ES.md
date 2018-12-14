# Convenciones de estilo NUU: Lenguaje de Programación Elixir

## Contenido

- [**Idiomas**](#idiomas)
- [**Guía**](#guía)
  - [Disposición del Código Fuente (Layout)](#disposición-del-código-fuente-layout)
  - [Sintaxis](#sintaxis)
  - [Nombrado](#nombrado)
  - [Comentarios](#comentarios)
    - [Comentarios de Anotación](#comentarios-de-anotación)
  - [Módulos](#módulos)
  - [Funciones](#funciones)
  - [Documentación](#documentación)
  - [Typespecs](#typespecs)
  - [Structs](#structs)
  - [Excepciones](#excepciones)
  - [Strings](#strings)
  - [Pruebas](#pruebas)
- [**Derechos**](#derechos)
  - [Licencia](#licencia)
  - [Atribución](#atribución)

## Idiomas

- [English](README.md)
- [Español](README.es_ES.md)

## Guía

Guía para la codificación homogenea en proyectos que requieran el uso del lenguaje de programación Elixir.

### Disposición del Código Fuente (Layout)

1. Evita lineas de más de 80 caracteres.

1. Evita los espacios y tabulaciones al final de línea (*trailing whitespace*).

1. No utilices punto y coma al término de cada linea, utiliza simplemente el final de linea.

    ```elixir {.line-numbers}
    # No recomendado
    some_function();

    # Recomendado
    some_function()
    ```

1. Usa los finales de línea de Unix `\n` (Los sistemas operativos derivados de Unix ya están cubiertos por defecto, los usuarios de Windows tendrán que prestar atención en que los saltos de línea no sean `\n\r`).

    Si usas Git puede que quieras utilizar la siguiente configuración para protegerte de que se te cuelen los finales de línea de Windows:

    ```bash
    git config --global core.autocrlf
    ```

1. Usa dos espacios por nivel de indentación. No utilices tabulaciones.

    ```elixir {.line-numbers}
    # No recomendado (4 espacios)
    def some_function do
        do_something()
    end

    # Recomendado (2 espacios)
    def some_function do
      do_something()
    end
    ```

1. Usa espacios después de comas, dos puntos y alrededor de operadores. Exceptuando operadores unarios, el operador punto y el operador rango (punto-punto). No coloques espacios alrededor de parejas como llaves, paréntesis, etc. Los espacios son (en la mayoría de casos) irrelevantes para Elixir en tiempo de ejecución, pero su uso apropiado es clave para escribir código fácilmente legible.

    | Regla de estilo | Operadores |
    | :--- | :--- |
    | Sin espacios | `@` `.` `+` `-` `!` `^` `&` `..` `~~~` `()` `[]` `{}` |
    | Espacio después | `,` `:` `not` |
    | Espacios alrededor | `*` `/` `+` `-` `++` `--` `<>` `in` `not in` `\|>` `<<<` `>>>` `~>>` `<<~` `~>` `<~` `<~>` `<\|>` `<` `>` `<=` `>=` `==` `!=` `=~` `===` `!==` `&&` `&&&` `and` `\|\|` `\|\|\|` `or` `=` `=>` `\|\|` `::` `when` `<-` `\\` |

    Nota: Los operadores `+` `-` están sobrecargados, y existen con aridad 1 y 2. Cuando se utilizan el operador unario, no se usan espacios. Cuando se utiliza el operador binario, se colocan espacios al rededor de este.

    ```elixir {.line-numbers}
    # No recomendado
    result=2*5
    list =[ 1,<< 2 >> ,3 ]++[4 ,5 ]
    some_string|>String.trim()|>String.split( "-" )
    for num<-list,do:num||0
    def other_function( args,options\\[  ] ),do:some_function( )
    num = - 2
    inverse= ! bool
    Enum . map( list, some_function( ) )
    range = 1 .. 10

    # Recomendado
    result = 2 * 5
    list = [1, <<2>>, 3] ++ [4, 5]
    some_string |> String.trim() |> String.split("-")
    for num <- list, do: num || 0
    def other_function(args, options \\ []), do: some_function()
    num = -2
    inverse = !bool
    Enum.map(list, some_function())
    range = 1..10
    ```

1. Utiliza líneas en blanco entre estructuras `def` para separar las funciones en párrafos lógicos.

    ```elixir {.line-numbers}
    # No recomendado
    def some_function(some_data) do
      altered_data = Module.function(data)
    end
    def some_function do
      result
    end
    def some_other_function do
      another_result
    end

    # Recomendado
    def some_function(some_data) do
      altered_data = Module.function(data)
    end

    def some_function do
      result
    end

    def some_other_function do
      another_result
    end
    ```

1. No separes estructuras `def` que sean de una sola línea y que pertenezcan a la misma función. Pero si se trata de un `def` multilinea sí sepáralo con una línea en blanco.

    ```elixir {.line-numbers}
    # No recomendado
    def some_function(nil), do: {:error, "No Value"}

    def some_function([]), do: :ok

    def some_function([first | rest]), do: some_function(rest)

    # Recomendado
    def some_function(nil), do: {:error, "No Value"}
    def some_function([]), do: :ok
    def some_function([first | rest]), do: some_function(rest)

    # Separación para el def multilínea.
    def some_function(nil), do: {:error, "No Value"}
    def some_function([]), do: :ok

    def some_function([first | rest]) do
      some_function(rest)
    end
    ```

1. Si utilizas la sintaxis `do:` en funciones y la línea que constituye el cuerpo de la función es demasiado larga, coloca el `do:` en una nueva línea con un nivel de indentación más que la línea anterior.

    ```elixir {.line-numbers}
    # Recomendado
    def some_function([:foo, :bar, :baz] = args),
      do: Enum.map(args, fn arg -> arg <> " is on a very long line!" end)
    ```

    Cuando la clausula `do:` se encuentra en su propia linea, se le considera un `def` multilinea y se separa con una linea en blanco.

    ```elixir {.line-numbers}
    # No recomendado
    def some_function([]), do: :empty
    def some_function(_),
      do: :very_long_line_here

    # Recomendado
    def some_function([]), do: :empty

    def some_function(_),
      do: :very_long_line_here
    ```

1. Si tienes dos o más estructuras `def` multilínea pertenecientes de una misma función, no utilices estructuras `def` de una sola línea en estas.

    ```elixir {.line-numbers}
    # No recomendado
    def some_function(nil), do: {:error, "No Value"}
    def some_function([]), do: :ok

    def some_function([first | rest]) do
      some_function(rest)
    end

    def some_function([first | rest], opts) do
      some_function(rest, opts)
    end

    # Recomendado
    def some_function(nil) do
      {:error, "No Value"}
    end

    def some_function([]) do
      :ok
    end

    def some_function([first | rest]) do
      some_function(rest)
    end

    def some_function([first | rest], opts) do
      some_function(rest, opts)
    end
    ```

1. Usa el operador *pipe* `|>` para encadenar funciones una tras otra.

    ```elixir {.line-numbers}
    # No recomendado
    String.strip(String.downcase(some_string))

    # Recomendado
    some_string |> String.downcase() |> String.strip()

    # Los pipelines multilínea no se indentan.
    some_string
    |> String.downcase()
    |> String.strip()

    # Un pipeline multilínea que esté en el lado derecho de un pattern match
    # debe ser indentado en una nueva línea.
    sanitized_string =
      some_string
      |> String.downcase()
      |> String.strip()
    ```

    Aunque este sea el método recomendado, ten en cuenta que al copiar y pegar *pipelines* multilínea en IEx podría causar un error de sintaxis, ya que IEx evaluará la primera línea sin darse cuenta de que la siguiente línea tiene otro *pipeline*.

1. Utiliza expresiones simples (*bare expresions*) como comienzo de una cadena de funciones.

    ```elixir {.line-numbers}
    # ¡NUNCA HAGAS ESTO!
    # Esto en realidad se interpreta como:
    # String.strip("nope" |> String.downcase())
    String.trim "nope" |> String.downcase()

    # No recomendado
    String.trim(some_string) |> String.downcase() |> String.codepoints()

    # Recomendado
    some_string |> String.trim() |> String.downcase() |> String.codepoints()
    ```

1. Evita utilizar el operador *pipe* `|>` una única vez.

    ```elixir {.line-numbers}
    # No recomendado
    some_string |> String.downcase()

    # Recomendado
    String.downcase(some_string)
    ```

1. Coloca parentesis en funciones unarias cuando utilizes el operador *pipe* `|>`.

    ```elixir {.line-numbers}
    # No recomendado
    some_string |> String.downcase |> String.strip

    # Recomendado
    some_string |> String.downcase() |> String.strip()
    ```

1. No utilices funciones anónimas en una cadena de funciones.

    ```elixir {.line-numbers}
    # No recomendado
    some_string
    |> String.trim()
    |> String.downcase()
    |> (fn string -> string <> "_OK" end).()
    |> String.split("-")

    # Recomendado
    some_string
    |> String.trim()
    |> String.downcase()
    |> review()
    |> String.split("-")

    def review(string), do: string <> "_OK"
    ```

1. Cuando las claúsulas de las estructuras `case` o `cond` requieran varias líneas, separa cada claúsula con una linea en blanco.

    ```elixir {.line-numbers}
    # No recomendado
    case arg do
      true ->
        :ok
      false ->
        :error
    end

    # Recomendado
    case arg do
      true ->
        :ok

      false ->
        :error
    end
    ```

1. Si un *list*, *tuple*, *map* o *struct* requiere varias líneas al declararse, se deberá colocar cada elemento y los delimitadores en una línea própia. Indenta un nivel cada elemento, pero no los delimitadores.

    ```elixir {.line-numbers}
    # No recomendado
    [:first_item, :second_item, :next_item,
    :final_item]

    # Recomendado
    [
      :first_item,
      :second_item,
      :next_item,
      :final_item
    ]
    ```

1. Cuando asignes un *list*, *tuple*, *map* o *struct*, manten el delimitador de apertura en la misma linea de asignación.

    ```elixir {.line-numbers}
    # No recomendado
    list =
    [
      :first_item,
      :second_item
    ]

    # Recomendado
    list = [
      :first_item,
      :second_item
    ]
    ```

1. Después de una asignación multilínea añade una línea en blanco como separación.

    ```elixir {.line-numbers}
    # No recomendado
    some_string =
      "Hello"
      |> String.downcase()
      |> String.strip()
    another_string <> some_string

    # Recomendado
    some_string =
      "Hello"
      |> String.downcase()
      |> String.strip()

    another_string <> some_string
    ```

1. Agrega un guión bajo para separar en millares las literales decimales de seis dígitos o más.

    ```elixir {.line-numbers}
    # No recomendado
    num = 1000000
    num = 1_500

    # Recomendado
    num = 1_000_000
    num = 1500
    ```

1. Utiliza letras mayúsculas cuando uses literales hexadecimales.

    ```elixir {.line-numbers}
    # No recomendado
    <<0xef, 0xbb, 0xbf>>

    # Recomendado
    <<0xEF, 0xBB, 0xBF>>
    ```

1. Termina cada fichero con una nueva línea.

### Sintaxis

1. Usa paréntesis al declarar una estructura `def` con uno o más argumentos, y omítelos cuando la estructura no reciba argumentos.

    ```elixir {.line-numbers}
    # No recomendado
    def some_function arg1, arg2 do
      ...
    end

    def some_function() do
      ...
    end

    # Recomendado
    def some_function(arg1, arg2) do
      ...
    end

    def some_function do
      ...
    end
    ```

1. Nunca dejes un espacio entre el nombre de la función y el paréntesis de apertura.

    ```elixir {.line-numbers}
    # No recomendado
    f (3 + 2)

    # Recomendado
    f(3 + 2)
    ```

1. Utiliza siempre paréntesis en las llamadas a funciones, sobretodo dentro de un *pipeline* y en funciones que no reciban argumentos de tal forma que puedan ser distinguidas de las variables. A partir de Elixir 1.4, el compilador te avisará de los lugares en los que exista ambigüedad.

    ```elixir {.line-numbers}
    # No recomendado
    f 3

    Enum.reduce 1..100, 0, fn x, acc ->
      x + acc
    end

    # Esto en realidad se interpreta como: rem(2, (3 |> g))
    2 |> rem 3 |> g

    def my_func do
      # ¿Esto es una variable o una llamada a función?
      do_stuff
    end

    # Recomendado
    f(3)

    Enum.reduce(1..100, 0, fn(x, acc) ->
      x + acc
    end)

    2 |> rem(3) |> g

    def my_func do
      # Esto es claramente una llamada a función.
      do_stuff()
    end
    ```

1. Para los macros vemos un comportamiento contrario.

    ```elixir {.line-numbers}
    # No recomendado
    if( valid?(username) ) do
      ...
    end

    defmodule( MyApp.Service.TwitterAPI ) do
      use MyApp.Service, social: true

      alias MyApp.Service.Helper, as: H
    end

    # Recomendado
    if valid?(username) do
      ...
    end

    defmodule MyApp.Service.TwitterAPI do
      use MyApp.Service, social: true

      alias MyApp.Service.Helper, as: H
    end
    ```

1. Utiliza la sintaxis `do:` cuando el contenido de un macro pueda establecerse de una sola línea.

    ```elixir {.line-numbers}
    # Recomendado
    if some_condition, do: # some_stuff
    ```

1. Si los argumentos de un macro vuelven la linea demasiado larga, indenta y alinea los argumentos sucesivos. Coloca el `do:` en una nueva linea con un nivel de indentación.

    ```elixir {.line-numbers}
    # Recomendado
    with {:ok, foo} <- Keyword.fetch(opts, :foo),
         {:ok, bar} <- Keyword.fetch(opts, :bar),
      do: {:ok, foo, bar}
    ```

1. Si el macro tiene un bloque `do` con más de una línea, o tiene una opción `else`, utiliza la sintaxis multilínea.

    ```elixir {.line-numbers}
    # Recomendado
    with {:ok, foo} <- Keyword.fetch(opts, :foo),
         {:ok, bar} <- Keyword.fetch(opts, :bar) do
      {:ok, foo, bar}
    else
      :error ->
        {:error, :bad_arg}
    end
    ```

1. Nunca utilices `unless` con `else`. Reescríbelo poniendo el caso positivo primero con una sentencia `if`.

    ```elixir {.line-numbers}
    # No recomendado
    unless success do
      IO.puts('failure')
    else
      IO.puts('success')
    end

    # Recomendado
    if success do
      IO.puts('success')
    else
      IO.puts('failure')
    end
    ```

1. Omite la opción `else` en una sentencia  `if` o `unless` si este regresa **nil**.

    ```elixir {.line-numbers}
    # No recomendado
    if byte_size(data) > 0, do: data, else: nil

    # Recomendado
    if byte_size(data) > 0, do: data
    ```

1. Utiliza **true** como la última condición de `cond` cuando necesites una cláusula por defecto.

    ```elixir {.line-numbers}
    # No recomendado
    cond do
      1 + 2 == 5 ->
        "Nope"

      1 + 3 == 5 ->
        "Uh, uh"

      :else ->
        "OK"
    end

    # Recomendado
    cond do
      1 + 2 == 5 ->
        "Nope"

      1 + 3 == 5 ->
        "Uh, uh"

      true ->
        "OK"
    end
    ```

1. Utiliza `or`, `and` y `not` para comparaciones extrictamente booleanas. Utiliza `||`, `&&`, y `!` cuando alguno de los argumentos sea no-booleano.

    ```elixir {.line-numbers}
    # No recomendado
    is_atom(name) && name != nil
    is_binary(task) || is_atom(task)

    # Recomendado
    is_atom(name) and name != nil
    is_binary(task) or is_atom(task)
    line && line != 0
    file || "sample.exs"
    ```

1. Utiliza siempre la sintaxis especial para listas de *keywords*.

    ```elixir {.line-numbers}
    # No recomendado
    some_value = [{:a, "baz"}, {:b, "qux"}]

    # Recomendado
    some_value = [a: "baz", b: "qux"]
    ```

1. Omite los delimitadores de las listas de *keywords* siempre que sean opcionales.

    ```elixir {.line-numbers}
    # No recomendado
    some_function(foo, bar, [a: "baz", b: "qux"])

    # Recomendado
    some_function(foo, bar, a: "baz", b: "qux")
    ```

### Nombrado

1. Utiliza *snake case* para nombrar directorios y archivos, por ejemplo: `lib/my_app/task_server.ex`.

1. Utiliza *upper camel case* para módulos (mantén los acrónimos como HTTP, RFC, XML en mayúsculas).

    ```elixir {.line-numbers}
    # No recomendado
    defmodule Somemodule do
      ...
    end

    defmodule Some_Module do
      ...
    end

    defmodule SomeXml do
      ...
    end

    # Recomendado
    defmodule SomeModule do
      ...
    end

    defmodule SomeXML do
      ...
    end
    ```

1. Usa *snake case* para *atoms*, funciones y variables.

    ```elixir {.line-numbers}
    # No recomendado
    :"some atom"
    :SomeAtom
    :someAtom

    someVar = 5

    def someFunction do
      ...
    end

    # Recomendado
    :some_atom

    some_var = 5

    def some_function do
      ...
    end
    ```

1. Los nombres de macros (funciones generadas en tiempo de compilación que devuelven un valor booleano) que puedan ser utilizadas dentro de *guards* deberían nombrarse con el prefijo "is_".

    ```elixir {.line-numbers}
    # Recomendado
    defmacro is_cool(var) do
      quote do: unquote(var) == "cool"
    end
    ```

1. Los nombres de las funciones que regresen un valor booleano deberán de terminar en signo de interrogación "?" en lugar de tener un prefijo "is_".

    ```elixir {.line-numbers}
    # Recomendado
    def cool?(var) do
      ...
    end
    ```

1. Las funciones privadas que compartan el mismo nombre con alguna función pública deben tener el prefijo "do_".

    ```elixir {.line-numbers}
    # Recomendado
    def sum(list), do: do_sum(list, 0)

    defp do_sum([], total), do: total
    defp do_sum([head | tail], total), do: do_sum(tail, head + total)
    ```

### Comentarios

1. Escribe código expresivo e intenta transmitir la intención de tu programa a través de flujos de control, estructura y nombrado.

1. Los comentarios se escriben en la línea inmediatamente superior al código que anotan.

    ```elixir {.line-numbers}
    String.first(some_string) # No recomendado

    # Recomendado
    String.first(some_string)
    ```

1. Utiliza un espacio entre el carácter introductorio del comentario "#" y el resto del texto del comentario.

    ```elixir {.line-numbers}
    #No recomendado
    String.first(some_string)

    # Recomendado
    String.first(some_string)
    ```

1. Los comentarios que sean más largos de una palabra se escribirán capitalizados, y las frases utilizarán signos de puntuación. Usa un espacio tras cada punto.

    ```elixir {.line-numbers}
    # No recomendado
    # estos comentarios no inician en mayúsculas y falta puntuación

    # Recomendado
    # Comentario con puntuación correctamente utilizada.
    ```

#### Comentarios de Anotación

1. La palabra clave para la anotación estará completamente en mayúsculas, seguida de dos puntos y un espacio, a continuación se añade la nota que describe el problema.

    ```elixir {.line-numbers}
    # Recomendado
    # TODO: Deprecate in v1.5.
    def some_function(arg), do: {:ok, arg}
    ```

1. En casos en los que el problema sea tan obvio que cualquier tipo de documentación resulte redundante, puedes poner las anotaciones sin ninguna  nota. Este uso debería de ser la excepción y no la norma.

    ```elixir {.line-numbers}
    start_task()

    # FIXME
    Process.sleep(5000)
    ```

1. Utiliza `TODO` para anotar código que falte o funcionalidades que deberán ser añadidas posteriormente.

1. Utiliza `FIXME` para denotar código que debe ser arreglado.

1. Utiliza `OPTIMIZE` para denotar código lento o ineficiente que pudiese llegar a causar problemas de rendimiento.

1. Utiliza `HACK` para denotar *code smells* en los que se hayan empleado prácticas de programación cuestionables y que deban ser refactorizados.

1. Utiliza `REVIEW` para denotar cualquier cosa que deba ser revisada para confirmar que funciona como se espera.

    ```elixir {.line-numbers}
    # REVIEW: Estamos seguros que es así como el cliente hace X actualmente?
    ```

1. Utiliza claves de anotación propias si lo consideras oportuno, pero asegúrate de documentarlas en el fichero README de tu proyecto o similar.

### Módulos

1. Utiliza un archivo por módulo a no ser que el módulo sea utilizado únicamente de manera interna por otro módulo (como en el caso de una prueba).

1. Utiliza *snake case* para el nombre del fichero y *upper camel case* para el nombre del módulo.

    ```elixir {.line-numbers}
    # Recomendado
    # nombre de archivo: some_module.ex

    defmodule SomeModule do
    end
    ```

1. Representa cada nivel de anidación dentro del módulo como un directorio.

    ```elixir {.line-numbers}
    # Recomendado
    # nombre de archivo: parser/core/xml_parser.ex

    defmodule Parser.Core.XMLParser do
    end
    ```

1. No dejes una línea en blanco después de `defmodule`.

    ```elixir {.line-numbers}
    # No recomendado
    defmodule System.Accounts do

      import Ecto.Query, warn: false
      ...
    end

    # Recomendado
    defmodule System.Accounts do
      import Ecto.Query, warn: false
      ...
    end
    ```

1. Deja una línea en blanco después de cada bloque de código a nivel de módulo.

1. Lista los atributos y directivas del módulo en el siguiente orden:

    1. `@moduledoc`
    1. `@behaviour`
    1. `use`
    1. `import`
    1. `alias`
    1. `require`
    1. `defstruct`
    1. `@type`
    1. `@module_attribute`
    1. `@callback`
    1. `@macrocallback`
    1. `@optional_callbacks`

1. Añade una línea en blanco entre cada grupo, y ordena alfabéticamente los términos (como nombres de módulo). Aquí tienes un ejemplo general de cómo deberías ordenar el código en tus módulos:

    ```elixir {.line-numbers}
    # Recomendado
    defmodule MyModule do
      @moduledoc """
      An example module
      """

      @behaviour MyBehaviour

      use GenServer

      import Something
      import SomethingElse

      alias My.Long.Module.Name
      alias My.Other.Module.Example

      require Integer

      defstruct name: nil, params: []

      @type params :: [{binary, binary}]

      @module_attribute :foo
      @other_attribute 100

      @callback some_function(term) :: :ok | {:error, term}

      @macrocallback macro_name(term) :: Macro.t()

      @optional_callbacks macro_name: 1

      ...
    end
    ```

1. Usa la pseudo variable `__MODULE__` cuando un módulo se refiera a sí mismo. Esto evitará que tengas que actualizar cualquier referencia cuando el nombre del módulo cambie.

    ```elixir {.line-numbers}
    # Recomendado
    defmodule SomeProject.SomeModule do
      defstruct [:name]

      def name(%__MODULE__{name: name}), do: name
    end
    ```

    Si prefieres utilizar otro nombre para esta referencia, define un alias.

    ```elixir {.line-numbers}
    # Recomendado
    defmodule SomeProject.SomeModule do
      alias __MODULE__, as: SomeModule

      defstruct [:name]

      def name(%SomeModule{name: name}), do: name
    end
    ```

1. Evita repeticiones en los nombres de módulos y espacios de nombrado. Mejorará la legibilidad global y elimina alias ambiguos.

    ```elixir {.line-numbers}
    # No recomendado
    defmodule Todo.Todo do
      ...
    end

    # Recomendado
    defmodule Todo.Item do
      ...
    end
    ```

### Funciones

1. Si una función recibe opciones, estas se deberán agrupar en un *keyword list* como último argumento, y con el operador `\\` se asignará una lista vacia como default. Cada opción tendrá su propia variable, a la cual se le asigna el valor del *keyword* correspondiente en el argumento opciones mediante el método `Keyword.get`, o un valor default en caso de no ser proporcionado.

    ```elixir {.line-numbers}
    # No recomendado
    def some_function(arg, opt1 \\ "Default value 1", opt1 \\ 255, opt1 \\ false) do
      ...
    end

    # Recomendado
    def some_function(arg, options \\ []) do
      opt1 = Keyword.get(options, :opt1, "Default value 1")
      opt2 = Keyword.get(options, :opt2, 255)
      opt3 = Keyword.get(options, :opt3, false)

      ...
    end
    ```

### Documentación

La documentación en Elixir (ya sea cuando es leída en IEx mediante h o cuando es generada con ExDoc) utiliza los atributos de módulo `@moduledoc` y `@doc`.

1. Utiliza `@moduledoc false` si no pretendes documentar un módulo.

    ```elixir {.line-numbers}
    # No recomendado
    defmodule SomeModule do
      ...
    end

    # Recomendado
    defmodule SomeModule do
      @moduledoc false
      ...
    end
    ```

1. Separa el código tras `@moduledoc` con una línea en blanco.

    ```elixir {.line-numbers}
    # No recomendado
    defmodule SomeModule do
      @moduledoc """
      About the module
      """
      use AnotherModule
    end

    # Recomendado
    defmodule SomeModule do
      @moduledoc """
      About the module
      """

      use AnotherModule
    end
    ```

1. Usa *heredocs strings* con markdown para la documentación.

    ```elixir {.line-numbers}
    # No recomendado
    defmodule SomeModule do
      @moduledoc "About the module"
    end

    defmodule SomeModule do
      @moduledoc """
      About the module

      Examples:
      iex> SomeModule.some_function
      :result
      """
    end

    # Recomendado
    defmodule SomeModule do
      @moduledoc """
      About the module

      ## Examples

          iex> SomeModule.some_function
          :result
      """
    end
    ```

### Typespecs

Typespecs es una notación para declarar tipos y especificaciones, ya sea para documentación o para la herramienta de análisis estático Dialyzer.

Los tipos propios deben de ser definidos en la parte superior del módulo junto con las demás directivas.

1. Sitúa las definiciones `@typedoc` y `@type` juntas, y separa cada par con una línea en blanco.

    ```elixir {.line-numbers}
    # Recomendado
    defmodule SomeModule do
      @moduledoc false

      @typedoc "The name"
      @type name :: atom

      @typedoc "The result"
      @type result :: {:ok, term} | {:error, term}

      ...
    end
    ```

1. Si la unión de tipos es demasiado larga para caber en una sola línea, añade una nueva línea e indenta un nivel más para mantener los tipos alineados.

    ```elixir {.line-numbers}
    # No recomendado
    @type long_union_type :: some_type | another_type | some_other_type |
    a_final_type

    # Recomendado
    @type long_union_type ::
            some_type
            | another_type
            | some_other_type
            | a_final_type
    ```

1. Nombra al tipo principal para un módulo t, por ejemplo: la especificación de tipo para un *struct*.

    ```elixir {.line-numbers}
    # Recomendado
    defstruct name: nil, params: []

    @type t :: %__MODULE__{
            name: String.t() | nil,
            params: Keyword.t()
          }
    ```

1. Sitúa las especificaciones justo antes de la definición de la función, sin separarlas con una línea en blanco.

    ```elixir {.line-numbers}
    # No recomendado
    @spec some_function(term) :: result

    def some_function(some_data) do
      {:ok, some_data}
    end

    # Recomendado
    @spec some_function(term) :: result
    def some_function(some_data) do
      {:ok, some_data}
    end
    ```

### Structs

1. Usa una lista de *atoms* para los campos del *struct* que tengan valor **nil**, seguida del resto de *keywords*.

    ```elixir {.line-numbers}
    # No recomendado
    defstruct name: nil, params: nil, active: true

    # Recomendado
    defstruct [:name, :params, active: true]
    ```

1. Omite los corchetes cuando el argumento de `defstruct` sea una lista de *keywords*. No se omitirán si en la lista existe por lo menos un *atom*.

    ```elixir {.line-numbers}
    # No recomendado
    defstruct [params: [], active: true]

    # Recomendado
    defstruct params: [], active: true

    # Requerido - No son opcionales los corchetes, con al menos un atom en la lista.
    defstruct [:name, params: [], active: true]
    ```

1. Si una definición de un *struct* despliega múltiples líneas, coloca cada elemento en su propia linea, manteniendo los elementos alineados.

    ```elixir {.line-numbers}
    # No recomendado
    defstruct foo: "test", bar: true, baz: false,
      qux: false, quux: 1

    # Recomendado
    defstruct foo: "test",
              bar: true,
              baz: false,
              qux: false,
              quux: 1
    ```

1. Si un *struct* multilínea requiere limitadores, dale formato de lista multilinea.

    ```elixir {.line-numbers}
    # Recomendado
    defstruct [
      :name,
      params: [],
      active: true
    ]
    ```

### Excepciones

1. Haz que los nombres de las excepciones terminen en "Error".

    ```elixir {.line-numbers}
    # No recomendado
    defmodule BadHTTPCode do
      defexception [:message]
    end

    defmodule BadHTTPCodeException do
      defexception [:message]
    end

    # Recomendado
    defmodule BadHTTPCodeError do
      defexception [:message]
    end
    ```

1. Utiliza mensajes de error en minúsculas cuando lances excepciones. No utilices puntuación al final.

    ```elixir {.line-numbers}
    # No recomendado
    raise ArgumentError, "This is not valid."

    # Recomendado
    raise ArgumentError, "this is not valid"
    ```

### Strings

1. Haz match de *strings* utilizando la concatenación de string en lugar de patrones binarios:

    ```elixir {.line-numbers}
    # No recomendado
    <<"my"::utf8, _rest::bytes>> = "my string"

    # Recomendado
    "my" <> _rest = "my string"
    ```

### Pruebas

1. Cuando escribas aserciones con ExUnit, se consistente con el orden de los valores esperados y actuales que estás probando. Es preferible poner el valor esperado a la derecha, a no ser que la aserción sea un *pattern match*.

    ```elixir {.line-numbers}
    # Recomendado - Resultado esperado a la derecha.
    assert actual_function(1) == true
    assert actual_function(2) == false

    # No recomendado - Orden incosistente.
    assert actual_function(1) == true
    assert false == actual_function(2)

    # Requerido - La aserción es un pattern match.
    assert {:ok, expected} = actual_function(3)
    ```

## Derechos

### Licencia

![Creative Commons License](http://i.creativecommons.org/l/by/3.0/88x31.png) Este documento está hecho bajo licencia [Creative Commons Reconocimiento 3.0 Unported License](https://creativecommons.org/licenses/by/3.0/deed.es_ES).

### Atribución

La estructura de esta guía, partes del código de ejemplo, y muchos otros puntos iniciales de este documento fueron tomados de:

- [Christopher Adams' Elixir Style Guide](https://github.com/christopheradams/elixir_style_guide)
- [Alberto Almargo's Elixir Style Guide](https://github.com/albertoalmagro/elixir_style_guide)
- [Aleksei Magusev's Elixir Style Guide](https://github.com/lexmag/elixir-style-guide)
- [Credo's Elixir Style Guide](https://github.com/rrrene/elixir-style-guide)