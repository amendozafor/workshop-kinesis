# workshop-kinesis


<!-- TABLE OF CONTENTS -->
<details>
  <summary>Tabla de contenido</summary>
  <ol>
    <li>
      <a href="#antes-de-empezar">Antes de empezar</a>
      <ul>
        <li><a href="#prerequisitos">Prerequisitos</a></li>
      </ul>
    </li>
    <li><a href="#simular-datos-de-entrada-en-tiempo-real">Simular datos de entrada en tiempo real</a>
     <ul>
        <li><a href="#crear-usuario-amazon-cognito">Crear usuario Amazon cognito</a>          </li>
      </ul>
    </li>
    <li><a href="#crear-kinesis-data-streams">Crear Kinesis Data Streams</a></li>
    <li><a href="#crear-kinesis-data-firehose">Crear Kinesis Data Firehose</a></li>
    <li><a href="#crear-kinesis-data-analytics-studio">Crear Kinesis Data Analytics Studio</a></li>
    <li><a href="#actualizar-rol-iam">Actualizar Rol IAM</a></li>
    <li><a href="#crear-notebook-kinesis-data-analytics-studio">Crear Notebook Kinesis Data Analytics Studio</a></li>
    <li><a href="#enviar-datos-de-prueba">Enviar Datos de Prueba</a></li>
    <li><a href="#analisis-en-tiempo-real">Análisis en Tiempo Real</a></li>
    <li><a href="#limpiar">Limpiar</a></li>
  </ol>
</details>


## Antes de empezar
Ingestar fácilmente datos de numerosas fuentes y tomar decisiones oportunas se está convirtiendo en una capacidad crítica y central para muchas empresas. En este laboratorio, brindamos experiencia práctica en el uso de Kinesis Data Stream para capurar datos en tiempo real, Kinesis Data Firehose para  cargar flujos de datos en Amazon S3 y realizar análisis en tiempo real en el flujo con Kinesis Data Analytics.

### Prerequisitos
Para este laboratorio, necesitaremos tener una cuenta de AWS con los permisos le lectura y escritura sobre los siguientes servicios

* AWS Kinesis Data Streams
* AWS Kinesis Data Firehose
* AWS Kinesis Data Analytics
* AWS Glue Catalog
* AWS Lambda
* Amazon S3
* Cognito user Pools
  

## Simular datos de entrada en tiempo real

En este paso utilizaremos Amazon Kinesis Data Generator (KDG) que nos facilitará el envío de datos a Kinesis Streams o Kinesis Firehose.

Antes de poder enviar datos a Kinesis, primero debe crear un usuario de Amazon Cognito en su cuenta de AWS con permisos para acceder a Amazon Kinesis. Para simplificar este proceso, se proporcionan una función de Amazon Lambda y una plantilla de Amazon CloudFormation para crear el usuario y asignar los permisos necesarios para usar el KDG.

`NOTA: La configuración de Kinesis Data Generator (KDG) en una cuenta de AWS creará un conjunto de credenciales de Cognito. Los usuarios que puedan autenticarse con esas credenciales podrán publicar en todos los Kinesis Data Streams y Kinesis Data Firehoses de la cuenta. Después de ejecutar la configuración a continuación, puede cambiar los roles de IAM que se crean para restringir los permisos para publicar en flujos especificos.`

### Crear usuario Amazon cognito
1. Inicie Sesión en la consola de AWS
2. Ingresa al siguiente enlace: [Cloudformation deploy](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=Kinesis-Data-Generator-Cognito-User&templateURL=https://aws-kdg-tools.s3.us-west-2.amazonaws.com/cognito-setup.yaml) Donde se te abrirá un template de cloudformation ya listo para desplegar en tu cuenta de AWS

Tambien puedes cargar el template de cero en cloudformation utilizando el template cloudformation, descargandolo desde el siguiente enlace
[cognito-setup.yaml](https://raw.githubusercontent.com/awslabs/amazon-kinesis-data-generator/mainline/setup/cognito-setup.yaml)
<br /><br />
3. Clic en el botón `Next`
   
<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/3548ebd5-4f34-4502-a2d6-f43bd4f7d37e">
<br /><br />

4. Debes crear un usuario y una contraseña para tu usuario de cognito rellenando los campos `username` y `password` y le damos clic en el botón `Next`
<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/a072827f-e537-4575-821a-b3e3a2944a4f">
<br /><br />

5. Para este ejercicio vamos a dejar los demas campos con los valores por defecto y le damos clic en el botón `Next`
<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/3782a014-553d-4385-8969-af5b074368cc">
<br /><br />

6. Revisamos la Configuración, y Aceptamos los terminos para que se incie la creación de los recursos  en nuestra cuent de AWS y hacemos clic en el botón `Submit`
<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/1ed94a58-1362-40ed-8b0b-bf7c1097405f">
<br /><br />

Inmediatamente iniciaría el proceso de creación de los recursos necesarios para autorizar al Kinesis Data Generator (KDG) a escribir datos en nuestros recursos de Kinesis.


Una vez se haya completado la creación de los recursos, nos debe mostrar el estado CREATE_COMPLETE
<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/8e7cb925-34c7-433e-afea-941a830c964e">
<br /><br />

Podemos comprobar que recusos se crearon, haciendo clic en el stack y llendo a la sección `Resources`
<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/adeb5564-3671-47d7-9ea0-f4e652e6bb0e">
<br /><br />

Una vez hayamos comprobado que los recursos desplegados en el stack se crearon correctamente, vamos a la pestaña `Output` y accedemos a la URL que nos generó

<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/c8574c2d-bdb8-49ff-8f93-25c4a7e2b2b8">
<br /><br />

Cuando nos cargue por completo la página del Kinesis Data Generator, Iniciamos sesión con el `username` y la `password` que creamos en uno de los pasos anteriores y nos debe mostrar la siguiente pantalla:

<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/1daf8a57-1a5a-4e47-9c80-e975ad2c60a5">
<br /><br /><br />

## Crear Kinesis Data Streams

Nuestro Kinesis Data stream reibirá los datos que enviemos desde el Kinesis Data Generator en tiempo real para posteriormente enviarlos a un destino en S3 utilizando Kinesis Data Firehose y Realizando análisis en tiempo real con Kinesis Data Analytics

1. En el búscador de la consola de AWS escribimos `Kinesis` y seleccionamos `Kinesis Data Stream`
<img width="1175" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/556d8008-4f5e-4ff4-ac51-1d04cb85939f">
<br /><br />

2. Hacemos clic en `Create Data Streams`
3. Ingresamos la siguiente configuración:
   * Data stream name: `kds-workshop-ug`
   * Capacity mode : `Provisioned`
   * Provisioned shards: `1`
  
 4. Clic en el botón `Create Data Stream`  

Nuestro Kinesis se empezará a crear y una vez finalicé se debe ver de la siguiente manera:
<img width="1130" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/62198216-e2c9-495d-8c7e-38f72ae59bbd">

<br /><br /><br />

## Crear Kinesis Data Firehose

Crearemos una secuencia de entrega de datos de Kinesis Data Firehose, para recibir los datos que se ingestan en Kinesis Data Streams y llevarlos a un bucket de S3 de manera particionada

1. Estando en el servicio de Kinesis en la consola de AWS, hacemos clic en la opción `Delivery Stream` y hacemos clic en el botón `Create Delivery Stream`
   
   <img width="1363" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/439f3e3d-2301-49f9-8ce0-c968031db596">
   <br /><br />
   
2. Ingresamos la siguiente configuración
      * Source: `Amazon Kinesis Data Stream`
      * Destination: `Amazon S3`

3. En la sección `Source settings`, hacemos clic  en el botón `Browse` y seleccionamos nuestro Kinesis Data Stream creado anteriormente
       <img width="1328" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/fea6b502-27a5-4740-8ef6-65b0b060bc90">

4. Diligenciamos el campo Delivery stream name: `kdf-workshop-ug`

5. En la sección `Destination settings` crearemos un nuevo bucket de S3, a donde nos llegaran nuestros datos particionados

   Hacemos clic en la opción `Create` y llenamos la siguiente configuración:
   *  Bucket name: `workshop-ug-med-bucket`
   *  dejamos la demas configuración por defecto
   *  Hacemos clic en el Botón `Crear Bucket`
6. Volvemos a la pantalla donde estamos creando nuestro Kinesis Data Firehose y  en el campo `S3 bucket` ingresamos el nombre el bucket que acabamos de crear
7. dejamos los demas campos con la configuración por defecto y hacemos clic en el botón `Create Delivery Stream`

Una vez se cree nuestro delivery steam, se verá de la siguiente manera y estará listo para recibir los datos y almacenarlos en S3

<img width="1378" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/cec220c3-2351-4256-8b34-51c4e3efaa63">

<br /><br /><br />

## Crear Kinesis Data Analytics Studio

1. Estando en el servicio de Kinesis en la consola de AWS, ingresamos a `Streaming Applications`, nos vamos a la sección `Studio` y hacemos clic en en botón `Create Studio Notebook`

<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/8a78b035-f8e3-41c6-bff5-6cf9a7031221">
<br /><br />

2. Ingresas la siguiente configuración:
   * Creation method: `Quick create with sample code`
   * Studio notebook name: `kda-workshop-ug`
   * AWS Glue database: `Default`
   * Clic botón `Create Studio Notebook`

Se iniciará el proceso de creación de nuestra aplicación de Kinesis Data Analytics, y en unos segundos estará lista para usar
<br /><br /><br />
## Actualizar Rol IAM

En el paso anterior, por defecto se crea un rol, el cual debemos modificarlo para darle acceso al nuestro notebook a los servicios de S3, Kinesis y Glue

1. Hacemos clic en el nombre del rol y nos llevará directamente a IAM

<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/dfd27b54-f25b-4d20-b414-6f4069588b1a">
<br /><br />
  
2. Seleccionamos nuestro rol y hacemos clic en el boton `Attach Policies`

<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/8b8c9d92-8488-43ac-8f13-17c62bce8f08">
 <br /><br />
  
3. Buscamos y agregamos  `AmazonS3FullAccess`, `AmazonKinesisFullAccess` y `AWSGlueServiceRole`

<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/c41e9f65-c1b9-40d3-8adc-837fba3d9122">
<br /><br />
La configuración de nuestra aplicación ya está completa, cuando esté lista pasamos a a siguiente sesión.
<br /><br />

## Crear Notebook Kinesis Data Analytics Studio

Ahora que nuestra aplicación ya está lista, lo que debemos hacer es iniciarla, y empezar a explorar nuestros datos.

1. hacemos clic en el botón Run y esperamos unos minutos a que se inicie nuestra aplicación

<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/c0321bf8-8523-499f-9c0b-2e35c208472b">
<br /><br />

2. Cuando la apliación esté en estado `Running`, clic en `Open in Apache Zeppelin`

3. Creamos nuestro primer Notebook:
    Estando en la interfaz de Zeppelin, hacemos clic en la opción `Create new note` y ya tendremos nuestra interfaz lista para empezar a analizar datos de manera interactiva
<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/88a74593-8bb9-4294-82e9-610648472689">

<br /><br /><br />
## Enviar Datos de Prueba

Ya está todo listo. Ahora solo nos falta, empezar a recibir datos para iniciar nuestro análisis en tiempo real, asi que volvemos al Kinesis Data Generator y llenamos la siguiente configuración:

* Region: `us-east-1`
* Stream/delivery stream: `kds-workshop-ug`
* Records per second: `1`
* Record template: Pegamos el siguiente script, el cual nos generará data de manera dinamica:
```sh
 {
    "fecha_compra": "{{date.now("YYYY-MM-DD HH:mm:ss")}}",
    "nombre_cliente": "{{name.firstName}} {{name.lastName}}",
    "numero_documento":{{random.number(1000000000)}},
    "cantidad_items": {{random.number(
        {
            "min":1,
            "max":150
        }
    )}},
    "estado_transaccion": "{{random.arrayElement(
        ["OK","SIN FONDOS","ERROR PLATAFORMA"]
    )}}",
    "valor_total":{{random.number(
        {
            "min":1000,
            "max":10000000
        }
    )}}
}
```

<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/53dd83f9-3cc9-4c45-b82c-accbfde51f4b">
<br /><br />
Hacemos Clic en el botón `Send Data` y se empezarán a ingestar los datos en nuestro Kinesis Data Streams
<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/a07016d6-b8c4-4ca9-9b74-6bf4647dfccc">


<br /><br /><br />
## Análisis en Tiempo Real

Volvemos a nuestro Notebook de Zeppelin, y lo primero que haremos es crear una tabla con nuestra estructura de datos, asi que pegamos el siguiente fragmeto y lo ejecutamos en el notebook:

```sh
%flink.ssql

CREATE TABLE ventasug (
  `fecha_compra` TIMESTAMP(3),
  `nombre_cliente` STRING,
  `numero_documento` STRING,
  `cantidad_items` INT,
  `estado_transaccion` STRING,
  `valor_total` INT
)
PARTITIONED BY (estado_transaccion)
WITH (
  'connector' = 'kinesis',
  'stream' = 'kds-workshop-ug',
  'aws.region' = 'us-east-1',
  'scan.stream.initpos' = 'LATEST',
  'format' = 'json'
);
```

Nos debe salir el siguiente mensaje:
<img width="857" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/53d22e10-d31e-44a7-b199-c433fd842a26">

con esto se ha creado una tabla llamada `ventasug` que estará disponible en nuestro catálogo de GLUE en la base de datos `Default`.

Ya podremos empezar a realizar consultas sobre nuestros datos, ya sea SQL o utilizando Python y Escala.

Ahora, ejecutamos el siguiete Query y podemos ver como va llegando la data en tiempo real y podremos empezar a realizar análisis mas profundos
```sh
%flink.ssql(type=update)

select *
from ventasug;
```
<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/d558a058-126e-4cae-b17b-a117f1d7d73a">

<br /><br /><br />
## Limpiar

1. Parar el Kinesis Data Generator:
<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/78058675-aa39-4f57-861c-231378bcf9e1">


2. Eliminar Kinesis Data Streams
3. Eliminar/Detener Aplicación de Kinesis Data Analytics
4. Eliminar Kinesis Data Firehose
5. Eliminar Bucket S3
6. Eliminar catálogo de GLUE
