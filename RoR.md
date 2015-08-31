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
si ejecutamos el *IRB* y corremos el comando:

    File.expand_path("./")
    => /Users/almapase/OneDrive/Platzi/ror/onepx

salimos de *IRB* y ejecutamos

    ~ pwd
    => /Users/almapase/onedrive/platzi/ror/onepx

SOLUCION, los dos comandos anteriores muestran la carpera ror en en minusculas pero en realidad es así: *RoR*:

    ~ cd onedrive/platzi
    ~ mv RoR ror

---
#Activar HSTORE en PosgreSQL
para eso haremos una migración

    ~ rails g migration add_hstore

Esto nos genera un rarchivo *20150830162955_add_hstore.rb* en el directorio => db/migrate
El archivo debe tener el siguiente codigo:

    class AddHstore < ActiveRecord::Migration
        def up
            enable_extension :hstore
        end
    
        def down
            disable_extension :hstore
        end
    end

En la consola aplicamos la migración

    ~ rake db:migrate
    
#Generaremos nuestro modelo usando las migraciones y el patron ACTIVE_RECORD
gereramos el modelo

    ~rails g model image name category:integer description:text tags
    
Nos generá un archivo en la carpeta => db/migrate, lo dejamos con el siguiente condigo:

    class CreateImages < ActiveRecord::Migration
      def change
        create_table :images do |t|
          t.string :name, null: false
          t.integer :category, default: 0
          t.text :description
          t.string :tags, array: true, default: [] 
                                                
          t.timestamps null: false
        end
      end
    end

Hora ejecutamos la migración:

    ~ rake db:migrate
    == 20150830164618 CreateImages: migrating =====================================
    -- create_table(:images)
       -> 0.0942s
    == 20150830164618 CreateImages: migrated (0.0943s) ============================
    
---
#Hoara trabajaremos con nuestro modelo
en el archivo *image.rb* colocamos el siguente codigo, que nos permitira darle nombre a las categorias
por medio de un *enumerable*

    class Image < ActiveRecord::Base
      enum category: %w(portrait landscape city\ exploration nature animals)
    end

Ahora trabajaremos en la consola de Rails, para eso ejecutamos:

    ~ rails c
    
Nos queda el siguiente pront (pry biene de una gema pryrails que decora el codigo de la consola de Rails:

    Loading development environment (Rails 4.2.4)
    [1] pry(main)>

Para interrumpir un proceso en ejecución dentro de la consola:

    Ctrl-C

Para salir de la consola en sí,

    Ctrl-D
    
Si en la consola de Rails ejecutamos, para saber cuantos registros tiene la tabla images

    > Image.cout
    (8.0ms)  SELECT COUNT(*) FROM "images"
    => 0

para que retorne todos los registros
   
    pry(main)> Image.all
    Image Load (5.2ms)  SELECT "images".* FROM "images"
    => []

Podemos crear un nuevo registro por medio de la consola de rails de la siguiente forma:

    [5] pry(main)> i = Image.new
    => #<Image:0x007fa54a43d6d0 id: nil, name: nil, category: 0, description: nil, tags: [], created_at: nil, updated_at: nil>
                    
    [6] pry(main)> i.name = 'Ciudad de Santiago'
    => "Ciudad de Santiago"
                    
    [7] pry(main)> i.description = 'Día lluvioso'
    => "Día lluvioso"
                    
    [8] pry(main)> i.category = "city \ exploration"
    ArgumentError: 'city  exploration' is not a valid category
    from /Users/almapase/.rvm/gems/ruby-2.2.1@Gemset_onepx/gems/activerecord-4.2.4/lib/active_record/enum.rb:105:in `block (3 levels) in enum'
                        
    [9] pry(main)> i.category = "city\ exploration"
    => "city exploration"
                            
    [10] pry(main)> i.tags = %w(santiago cuidad calles lluvia)
    => ["santiago", "cuidad", "calles", "lluvia"]
                            
    [11] pry(main)> i
    => #<Image:0x007fa54a43d6d0
     id: nil,
     name: "Ciudad de Santiago",
     category: 2,
     description: "Día lluvioso",
     tags: ["santiago", "cuidad", "calles", "lluvia"],
     created_at: nil,
     updated_at: nil>
                        
    [12] pry(main)> i.save
       (2.5ms)  BEGIN
      SQL (37.8ms)  INSERT INTO "images" ("name", "description", "category", "tags", "created_at", "updated_at") VALUES ($1, $2, $3, $4, $5, $6) RETURNING "id"  [["name", "Ciudad de Santiago"], ["description", "Día lluvioso"], ["category", 2], ["tags", "{santiago,cuidad,calles,lluvia}"], ["created_at", "2015-08-30 22:58:12.094602"], ["updated_at", "2015-08-30 22:58:12.094602"]]
       (27.9ms)  COMMIT
    => true


Ahora podemos consultar el objeto *i* enlazado al regustro de la base de datos

    [13] pry(main)> i
    => #<Image:0x007fa54a43d6d0
     id: 1,
     name: "Ciudad de Santiago",
     category: 2,
     description: "Día lluvioso",
     tags: ["santiago", "cuidad", "calles", "lluvia"],
     created_at: Sun, 30 Aug 2015 22:58:12 UTC +00:00,
     updated_at: Sun, 30 Aug 2015 22:58:12 UTC +00:00>

Buscar dentro de la tabla imagenes los registros que contegan *lluv* dentro de la descripción

    [15] pry(main)> Image.where("description LIKE ?", "%lluv%")
      Image Load (22.1ms)  SELECT "images".* FROM "images" WHERE (description LIKE '%lluv%')
    => [#<Image:0x007fa54e079a68
      id: 1,
      name: "Ciudad de Santiago",
      category: 2,
      description: "Día lluvioso",
      tags: ["santiago", "cuidad", "calles", "lluvia"],
      created_at: Sun, 30 Aug 2015 22:58:12 UTC +00:00,
      updated_at: Sun, 30 Aug 2015 22:58:12 UTC +00:00>]

Con esta centencia EXCLUSIVA DE PosgreSQL buscamos dentro del array del campo *tags*

    [16] pry(main)> Image.where("? = ANY(tags)", "calles")
      Image Load (15.2ms)  SELECT "images".* FROM "images" WHERE ('calles' = ANY(tags))
    => [#<Image:0x007fa54e1c9da0
      id: 1,
      name: "Ciudad de Santiago",
      category: 2,
      description: "Día lluvioso",
      tags: ["santiago", "cuidad", "calles", "lluvia"],
      created_at: Sun, 30 Aug 2015 22:58:12 UTC +00:00,
      updated_at: Sun, 30 Aug 2015 22:58:12 UTC +00:00>]

Todos los metodos diponibles para *Image*

    [17] pry(main)> Image.instance_methods
    => [:category=,
     :category,
     :category_before_type_cast,
     :portrait?,
     :portrait!,
     :landscape?,
     :landscape!,
     :"city exploration?",
     :"city exploration!",
     :nature?,
     :nature!,
     :animals?,
     :animals!,
     :id,
     :id=,
     :id_before_type_cast,
     :id_came_from_user?,
     :id?,
     :id_changed?,
     :id_change,
     :id_will_change!,
     :id_was,
     :reset_id!,
     :restore_id!,
     :name,
     :name=,
     :name_before_type_cast,
     :name_came_from_user?,
     :name?,
     :name_changed?,
     :name_change,
     :name_will_change!,
     :name_was,
     :reset_name!,
     :restore_name!,
     :category_came_from_user?,

#Ahora conectaremos el patron MVC


