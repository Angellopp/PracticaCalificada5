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




