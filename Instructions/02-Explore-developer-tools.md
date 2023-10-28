---
lab:
  title: Exploración de las herramientas de desarrollo para la interacción de áreas de trabajo
---

# Exploración de las herramientas de desarrollo para la interacción de áreas de trabajo

Puede usar varias herramientas para interactuar con el espacio de trabajo de Azure Machine Learning. Dependiendo de la tarea que necesite realizar y de su preferencia para la herramienta de desarrollo, puede elegir qué herramienta usar cuando. Este laboratorio está diseñado como introducción a las herramientas de desarrollo que se suelen usar para la interacción del área de trabajo. Si quiere aprender a usar una herramienta específica en mayor profundidad, hay otros laboratorios que explorar.

## Antes de empezar

Necesitará una [suscripción de Azure](https://azure.microsoft.com/free?azure-portal=true) en la que tenga acceso de nivel administrativo.

Las herramientas de desarrollo más usadas para interactuar con el área de trabajo de Azure Machine Learning son:

- **CLI de Azure** con la extensión de Azure Machine Learning: este enfoque de línea de comandos es ideal para la automatización de la infraestructura.
- **Estudio de Azure Machine Learning**: use la interfaz de usuario fácil de usar para explorar el área de trabajo y todas sus funcionalidades.
- **SDK de Python** para Azure Machine Learning: use para enviar trabajos y administrar modelos desde un cuaderno de Jupyter Notebook, ideal para científicos de datos.

Explorará cada una de estas herramientas para tareas que normalmente se realizan con esa herramienta.

## Aprovisionamiento de la infraestructura con la CLI de Azure

Para que un científico de datos entrene un modelo de aprendizaje automático con Azure Machine Learning, deberá configurar la infraestructura necesaria. Puede usar la CLI de Azure con la extensión de Azure Machine Learning para crear un área de trabajo y recursos de Azure Machine Learning, como una instancia de proceso.

Para empezar, abra azure Cloud Shell, instale la extensión de Azure Machine Learning y clone el repositorio de Git.

1. En un explorador, abra el portal Azure en `https://portal.azure.com/`, iniciando sesión con su cuenta Microsoft.
1. Seleccione el botón \[>_] (*Cloud Shell*) en la parte superior de la página, a la derecha del cuadro de búsqueda. Se abre un panel de Cloud Shell en la parte inferior del portal.
1. Seleccione **Bash** si se le pregunta. La primera vez que abra el shell de la nube, se le pedirá que elija el tipo de shell que desea utilizar (*Bash* o *PowerShell*).
1. Compruebe que se ha especificado la suscripción correcta y seleccione **Crear almacenamiento** si se le pide que cree almacenamiento para el shell de la nube. Espere a que se cree el almacén.
1. Quite las extensiones de la CLI de ML (tanto la versión 1 como la 2) para evitar conflictos con versiones anteriores con este comando:
    
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

1. Espere a que se cree el área de trabajo y sus recursos asociados: normalmente tarda unos 5 minutos.

## Crear una instancia de computación con la CLI de Azure

Otra parte importante de la infraestructura necesaria para entrenar un modelo de aprendizaje automático es el **proceso**. Aunque puede entrenar modelos localmente, es más escalable y rentable usar el proceso en la nube.

Cuando los científicos de datos desarrollan un modelo de aprendizaje automático en el área de trabajo de Azure Machine Learning, quieren usar una máquina virtual en la que pueden ejecutar cuadernos de Jupyter Notebook. Para el desarrollo, una **instancia de proceso** es una opción ideal.

Después de crear un área de trabajo de Azure Machine Learning, también puede crear una instancia de proceso mediante la CLI de Azure.

En este ejercicio, creará una instancia de proceso con la siguiente configuración:

- **Nombre del proceso**: *nombre de la instancia de cálculo. Debe ser único y tener menos de 24 caracteres.*
- **Tamaño de máquina virtual**: STANDARD_DS11_V2
- **Tipo de proceso** (instancia o clúster): ComputeInstance
- **Nombre del área de trabajo de Azure Machine Learning**: mlw-dp100-labs
- **Grupo de recursos**: rg-dp100-labs

1. Use el siguiente comando para crear una instancia de proceso en el área de trabajo. Si el nombre de la instancia de proceso contiene "XXXX", reemplácelo por números aleatorios para crear un nombre único.

    ```azurecli
    az ml compute create --name "ciXXXX" --size STANDARD_DS11_V2 --type ComputeInstance -w mlw-dp100-labs -g rg-dp100-labs
    ```

    Si recibe un mensaje de error que indica que ya existe una instancia de proceso con el nombre, cambie el nombre y vuelva a intentar el comando.

## Creación de un clúster de proceso con la CLI de Azure

Aunque una instancia de proceso es ideal para el desarrollo, un clúster de proceso es más adecuado cuando queremos entrenar modelos de aprendizaje automático. Solo cuando se envía un trabajo para usar el clúster de proceso, cambiará el tamaño a más de 0 nodos y ejecutará el trabajo. Una vez que el clúster de proceso ya no sea necesario, cambiará automáticamente el tamaño a 0 nodos para minimizar los costos. 

Para crear un clúster de proceso, puede usar la CLI de Azure, de forma similar a la creación de una instancia de proceso.

Creará un clúster de proceso con la siguiente configuración:

- **Nombre del proceso**: aml-cluster
- **Tamaño de máquina virtual**: STANDARD_DS11_V2
- **Tipo de proceso**: AmlCompute *(crea un clúster de proceso)*
- **Número máximo de instancias**: *número máximo de nodos*
- **Nombre del área de trabajo de Azure Machine Learning**: mlw-dp100-labs
- **Grupo de recursos**: rg-dp100-labs

1. Use el siguiente comando para crear un clúster de proceso en el área de trabajo.
    
    ```azurecli
    az ml compute create --name "aml-cluster" --size STANDARD_DS11_V2 --max-instances 2 --type AmlCompute -w mlw-dp100-labs -g rg-dp100-labs
    ```

## Configuración de la estación de trabajo con el Estudio de Azure Machine Learning

Aunque la CLI de Azure es ideal para la automatización, es posible que desee revisar la salida de los comandos que ha ejecutado. Puede usar el Estudio de Azure Machine Learning para comprobar si se han creado recursos y activos y comprobar si los trabajos se ejecutaron correctamente o revisar por qué se produjo un error en un trabajo. 

1. En el Azure Portal, vaya al área de trabajo de Azure Machine Learning denominada **mlw-dp100-labs**.
1. Seleccione el área de trabajo de Azure Machine Learning y, en su página **Información general**, seleccione **Iniciar Studio**. Se abrirá otra pestaña en el explorador para abrir el Estudio de Azure Machine Learning.
1. Cierre los elementos emergentes que aparecen en Studio.
1. En el Estudio de Azure Machine Learning, vaya a la página **Proceso** y compruebe que la instancia de proceso y el clúster que creó en la sección anterior existen. La instancia de proceso debe estar en ejecución, el clúster debe estar inactivo y tener 0 nodos en ejecución.

## Use el SDK de Python para entrenar un modelo

Ahora que ha comprobado que se ha creado el proceso necesario, puede usar el SDK de Python para ejecutar un script de entrenamiento. Instalará y usará el SDK de Python en la instancia de proceso y entrenará el modelo de aprendizaje automático en el clúster de proceso.

1. Seleccione la aplicación **Terminal** de la **instancia de proceso** para iniciar el terminal.
1. En el terminal, instale el SDK de Python en la instancia de proceso mediante la ejecución de los siguientes comandos en el terminal:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Omita los mensajes (error) que indiquen que los paquetes no se instalaron.

1. Ejecute el siguiente comando para clonar un repositorio de Git que contenga cuadernos, datos y otros archivos en su área de trabajo:

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Una vez completado el comando, en el panel **Archivos**, seleccione **&#8635;** para actualizar la vista y compruebe que se ha creado la carpeta **/Users/*su-nombre-de-usuario*/azure-ml-labs**.
1. Abra el cuaderno **Labs/02/Run training script.ipynb**.

    > Seleccione **Autenticar** y siga los pasos necesarios si aparece una notificación en la que se le pide que se autentique.

1. Compruebe que el cuaderno usa el kernel de **Python 3.8- AzureML**. Cada kernel tiene su propia imagen con su propio conjunto de paquetes preinstalados.
1. Ejecute todas las celdas del cuaderno.

Se creará un nuevo trabajo en el área de trabajo de Azure Machine Learning. El trabajo realiza un seguimiento de las entradas definidas en la configuración del trabajo, el código usado y las salidas como métricas para evaluar el modelo.

## Revise el historial de trabajos en el Estudio de Azure Machine Learning

Al enviar un trabajo al área de trabajo de Azure Machine Learning, puede revisar su estado en el Estudio de Azure Machine Learning.

1. Seleccione la dirección URL del trabajo proporcionada como salida en el cuaderno o vaya a la página **Trabajos** del Estudio de Azure Machine Learning.
1. Se enumera un nuevo experimento denominado **diabetes-training**. Seleccione el trabajo más reciente **diabetes-pythonv2-train**.
1. Revise las **Propiedades** del trabajo. Anote el **Estado**del trabajo:
    - **En cola**: el trabajo está esperando que el proceso esté disponible.
    - **En preparación**: el clúster de proceso está cambiando de tamaño o se está instalando el entorno en el destino de proceso.
    - **En ejecución**: se ejecuta el script de entrenamiento.
    - **Finalización**: el script de entrenamiento se ejecutó y el trabajo se está actualizando con toda la información final.
    - **Completado**: el trabajo se completó correctamente y finaliza.
    - **Error**: error en el trabajo y finalizado.
1. En **Salidas y registros**, encontrará la salida del script en **user_logs/std_log.txt**. Las salidas de las instrucciones de **impresión** del script se mostrarán aquí. Si hay un error debido a un problema con el script, también encontrará el mensaje de error aquí.
1. En **Código**, encontrará la carpeta que especificó en la configuración del trabajo. Esta carpeta incluye el script de entrenamiento y el conjunto de datos.

## Eliminación de recursos de Azure

Cuando termine de explorar Azure Machine Learning, debe eliminar los recursos que ha creado para evitar costos innecesarios de Azure.

1. Cierre la pestaña Estudio de Azure Machine Learning y vuelva al Azure Portal.
1. En Azure Portal, en la página **Inicio**, seleccione **Grupos de recursos**.
1. Seleccione el grupo de recursos **rg-dp100-labs**.
1. En la parte superior de la página **Información general** del grupo de recursos, seleccione **Eliminar grupo de recursos**. 
1. Escriba el nombre del grupo de recursos para confirmar que quiere eliminarlo y seleccione **Eliminar**.
