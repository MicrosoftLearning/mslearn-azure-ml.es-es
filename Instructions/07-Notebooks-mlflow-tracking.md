---
lab:
  title: Seguimiento del entrenamiento de modelos en cuadernos con MLflow
---

# Seguimiento del entrenamiento de modelos en cuadernos con MLflow

A menudo, iniciará un nuevo proyecto de ciencia de datos experimentando y entrenando varios modelos. Para realizar un seguimiento del trabajo y mantener una visión general de los modelos que entrena y cómo funcionan, puede usar el seguimiento de MLflow.

En este ejercicio, MLflow se ejecutará en un cuaderno que se ejecuta en una instancia de proceso para registrar el entrenamiento del modelo.

## Antes de empezar

Necesitará una [suscripción de Azure](https://azure.microsoft.com/free) en la que tenga acceso de nivel administrativo.

## Aprovisionar un área de trabajo de Azure Machine Learning

Un *área de trabajo* de Azure Machine Learning proporciona un lugar central para administrar todos los recursos y recursos que necesita para entrenar y administrar los modelos. Puede interactuar con el área de trabajo de Azure Machine Learning a través de Studio, el SDK de Python y la CLI de Azure.

Usará la CLI de Azure para aprovisionar el área de trabajo y el proceso necesario, y usará el SDK de Python para entrenar un modelo de clasificación con aprendizaje automático automatizado.

### Creación del área de trabajo y los recursos de proceso

Para crear el área de trabajo de Azure Machine Learning y una instancia de proceso, usará la CLI de Azure. Todos los comandos necesarios se agrupan en un script de Shell para que se ejecute.
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
    cd azure-ml-labs/Labs/07
    ./setup.sh
    ```

    > Omita los mensajes (error) que digan que las extensiones no se instalaron.

1. Espere a que se complete el script: normalmente tarda entre 5 y 10 minutos.

## Clonación de los materiales de laboratorio

Cuando haya creado el área de trabajo y los recursos de proceso necesarios, puede abrir el Estudio de Azure Machine Learning y clonar los materiales del laboratorio en el área de trabajo.

1. En el Azure Portal, vaya al área de trabajo de Azure Machine Learning denominada **mlw-dp100-...** .
1. Seleccione el área de trabajo de Azure Machine Learning y, en su página **Información general**, seleccione **Iniciar Studio**. Se abrirá otra pestaña en el explorador para abrir el Estudio de Azure Machine Learning.
1. Cierre los elementos emergentes que aparecen en Studio.
1. En el Estudio de Azure Machine Learning, vaya a la página **Proceso** y compruebe que la instancia de proceso que creó en la sección anterior existe. La instancia de proceso debe estar en ejecución.
1. En la pestaña **Instancias de proceso**, busque la instancia de proceso y seleccione la aplicación **Terminal**.
1. En el terminal, instale el SDK de Python y la biblioteca MLflow en la instancia de proceso mediante la ejecución de los siguientes comandos en el terminal:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    pip install mlflow
    ```

    > Omita los mensajes (error) que indiquen que no se han encontrado ni desinstalado los paquetes.

1. Ejecute el siguiente comando para clonar un repositorio de Git que contenga un cuaderno, datos y otros archivos en su área de trabajo:

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Una vez completado el comando, en el panel **Archivos**, haga clic en **&#8635;** para actualizar la vista y compruebe que se ha creado la carpeta **/Users/*su-nombre-de-usuario*/azure-ml-labs**.

## Seguimiento del entrenamiento del modelo con MLflow

Ahora que tiene todos los recursos necesarios, puede ejecutar el cuaderno para configurar y usar MLflow al entrenar modelos en un cuaderno.

1. Abra el cuaderno **Labs/07/Track model training with MLflow.ipynb**.

    > Seleccione **Autenticar** y siga los pasos necesarios si aparece una notificación en la que se le pide que se autentique.

1. Compruebe que el cuaderno usa el kernel de **Python 3.8- AzureML**.
1. Ejecute todas las celdas del cuaderno.
1. Revise el nuevo trabajo que se crea cada vez que entrena un modelo.

## Eliminación de recursos de Azure

Cuando termine de explorar Azure Machine Learning, debe eliminar los recursos que ha creado para evitar costos innecesarios de Azure.

1. Cierre la pestaña Estudio de Azure Machine Learning y vuelva al Azure Portal.
1. En Azure Portal, en la página **Inicio**, seleccione **Grupos de recursos**.
1. Seleccione el grupo de recursos **rg-dp100-...** .
1. En la parte superior de la página **Información general** del grupo de recursos, seleccione **Eliminar grupo de recursos**.
1. Escriba el nombre del grupo de recursos para confirmar que quiere eliminarlo y seleccione **Eliminar**.
