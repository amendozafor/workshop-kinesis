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
        <li><a href="#crear-usuario-amazon-cognito-para-autenticar-desde-kdg">Crear usuario Amazon cognito para autenticar desde KDG</a></li>
      </ul>
    </li>
    <li><a href="#crear-kinesis-data-streams">Crear Kinesis Data Streams</a></li>
    <li><a href="#crear-kinesis-firehose">Crear Kinesis Firehose</a></li>
    <li><a href="#crear-aplicacion-de-kinesis-data-analytics">Crear Aplicación de Kinesis Data Analytics</a></li>
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

`NOTA: La configuración de Kinesis Data Generator (KDG) en una cuenta de AWS creará un conjunto de credenciales de Cognito. Los usuarios que puedan autenticarse con esas credenciales podrán publicar en todos los Kinesis Data Streams y Kinesis Data Firehoses de la cuenta. Después de ejecutar la configuración a continuación, puede cambiar los roles de IAM que se crean para restringir los permisos para publicar en flujos o mangueras de incendios específicos.`

###Crear usuario Amazón cognito para autenticar desde KDG
1. Inicie Sesión en la consola de AWS
2. Ingresa al siguiente enlace: https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=Kinesis-Data-Generator-Cognito-User&templateURL=https://aws-kdg-tools.s3.us-west-2.amazonaws.com/cognito-setup.yaml

3. Se te abrirá un template de cloudformation ya listo para desplegar, o tambien puedes cargarlo de cero en cloudformation utilizando el template cloudformation https://raw.githubusercontent.com/awslabs/amazon-kinesis-data-generator/mainline/setup/cognito-setup.yaml

<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/3548ebd5-4f34-4502-a2d6-f43bd4f7d37e">

Clic en el botón Next

Creas un usuario y una contraseña para cognito
<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/a072827f-e537-4575-821a-b3e3a2944a4f">

Clic en el botón Next

<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/3782a014-553d-4385-8969-af5b074368cc">

En esta sesión dejamos los valores por defecto y clic en el botón Next

Revisamos la Configuración, y Aceptamos los terminos para que amazón cree los recursos
<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/1ed94a58-1362-40ed-8b0b-bf7c1097405f">

y por último, clic en el botón Sumbit

Inmediatamente iniciaría el proceso de creación de los recursos necesarios para autorizar al Kinesis Data Generator a escribir datos en nuestros recursos de Kinesis.


Una vez se haya completado la creación de los recursos, nos debe mostrar el estado CREATE_COMPLETE
<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/8e7cb925-34c7-433e-afea-941a830c964e">


Podemos comprobar que recusos se crearon, haciendo clic en el stack y llendo a la sección Resources
<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/adeb5564-3671-47d7-9ea0-f4e652e6bb0e">

Una vez hayamos comprobado que los recursos desplegados en el stack se crearon correctamente, vamos a la pestaña "Output" y accedemos a la URL que nos generó

<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/c8574c2d-bdb8-49ff-8f93-25c4a7e2b2b8">

Una vez nos cargue el sitio, iniciamos sesión con el usuario y la contraseña que creamos en el stack de cloudformation

<img width="800" alt="image" src="https://github.com/amendozafor/workshop-kinesis/assets/85176161/67bd6dfb-9156-4735-8f24-2402ffa3758c">

## Crear Kinesis Data Streams
