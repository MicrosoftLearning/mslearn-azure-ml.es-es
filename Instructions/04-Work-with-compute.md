---
lab:
  title: Trabajo con recursos de proceso en Azure Machine Learning
---

# Trabajo con recursos de proceso en Azure Machine Learning

Una de las principales ventajas de la nube es la posibilidad de usar recursos de proceso escalables y a petición para el procesamiento rentable de datos de gran tamaño.

En este ejercicio, aprenderá a utilizar el proceso en la nube en Azure Machine Learning para ejecutar experimentos y código de producción a escala.

## Antes de empezar

Necesitará una [suscripción de Azure](https://azure.microsoft.com/free?azure-portal=true) en la que tenga acceso de nivel administrativo.

## Aprovisionar un área de trabajo de Azure Machine Learning

Un *área de trabajo* de Azure Machine Learning proporciona un lugar central para administrar todos los recursos y recursos que necesita para entrenar y administrar los modelos. Puede interactuar con el área de trabajo de Azure Machine Learning a través de Studio, el SDK de Python y la CLI de Azure.

Para crear el área de trabajo de Azure Machine Learning, usará la CLI de Azure. Todos los comandos necesarios se agrupan en un script de Shell para que se ejecute.

1. En un explorador, abra el portal Azure en `https://portal.azure.com/`, iniciando sesión con su cuenta Microsoft.
1. Seleccione el botón \[>_] (*Cloud Shell*) en la parte superior de la página, a la derecha del cuadro de búsqueda. Se abre un panel de Cloud Shell en la parte inferior del portal.
1. Seleccione **Bash** si se le pregunta. La primera vez que abra el shell de la nube, se le pedirá que elija el tipo de shell que desea utilizar (*Bash* o *PowerShell*).
1. Compruebe que se ha especificado la suscripción correcta y seleccione **Crear almacenamiento** si se le pide que cree almacenamiento para el shell de la nube. Espere a que se cree el almacén.
1. Para evitar cualquier conflicto con versiones anteriores, elimine cualquier extensión CLI de ML (tanto de la versión 1 como de la 2) ejecutando este comando en el terminal:

    ```azurecli
    az extension remove -n azure-cli-ml
    az extension remove -n ml
    ```

    > Use `SHIFT + INSERT` para pegar el código copiado en Cloud Shell.

    > Omita los mensajes (error) que digan que las extensiones no se instalaron.

1. Instale la extensión Azure Machine Learning (v2) con el siguiente comando:
    
    ```azurecli
    az extension add -n ml -y
    ```

1. Cree un grupo de recursos. Elija una ubicación cercana a usted.

    ```azurecli
    az group create --name "rg-dp100-labs" --location "eastus"
    ```

1. Creación de un área de trabajo:

    ```azurecli
    az ml workspace create --name "mlw-dp100-labs" -g "rg-dp100-labs"
    ```

1. Espere a que se complete el comando: normalmente tarda entre 5 y 10 minutos.

## Creación del script de configuración del cálculo

Para ejecutar cuadernos en el área de trabajo de Azure Machine Learning, necesitará una instancia de proceso. Puede usar un script de instalación para configurar la instancia de proceso al crearla.

1. En el Azure Portal, vaya al área de trabajo de Azure Machine Learning denominada **mlw-dp100-labs**.
1. Seleccione el área de trabajo de Azure Machine Learning y, en su página **Información general**, seleccione **Iniciar Studio**. Se abrirá otra pestaña en el explorador para abrir el Estudio de Azure Machine Learning.
1. Cierre los elementos emergentes que aparecen en Studio.
1. En el Estudio de Azure Machine Learning, vaya a la página **Cuadernos**.
1. En el panel **Archivos**, seleccione el icono &#10753; para **Agregar archivos**.
1. Seleccione **Create new file** (Crear archivo).
1. Compruebe que la ubicación del archivo sea **Users/* your-user-name***.
1. Cambie el tipo de archivo a **Bash (*.sh)** .
1. Cambie el nombre del archivo a `compute-setup.sh`.
1. Abra el archivo **compute-setup.sh** recién creado y pegue lo siguiente en su contenido:

    ```azurecli
    #!/bin/bash

    # clone repository
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Guarde el archivo **compute-setup.sh**.

## Creación de la instancia de proceso

Para crear la instancia de proceso, puede usar Studio, el SDK de Python o la CLI de Azure. Usará Studio para crear la instancia de proceso con el script de instalación que acaba de crear.

1. Vaya a la página **Proceso** con el menú de la izquierda.
1. En la pestaña **Instancias de proceso**, seleccione **Nuevo**.
1. Configure (aún no cree) la instancia de proceso con las siguientes opciones: 
    - **Nombre del proceso**: *escriba un nombre único*
    - **Tipo de máquina virtual**: *CPU*
    - **Tamaño de máquina virtual**: *Standard_DS11_v2*
1. Seleccione **Siguiente**.
1. Seleccione **Agregar programación** y configure la programación para **detener** la instancia de proceso todos los días a **las 18:00** o **6:00 p.m**.
1. Seleccione **Siguiente**.
1. Revise la configuración de seguridad, pero **no** las seleccione:
    - **Habilitar acceso SSH**: *se puede usar para habilitar el acceso directo a la máquina virtual mediante un cliente SSH.*
    - **Habilitar red virtual**: *normalmente se usaría en un entorno empresarial para mejorar la seguridad de la red.*
    - **Asignar a otro usuario**: *puede utilizarlo para asignar una instancia de cálculo a otro científico de datos.*
1. Seleccione **Siguiente**.
1. Seleccione el botón de alternancia para **Aprovisionamiento con un script de creación**.
1. Seleccione el script **compute-setup.sh** que creó anteriormente.
1. Seleccione **Revisar y crear** para crear la instancia de proceso y esperar a que se inicie y su estado cambie a **En ejecución**.
1. Cuando se ejecute la instancia de proceso, vaya a la página **Cuadernos**. En el panel **Archivos**, haga clic en **&#8635;** para actualizar la vista y comprobar que se ha creado una nueva carpeta **Users/*your-user-name*/dp100-azure-ml-labs**.

## Configuración de la instancia de proceso

Cuando haya creado la instancia de proceso, puede ejecutar cuadernos en ella. Es posible que tenga que instalar determinados paquetes para ejecutar el código que desee. Puede incluir paquetes en el script de instalación o instalarlos mediante el terminal.

1. En la pestaña **Instancias de proceso**, busque la instancia de proceso y seleccione la aplicación **Terminal**.
1. En el terminal, instale el SDK de Python en la instancia de proceso mediante la ejecución de los siguientes comandos en el terminal:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Omita los mensajes (error) que indiquen que los paquetes no se instalaron.

1. Cuando se instalan los paquetes, puede cerrar la pestaña para finalizar el terminal.

## Creación de un clúster de proceso

Los cuadernos son ideales para el desarrollo o el trabajo iterativo durante la experimentación. Al experimentar, querrá ejecutar cuadernos en una instancia de proceso para probar y revisar código rápidamente. Al pasar a producción, querrá ejecutar scripts en un clúster de proceso. Creará un clúster de proceso con el SDK de Python y lo usará para ejecutar un script como trabajo.

1. Abra el cuaderno **Labs/04/Work with compute.ipynb**.

    > Seleccione **Autenticar** y siga los pasos necesarios si aparece una notificación en la que se le pide que se autentique.

1. Compruebe que el cuaderno usa el kernel de **Python 3.8- AzureML**.
1. Ejecute todas las celdas del cuaderno.

## Eliminación de recursos de Azure

Cuando termine de explorar Azure Machine Learning, debe eliminar los recursos que ha creado para evitar costos innecesarios de Azure.

1. Cierre la pestaña Estudio de Azure Machine Learning y vuelva al Azure Portal.
1. En Azure Portal, en la página **Inicio**, seleccione **Grupos de recursos**.
1. Seleccione el grupo de recursos **rg-dp100-labs**.
1. En la parte superior de la página **Información general** del grupo de recursos, seleccione **Eliminar grupo de recursos**.
1. Escriba el nombre del grupo de recursos para confirmar que quiere eliminarlo y seleccione **Eliminar**.
