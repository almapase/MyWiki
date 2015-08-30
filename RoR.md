Instalando Ruby on Rails
==============================================

Instalar RVM y Ruby a la vez:

    ~ curl -L https://get.rvm.io | bash -s stable --ruby

luego de instalar ejecutar la sigiente fuente:

    ~ source ~/.rvm/scripts/rvm

para revisar que ruby hay instalado:

    ~ rvm list

###Definimos el Gemset del proyecto
creamos el Gemset: *Gemset_onepx* en este caso, y le decimos que lo use todo en el mismo comando:

    ~ rvm use 2.2.1@Gemset_onepx --create

si solo quisieramos indicar que use cierto Gemset:

    ~ rvm 2.2.1@[Nombre del Gemset]

###Instalamos Rails en el proyecto
Instalamos la ultima version disponible:

    ~ gem install rails

para saber los gemset instalados:

    ~ rvm gemset list_all

---

# Creamos un nuevo proyecto llamado ONEPX será un clon de 500px.com
Nos unbicamos en el directorio que deceamos

    ~ cd onedrive/platzi/ror

ejecutamos el comando para crear una proyecto, en este caso indicando a postgresql como motror de base de datos:

    ~ rails new onepx -d postgresql

dio un error instalado pg -v 0.18.2, SOLUCIONAMOS EL ERROR COMO SIGUE:

    INSTALAMOS postgreSQL
    En la terminal ejecutamos:
    ~ gem install pg -- --with-pg-config=/Library/PostgreSQL/9.4/bin/pg_config

---
agregamos la siguiente dependencia en el archivo GemFile

    group :test do
      gem 'minitest-rails'
    end

agregamos al ambiente de desarrollo la dependencia

    gem 'pry-rails'
para actualizar las depencias, Corremos el comando:

    ~ bunble

nos muestra las tareas programadas en rails

  ~ rake -T

Usamos la primera tarea para crear la base de datos en el motor:

  ~ rake db:drop db:create db:migrate

El ultimo comando nos arroja el siguiente error:
*could not connect to server: No such file or directory*
SOLUCIONAMOS con los siguientes comandos:

    ~ sudo mkdir /var/pgsql_socket/
    ~ sudo ln -s /private/tmp/.s.PGSQL.5432 /var/pgsql_socket/

Volvemos a ejecutar:

    ~ rake db:drop db:create db:migrate

Otro error: faltan las password de Postgres

    en el archivo dataabse.yml
    agregamos ususrio y password
    a las bases de datos de desrrollo y de test

Error, al tratar de ver la vista en el browser:
*AbstractController::Helpers::MissingHelperError in ImagesController#index*
Luego de investigar nos damos cuenta que es un error mayusculas iniciales en los nombres de las carpetas
si ejecutamos el *IRB* y correcomos el comando:

    File.expand_path("./")
    => /Users/almapase/OneDrive/Platzi/ror/onepx

salimos de *IRB* y ejecutamos

    ~ pwd
    => /Users/almapase/onedrive/platzi/ror/onepx

SOLUCION:

    ~ cd onedrive/platzi
    ~ mv RoR ror
