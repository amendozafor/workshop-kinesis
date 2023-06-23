# workshop-kinesis
Workshop de Kinesis 

## Antes de empezar
Ingestar fácilmente datos de numerosas fuentes y tomar decisiones oportunas se está convirtiendo en una capacidad crítica y central para muchas empresas. En este laboratorio, brindamos experiencia práctica en el uso de Kinesis Data Stream para capurar datos en tiempo real, Kinesis Data Firehose para  cargar flujos de datos en Amazon S3 y realizar análisis en tiempo real en el flujo con Kinesis Data Analytics.

### Prerequisitos
Para este laboratorio, necesitaremos tener una cuenta de AWS con los permisos le lectura y escritura sobre los siguientes servicios

* AWS Kinesis Data Streams
* AWS Kinesis Data Firehose
* AWS Lambda
* Amazon S3
* Cognito user Pools
  


## Simular datos de entrada en tiempo real


En este paso utilizaremos Amazon Kinesis Data Generator (KDG) que nos facilitará el envío de datos a Kinesis Streams o Kinesis Firehose.

Antes de poder enviar datos a Kinesis, primero debe crear un usuario de Amazon Cognito en su cuenta de AWS con permisos para acceder a Amazon Kinesis. Para simplificar este proceso, se proporcionan una función de Amazon Lambda y una plantilla de Amazon CloudFormation para crear el usuario y asignar los permisos necesarios para usar el KDG.

NOTA: La configuración de Kinesis Data Generator (KDG) en una cuenta de AWS creará un conjunto de credenciales de Cognito. Los usuarios que puedan autenticarse con esas credenciales podrán publicar en todos los Kinesis Data Streams y Kinesis Data Firehoses de la cuenta. Después de ejecutar la configuración a continuación, puede cambiar los roles de IAM que se crean para restringir los permisos para publicar en flujos o mangueras de incendios específicos.

1. Inicie Sesión en la consola de AWS
2. Ingresa al siguiente enlace: https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=Kinesis-Data-Generator-Cognito-User&templateURL=https://aws-kdg-tools.s3.us-west-2.amazonaws.com/cognito-setup.yaml

3. Se te abrirá un template de cloudformation ya listo para desplegar, o tambien puedes cargarlo de cero en cloudformation utilizando el template cloudformation https://raw.githubusercontent.com/awslabs/amazon-kinesis-data-generator/mainline/setup/cognito-setup.yaml


