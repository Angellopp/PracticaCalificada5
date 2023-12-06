### Practica Calificada 5
## Preguntas:  En este conjunto de preguntas tus respuestas deben ir de acuerdo a las actividades correspondientes, no se puntúa sino hay evidencia del uso de los scripts desarrollados y solo presentas respuestas sin evidencia de lo desarrollado a lo largo del curso. (7 puntos)

## 1.	En las actividades relacionados a la Introducción de Rails los métodos actuales del controlador no son muy robustos: si el usuario introduce de manera manual un URI para ver (Show) una película que no existe (por ejemplo /movies/99999), verás un mensaje de excepción horrible. Modifica el método show del controlador para que, si se pide una película que no existe, el usuario sea redirigido a la vista Index con un mensaje más amigable explicando que no existe ninguna película con ese.

El error que nos muestra es debido a que no existe una pelicula con id = 500

![Alt text](image.png)

Lo que haremos es utilizar un bloque ``begin`` y ``rescue`` para capturar la excepción ``ActiveRecord::RecordNotFound`` que se produce cuando no se encuentra una película con el ID proporcionado. Dentro del bloque ``rescue``, redirigimos al usuario a la página de índice (``movies_path``) con un mensaje amigable indicando que no se encontró ninguna película con el ID proporcionado.

```ruby
def show
    id = params[:id] # retrieve movie ID from URI route
    begin
      @movie = Movie.find(id) # look up movie by unique ID
    rescue ActiveRecord::RecordNotFound
      redirect_to movies_path, :notice => "No se encontró ninguna película con ese ID."
    end
  end
```

De esta manera, en lugar de ver un mensaje de excepción horrible, el usuario será redirigido a la página de índice con un mensaje más amigable explicando que no existe ninguna película con ese ID.

Tambien agregamos a la vista `index.hmtl.haml` el siguiente codigo para que se muestre la alerta de ``flash``:

```ruby
- if flash[:alert].present?
  .alert.alert-danger
    = flash[:alert]
```
y nos proporciona la siguiente vista:

![Alt text](image-1.png)

## 2.	En las actividades relacionados a Rails Avanzado, si tenemos el siguiente ejemplo de código que muestra cómo se integra OmniAuth en una aplicación Rails:

```ruby
class SessionsController < ApplicationController
 	   def create
    	         @user = User.find_or_create_from_auth_hash(auth_hash)
    	         self.current_user = @user
    	         redirect_to '/'
                   end
          	  protected
          	  def auth_hash
            		request.env['omniauth.auth']
                 end
              end
```

2.	En las actividades relacionados a Rails Avanzado, si tenemos el siguiente ejemplo de código que muestra cómo se integra OmniAuth en una aplicación Rails:

```ruby
class SessionsController < ApplicationController
 	   def create
    	         @user = User.find_or_create_from_auth_hash(auth_hash)
    	         self.current_user = @user
    	         redirect_to '/'
                   end
          	  protected
          	  def auth_hash
            		request.env['omniauth.auth']
                 end
              end
```

El código coloca la funcionalidad en su propio método ``auth_hash`` en lugar de simplemente referenciar ``request.env['omniauth.auth']`` directamente por razones de claridad y reutilización del código.

Al colocar esta funcionalidad en su propio método, el código se vuelve más legible y más fácil de entender. Además, si en el futuro se necesita modificar o agregar alguna lógica adicional al manejo de la autenticación, se puede hacer directamente en el método ``auth_hash`` sin afectar el resto del código en el método ``create``.

El script:

```ruby
def auth_hash
  request.env['omniauth.auth']
end
```

El método ``auth_hash`` simplemente devuelve el valor de ``request.env['omniauth.auth']``.

## 3. En las actividades relacionados a JavaScript, Siguiendo la estrategia del ejemplo de jQuery utiliza JavaScript para implementar un conjunto de casillas de verificación (checkboxes) para la página que muestra la lista de películas, una por cada calificación (G, PG, etcétera), que permitan que las películas correspondientes permanezcan en la lista cuando están marcadas. Cuando se carga la página por primera vez, deben estar marcadas todas; desmarcar alguna de ellas debe esconder las películas con la clasificación a la que haga referencia la casilla desactivada.

Creamos el archivo ``app/assets/javascripts/movies.js`` y agregamos el siguiente código JavaScript:

```javascript
document.addEventListener('DOMContentLoaded', function() {
  // Primero obtenemos todas las casillas de verificación de calificación
  var checkboxes = document.querySelectorAll('.rating-checkbox');

  // Agregamos un escuchador de eventos a cada casilla de verificación
  checkboxes.forEach(function(checkbox) {
    checkbox.addEventListener('change', function() {

      // Filtramos las películas según las casillas de verificación seleccionadas
      var selectedRatings = [];
      checkboxes.forEach(function(checkbox) {
        if (checkbox.checked) {
          selectedRatings.push(checkbox.value);
        }
      });

      // Mostramoslas películas filtradas
      var movies = document.querySelectorAll('.movie-row');
      movies.forEach(function(movie) {
        var rating = movie.dataset.rating;
        if (selectedRatings.includes(rating)) {
          movie.style.display = 'table-row';
        } else {
          movie.style.display = 'none';
        }
      });
    });
  });

  // Marcamos todas las casillas de verificación al cargar inicialmente la página
  checkboxes.forEach(function(checkbox) {
    checkbox.checked = true;
  });

});
```

TAmben nos aseguramos de tener la siguiente línea en el archivo ``app/assets/javascripts/application.js`` para incluir el archivo movies.js:

```ruby
//= require movies
```
Ahora podemos comprobarlo en la página INICIAL de movies:

![Alt text](image-3.png)

y lo que nos genera al filtrar las películas por calificación.:

![Alt text](image-2.png)

## 4. De la actividad relacionada a BDD e historias de usuario crea definiciones de pasos que te permitan escribir los siguientes pasos en un escenario de RottenPotatoes:

```ruby
Given the movie "Inception" exists
	And it has 5 reviews
	And its average review score is 3.5
```

Vamos a usar el proyecto de RottenPotatoes que viene adjunto a esta PC: `BDD_Cucumber`, que ya tiene generada la carpeta ``features``.

``Given the movie "Inception" exists:`` Este paso crea una película con el título "Inception" utilizando el método create del modelo Movie. La película se guarda en la base de datos.

```ruby
Given /^the movie "([^"]*)" exists$/ do |movie_title|
  Movie.create(title: movie_title)
end
```
``And it has 5 reviews:`` Este paso asocia 5 revisiones a la película actualmente en consideración utilizando el método update del modelo Movie. El número de revisiones se proporciona como un parámetro y se convierte a un entero antes de asignarlo al atributo reviews de la película.

```ruby
Given /^it has (\d+) reviews$/ do |review_count|
  movie = Movie.last
  movie.update(reviews: review_count.to_i)
end
```

``And its average review score is 3.5:`` Este paso establece el puntaje promedio de revisión para la película actualmente en consideración utilizando el método update del modelo Movie. El puntaje promedio se proporciona como un parámetro y se convierte a un número de punto flotante decimal antes de asignarlo al atributo ``rating``de la película.

```ruby
Given /^its average review score is (\d+\.\d+)$/ do |review_score|
  movie = Movie.last
  movie.update(rating: review_score.to_f)
end
```

## 5. De la actividad relacionadas a BDD e historias de usuario, supongamos que en RottenPotatoes, en lugar de utilizar seleccionar la calificación y la fecha de estreno, se opta por rellenar el formulario en blanco. Primero, realiza los cambios apropiados al escenario. Enumera las definiciones de pasos a partir que Cucumber invocaría al pasar las pruebas de estos nuevos pasos. (Recuerda: rails generate cucumber:install)

Para rellenar el formulario en blanco aplicamos los siguientes cambios:

```ruby
Scenario: Adding a new movie with a blank form
  Given I am on the Add New Movie page
  When I fill in the following form:
    | Title        | Inception    |
    | Rating       |              |
    | Release Date |              |
  And I press "Save Changes"
```

En cuanto a las definiciones de pasos que Cucumber invocaría del archivo ``features/movies_step.rb`` para pasar las pruebas de estos nuevos pasos:

```ruby
When /^(?:|I )fill in the following form:$/ do |fields|
  fields.rows_hash.each do |name, value|
    When %{I fill in "#{name}" with "#{value}"}
  end
end
When /^(?:|I )fill in "([^"]*)" with "([^"]*)"$/ do |field, value|
  fill_in(field, :with => value)
end
```
Con esta definición de paso, Cucumber llenará el formulario en blanco con los detalles de la película proporcionados en la tabla.


