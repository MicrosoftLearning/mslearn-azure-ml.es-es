---
lab:
  title: Ejecución de un script de entrenamiento como un trabajo de comando en Azure Machine Learning
---

# Ejecución de un script de entrenamiento como un trabajo de comando en Azure Machine Learning

Un cuaderno es ideal para la experimentación y el desarrollo. Una vez que haya desarrollado un modelo de aprendizaje automático y esté listo para producción, querrá entrenarlo con un script. Puede ejecutar un script como un trabajo de comandos.

En este ejercicio, probará un script y, a continuación, lo ejecutará como un trabajo de comando.

## Antes de empezar

Necesitará una [suscripción de Azure](https://azure.microsoft.com/free?azure-portal=true) en la que tenga acceso de nivel administrativo.

## Aprovisionar un área de trabajo de Azure Machine Learning

Un *área de trabajo* de Azure Machine Learning proporciona un lugar central para administrar todos los recursos y recursos que necesita para entrenar y administrar los modelos. Puede interactuar con el área de trabajo de Azure Machine Learning a través de Studio, el SDK de Python y la CLI de Azure.

Usará la CLI de Azure para aprovisionar el área de trabajo y el proceso necesario, y usará el SDK de Python para ejecutar un trabajo de comando.

### Creación del área de trabajo y los recursos de proceso

Para crear el área de trabajo de Azure Machine Learning, una instancia de proceso y un clúster de proceso, usará la CLI de Azure. Todos los comandos necesarios se agrupan en un script de Shell para que se ejecute.

1. En un explorador, abra el portal Azure en `https://portal.azure.com/`, iniciando sesión con su cuenta Microsoft.
1. Seleccione el botón \[>_] (*Cloud Shell*) en la parte superior de la página, a la derecha del cuadro de búsqueda. Se abre un panel de Cloud Shell en la parte inferior del portal.
1. Seleccione **Bash** si se le pregunta. La primera vez que abra el shell de la nube, se le pedirá que elija el tipo de shell que desea utilizar (*Bash* o *PowerShell*).
1. Comprueba que se ha especificado la suscripción correcta y que se ha seleccionado **No se requiere ninguna cuenta de almacenamiento**. Seleccione **Aplicar**.
1. En el terminal, escriba los siguientes comandos para clonar este repositorio:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Use `SHIFT + INSERT` para pegar el código copiado en Cloud Shell.

1. Una vez clonado el repositorio, escriba los siguientes comandos para cambiar a la carpeta de este laboratorio y ejecute el script **setup.sh** que contiene:
    
    ```azurecli
    cd azure-ml-labs/Labs/08
    ./setup.sh
    ```

    > Omita los mensajes (error) que digan que las extensiones no se instalaron.

1. Espere a que se complete el script: normalmente tarda entre 5 y 10 minutos.

## Clonación de los materiales de laboratorio

Cuando haya creado el área de trabajo y los recursos de proceso necesarios, puede abrir el Estudio de Azure Machine Learning y clonar los materiales del laboratorio en el área de trabajo.

1. En el Azure Portal, vaya al área de trabajo de Azure Machine Learning denominada **mlw-dp100-...** .
1. Seleccione el área de trabajo de Azure Machine Learning y, en su página **Información general**, seleccione **Iniciar Studio**. Se abrirá otra pestaña en el explorador para abrir el Estudio de Azure Machine Learning.
1. Cierre los elementos emergentes que aparecen en Studio.
1. En el Estudio de Azure Machine Learning, vaya a la página **Proceso** y compruebe que la instancia de proceso y el clúster que creó en la sección anterior existen. La instancia de proceso debe estar en ejecución, el clúster debe estar inactivo y tener 0 nodos en ejecución.
1. En la pestaña **Instancias de proceso**, busque la instancia de proceso y seleccione la aplicación **Terminal**.
1. En el terminal, instale el SDK de Python en la instancia de proceso mediante la ejecución de los siguientes comandos en el terminal:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Omita los mensajes (error) que indiquen que no se han encontrado ni desinstalado los paquetes.

1. Ejecute el siguiente comando para clonar un repositorio de Git que contenga cuadernos, datos y otros archivos en su área de trabajo:

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Una vez completado el comando, en el panel **Archivos**, haga clic en **&#8635;** para actualizar la vista y compruebe que se ha creado la carpeta **/Users/*su-nombre-de-usuario*/azure-ml-labs**.

## Conversión de un cuaderno en un script

El uso de un cuaderno asociado a una instancia de proceso es ideal para la experimentación y el desarrollo, ya que le permite ejecutar código inmediatamente que ha escrito y revisar su salida. Para pasar de desarrollo a producción, querrá usar scripts. Como primer paso, puede usar el Estudio de Azure Machine Learning para convertir el cuaderno en un script.

1. Abra el cuaderno **Labs/08/src/Train classification model.ipynb**.

    > Seleccione **Autenticar** y siga los pasos necesarios si aparece una notificación en la que se le pide que se autentique.

1. Compruebe que el cuaderno usa el kernel de **Python 3.8- AzureML**.
1. Ejecute todas las celdas para explorar el código y entrenar un modelo.
1. Seleccione el icono de &#9776; en la parte superior del cuaderno para ver el **menú del cuaderno**.
1. Expanda **Exportar como** y seleccione **Python (.py)** para convertir el cuaderno en un script de Python.
1. Asigne el nombre `train-classification-model` al archivo nuevo.
1. Una vez creado el nuevo archivo, el script debe abrirse automáticamente. Explore el archivo y observe que contiene el mismo código que el cuaderno.
1. Seleccione el icono de &#9655;&#9655; en la parte superior del cuaderno para **guardar y ejecutar el script en el terminal**.
1. El script lo inicia el comando **python train-classification-model.py** y la salida debe mostrarse debajo del comando.

   > **Nota:** si el script devuelve importError para libstdc++6, ejecuta los siguientes comandos en el terminal antes de volver a ejecutar el script:
   > ```bash
   > sudo add-apt-repository ppa:ubuntu-toolchain-r/test
   > sudo apt-get update
   > sudo apt-get upgrade libstdc++6
   > ```

## Prueba de un script con el terminal

Después de convertir un cuaderno en un script, puede que desee refinarlo aún más. Un procedimiento recomendado al trabajar con scripts es usar funciones. Cuando el script consta de funciones, es más fácil realizar pruebas unitarias del código. Al usar funciones, el script constará de bloques de código, cada bloque que realiza una tarea específica.

1. Abra el script **Labs/08/src/train-model-parameters.py** y explore su contenido.
    Tenga en cuenta que hay una función principal que incluye cuatro otras funciones:

    - Lectura de datos
    - División de los datos
    - Entrenamiento de un modelo
    - Evaluación de modelo

    Después de la función principal, se define cada función. Observe cómo cada función define la entrada y salida esperadas.

1. Seleccione el icono de &#9655;&#9655; en la parte superior del cuaderno para **guardar y ejecutar el script en el terminal**. Debería obtener un error después de **Lectura de datos...** indicando que no pudo obtener los datos porque la ruta de acceso del archivo no era válida.

    Los scripts permiten parametrizar el código para cambiar fácilmente los datos o parámetros de entrada. En este caso, el script espera un parámetro de entrada para la ruta de acceso de datos que no se ha proporcionado. Puede encontrar los parámetros definidos y esperados al final del script en la función **parse_args()** .

    Hay dos parámetros de entrada definidos:
    - **--training_data** que espera una cadena.
    - **--reg_rate** que espera un número, pero tiene un valor predeterminado de 0,01.

    Para ejecutar correctamente el script, deberá especificar el valor de los parámetros de datos de entrenamiento. Para ello, consulte el archivo **dediabetes.csv** que se almacena en la misma carpeta que el script de entrenamiento.

1. En el terminal, ejecute los siguientes comandos:

    ```
    cd azure-ml-labs/Labs/08/src/
    python train-model-parameters.py --training_data diabetes.csv
    ```

El script debe ejecutarse correctamente y, como resultado, la salida debe mostrar la precisión y la AUC del modelo entrenado.

Probar el script en el terminal es ideal para comprobar si el script funciona según lo previsto. Si hay algún problema con el código, recibirá un error en el terminal.

**Opcionalmente**, edite el código para forzar un error y vuelva a ejecutar el comando en el terminal para ejecutar el script. Por ejemplo, elimine la línea **import pandas as pd**, guarde y ejecute el script con el parámetro de entrada para revisar el mensaje de error.

## Ejecución de un script como trabajo de comando

Si sabe que el script funciona, puede ejecutarlo como un trabajo de comando. Al ejecutar el script como un trabajo de comando, podrá realizar un seguimiento de todas las entradas y salidas del script.

1. Abra el cuaderno **Labs/08/Run script as command job.ipynb**.
1. Ejecute todas las celdas del cuaderno.
1. En el Estudio Azure Machine Learning, navegue hasta la página **Trabajos**.
1. Vaya al trabajo **diabetes-train-script** para explorar la información general del trabajo de comando que ejecutó.
1. Navegue hasta la pestaña **Código**. Todo el contenido de la carpeta **src**, que era el valor del parámetro **code** del comando de trabajo, se copia aquí. Puede revisar el script de entrenamiento que ejecutó el trabajo de comando.
1. Navegue hasta la pestaña **Salidas + registros**.
1. Abra el archivo **std_log.txt** y explore su contenido. El contenido de este archivo es la salida del comando. Recuerde que la misma salida se mostró en el terminal al probar el script allí. Si el trabajo no se realiza correctamente debido a un problema con el script, el mensaje de error se mostrará aquí.

**Opcionalmente**, edite el código para forzar un error y use el cuaderno para volver a iniciar el trabajo de comando. Por ejemplo, quite la línea **import pandas as pd** del script y guarde el script. O bien, edite la configuración del trabajo de comando para explorar los mensajes de error cuando algo está mal con la propia configuración del trabajo en lugar del script.

## Eliminación de recursos de Azure

Cuando termine de explorar Azure Machine Learning, debe eliminar los recursos que ha creado para evitar costos innecesarios de Azure.

1. Cierre la pestaña Estudio de Azure Machine Learning y vuelva al Azure Portal.
1. En Azure Portal, en la página **Inicio**, seleccione **Grupos de recursos**.
1. Seleccione el grupo de recursos **rg-dp100-...** .
1. En la parte superior de la página **Información general** del grupo de recursos, seleccione **Eliminar grupo de recursos**.
1. Escribe el nombre del grupo de recursos para confirmar que quieres eliminarlo y selecciona **Eliminar**.
