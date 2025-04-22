---
lab:
  title: Hacer que los datos estén disponibles en Azure Machine Learning
---

# Hacer que los datos estén disponibles en Azure Machine Learning

Aunque es bastante habitual trabajar con datos en su sistema de archivos local, en un entorno empresarial puede ser más eficaz almacenar los datos en una ubicación central donde varios científicos de datos e ingenieros de aprendizaje automático puedan acceder a ellos.

En este ejercicio, explorará *almacenes de datos* y *recursos de datos*, que son los objetos principales que se usan para abstraer el acceso a los datos en Azure Machine Learning.

## Antes de empezar

Necesitará una [suscripción de Azure](https://azure.microsoft.com/free?azure-portal=true) en la que tenga acceso de nivel administrativo.

## Aprovisionar un área de trabajo de Azure Machine Learning

Un *área de trabajo* de Azure Machine Learning proporciona un lugar central para administrar todos los recursos y recursos que necesita para entrenar y administrar los modelos. Puede interactuar con el área de trabajo de Azure Machine Learning a través de Studio, el SDK de Python y la CLI de Azure.

Usará un script de Shell que usa la CLI de Azure para aprovisionar el área de trabajo y los recursos necesarios. A continuación, usará el Diseñador en el Estudio de Azure Machine Learning para entrenar y comparar modelos.

### Creación del área de trabajo y los recursos de proceso

Para crear el área de trabajo de Azure Machine Learning y los recursos de proceso, utilizará la CLI de Azure. Todos los comandos necesarios se agrupan en un script de Shell para que se ejecute.

1. En un explorador, abra el portal Azure en `https://portal.azure.com/`, iniciando sesión con su cuenta Microsoft.
1. Seleccione el botón \[>_] (*Cloud Shell*) en la parte superior de la página, a la derecha del cuadro de búsqueda. Se abre un panel de Cloud Shell en la parte inferior del portal.
1. Seleccione **Bash** si se le pregunta. La primera vez que abra el shell de la nube, se le pedirá que elija el tipo de shell que desea utilizar (*Bash* o *PowerShell*).
1. Comprueba que se ha especificado la suscripción correcta y que se ha seleccionado **No se requiere ninguna cuenta de almacenamiento**. Seleccione **Aplicar**.
1. Escriba los siguientes comandos en el terminal para clonar este repositorio:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Use `SHIFT + INSERT` para pegar el código copiado en Cloud Shell.

1. Escriba los comandos siguientes después de clonar el repositorio para cambiar a la carpeta de este laboratorio y ejecute el script **setup.sh** que contiene:

    ```azurecli
    cd azure-ml-labs/Labs/03
    ./setup.sh
    ```

    > Omita los mensajes (error) que digan que las extensiones no se instalaron.

1. Espere a que se complete el script: normalmente tarda entre 5 y 10 minutos.

    <details>
    <summary><b>Sugerencia para solucionar problemas</b>: error de creación del área de trabajo</summary><br>
    <p>Si recibes un error al ejecutar el script de instalación a través de la CLI, debes aprovisionar los recursos manualmente:</p>
    <ol>
        <li>En la página principal de Azure Portal, selecciona <b>+Crear un recurso</b>.</li>
        <li>Busca <i>aprendizaje automático</i> y, después, selecciona <b>Azure Machine Learning</b>. Seleccione <b>Crear</b>.</li>
        <li>Cree un recurso de Azure Machine Learning con la siguiente configuración: <ul>
                <li><b>Suscripción</b>: <i>suscripción de Azure</i></li>
                <li><b>Grupo de recursos</b>: rg-dp100-labs</li>
                <li><b>Nombre del área de trabajo</b>: mlw-dp100-labs</li>
                <li><b>Región</b>: <i>seleccione la región geográfica más cercana</i>.</li>
                <li><b>Cuenta de almacenamiento</b>: <i>tenga en cuenta la nueva cuenta de almacenamiento predeterminada que se creará para el área de trabajo</i>.</li>
                <li><b>Almacén de claves</b>: <i>tenga en cuenta el nuevo almacén de claves predeterminado que se creará para el área de trabajo</i>.</li>
                <li><b>Application Insights</b>: <i>tenga en cuenta el nuevo recurso de Application Insights predeterminado que se creará para el área de trabajo</i>.</li>
                <li><b>Registro de contenedor</b>: ninguno (<i>se creará uno automáticamente la primera vez que implemente un modelo en un contenedor</i>).</li>
            </ul>
        <li>Selecciona <b>Revisar y crear</b> y espera a que se cree el área de trabajo y sus recursos asociados: normalmente tarda unos 5 minutos.</li>
        <li>Selecciona <b>Ir al recurso</b> y en su página <b>Información general</b>, selecciona <b>Iniciar Studio</b>. Se abrirá otra pestaña en el explorador para abrir el Estudio de Azure Machine Learning.</li>
        <li>Cierre los elementos emergentes que aparecen en Studio.</li>
        <li>En el Estudio de Azure Machine Learning, ve a la página <b>Proceso</b> y selecciona <b>+Nuevo</b> en la pestaña <b>Instancias de proceso</b>.</li>
        <li>Asigna un nombre único a la instancia de proceso y, a continuación, selecciona <b>Standard_DS11_v2</b> como tamaño de máquina virtual.</li>
        <li>Seleccione <b>Revisar y crear</b> y luego <b>Crear</b>.</li>
        <li>A continuación, selecciona la pestaña <b>Clústeres de proceso</b> y selecciona <b>+ Nuevo</b>.</li>
        <li>Elige la misma región en la que creaste el área de trabajo y, a continuación, selecciona <b>Standard_DS11_v2</b> como tamaño de máquina virtual. Seleccione <b>Siguiente</b>.</li>
        <li>Asigna al clúster un nombre único y, a continuación, selecciona <b>Crear</b>.</li>
    </ol>
    </details>

## Explorar los almacenes de datos predeterminados

Al crear un área de trabajo de Azure Machine Learning, se crea automáticamente una cuenta de almacenamiento y se conecta al área de trabajo. Explorará cómo está conectada la cuenta de almacenamiento.

1. En Azure Portal, vaya al nuevo grupo de recursos denominado **rg-dp100-...**.
1. Seleccione la cuenta de almacenamiento en el grupo de recursos. El nombre suele comenzar con el nombre que proporcionó para el área de trabajo (sin guiones).
1. Revise la página **Información general** de la cuenta de almacenamiento. Tenga en cuenta que la cuenta de almacenamiento tiene varias opciones para **Almacenamiento de datos**, como se muestra en el panel Información general y en el menú izquierdo.
1. Seleccione **Contenedores** para explorar la parte de Blob Storage de la cuenta de almacenamiento.
1. Tenga en cuenta el contenedor **azureml-blobstore-...** . El almacén de datos predeterminado para los recursos de datos usa este contenedor para almacenar datos.
1. Usando el botón &#43; **Contenedor** de la parte superior de la pantalla, cree un nuevo contenedor y nómbrelo `training-data`.
1. Seleccione **Recursos compartidos** de archivos en el menú izquierdo para explorar la parte Recurso compartido de archivos de la cuenta de almacenamiento.
1. Observe el **code-...** de archivo compartido. Los cuadernos del área de trabajo se almacenan aquí. Después de clonar los materiales de laboratorio, puede encontrar los archivos de este recurso compartido de archivos en la carpeta **code-.../Users/*your-user-name*/azure-ml-labs**.

## Copia de la clave de acceso

Para crear un almacén de datos en el área de trabajo de Azure Machine Learning, debe proporcionar algunas credenciales. Una manera sencilla de proporcionar al área de trabajo acceso a Blob storage es usar la clave de cuenta.

1. En la cuenta de almacenamiento, seleccione la pestaña **Claves de acceso** en el menú de la izquierda.
1. Tenga en cuenta que se proporcionan dos claves: key1 y key2. Cada clave tiene la misma funcionalidad. 
1. Seleccione **Mostrar** para el campo **Clave** en **key1**.
1. Copie el valor del campo **Clave** en un Bloc de notas. Tendrá que pegar este valor en el cuaderno más adelante.
1. Copie el nombre de la cuenta de almacenamiento en la parte superior de la página. El nombre debe comenzar por **mlwdp100storage...** Tendrá que pegar este valor en el cuaderno más adelante.

> **Nota**: Copie la clave y el nombre de la cuenta en un Bloc de notas para evitar mayúsculas automáticas (lo que sucede en Word). La clave distingue mayúsculas de minúsculas.

## Clonación de los materiales de laboratorio

Para crear un almacén de datos y recursos de datos con el SDK de Python, deberá clonar los materiales del laboratorio en el área de trabajo.

1. En el Azure Portal, vaya al área de trabajo de Azure Machine Learning denominada **mlw-dp100-labs**.
1. Seleccione el área de trabajo de Azure Machine Learning y, en su página **Información general**, seleccione **Iniciar Studio**. Se abrirá otra pestaña en el explorador para abrir el Estudio de Azure Machine Learning.
1. Cierre los elementos emergentes que aparecen en Studio.
1. En el Estudio de Azure Machine Learning, vaya a la página **Proceso** y compruebe que la instancia de proceso y el clúster que creó en la sección anterior existen. La instancia de proceso debe estar en ejecución, el clúster debe estar inactivo y tener 0 nodos en ejecución.
1. En la pestaña **Instancias de proceso**, busque la instancia de proceso y seleccione la aplicación **Terminal**.
1. En el terminal, instale el SDK de Python en la instancia de proceso mediante la ejecución de los siguientes comandos en el terminal:

    ```azurecli
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    pip install mltable
    ```

    > Omita los mensajes (error) que indiquen que los paquetes no se instalaron.

1. Ejecute el siguiente comando para clonar un repositorio de Git que contenga cuadernos, datos y otros archivos en su área de trabajo:

    ```azurecli
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Una vez completado el comando, en el panel **Archivos**, haga clic en **&#8635;** para actualizar la vista y compruebe que se ha creado la carpeta **/Users/*su-nombre-de-usuario*/azure-ml-labs**.

**Opcionalmente**, en otra pestaña del navegador, navegue de nuevo al portal de [Azure](https://portal.azure.com?azure-portal=true). Explore de nuevo el recurso compartido de archivos **code-...** en la cuenta de almacenamiento para encontrar los materiales de laboratorio clonados en la carpeta **azure-ml-labs** recién creada.

## Creación de un almacén de datos y recursos de datos

El código para crear un almacén de datos y recursos de datos con el SDK de Python se proporciona en un cuaderno.

1. Abra el cuaderno **Labs/03/Work with data.ipynb**.

    > Seleccione **Autenticar** y siga los pasos necesarios si aparece una notificación en la que se le pide que se autentique.

1. Compruebe que el cuaderno usa el kernel de **Python 3.8- AzureML**.
1. Ejecute todas las celdas del cuaderno.

## Opcional: Exploración de los recursos de datos

**Opcionalmente**, puede explorar cómo se almacenan los recursos de datos en la cuenta de almacenamiento asociada.

1. Vaya a la pestaña **Datos** de Estudio de Azure Machine Learning para explorar los recursos de datos.
1. Seleccione el nombre del recurso de datos **diabetes-local** para explorar sus detalles. 

    En **Orígenes de datos** para el recurso de datos **diabetes-local**, encontrará dónde se ha cargado el archivo. La ruta de acceso que comienza por **LocalUpload/...** muestra la ruta de acceso dentro del contenedor de la cuenta de almacenamiento **azureml-blobstore-...** . Para comprobar que el archivo existe, vaya a esa ruta de acceso en el Azure Portal.

## Eliminación de recursos de Azure

Cuando termine de explorar Azure Machine Learning, debe eliminar los recursos que ha creado para evitar costos innecesarios de Azure.

1. Cierre la pestaña Estudio de Azure Machine Learning y vuelva al Azure Portal.
1. En Azure Portal, en la página **Inicio**, seleccione **Grupos de recursos**.
1. Seleccione el grupo de recursos **rg-dp100-...** .
1. En la parte superior de la página **Información general** del grupo de recursos, seleccione **Eliminar grupo de recursos**.
1. Escribe el nombre del grupo de recursos para confirmar que quieres eliminarlo y selecciona **Eliminar**.
