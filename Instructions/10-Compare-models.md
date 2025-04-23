---
lab:
  title: Creación y exploración del panel de inteligencia artificial responsable
---

# Creación y exploración del panel de inteligencia artificial responsable

Después de entrenar el modelo, querrá evaluar el modelo para explorar si funciona según lo previsto. Junto a las métricas de rendimiento, hay otros factores que puede tener en cuenta. El panel de inteligencia artificial responsable de Azure Machine Learning permite analizar los datos y las predicciones del modelo para identificar cualquier sesgo o imparcialidad.

En este ejercicio, preparará los datos y creará un panel de inteligencia artificial responsable en Azure Machine Learning.

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
    cd azure-ml-labs/Labs/10
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

## Creación de una canalización para evaluar modelos y enviarlos desde un cuaderno

Ahora que tiene todos los recursos necesarios, puede ejecutar el cuaderno para recuperar los componentes de inteligencia artificial responsable integrados, crear una canalización y enviar la canalización para generar un panel de inteligencia artificial responsable.

1. Abra el cuaderno **Labs/10/Create Responsible AI dashboard.ipynb**.

    > Seleccione **Autenticar** y siga los pasos necesarios si aparece una notificación en la que se le pide que se autentique.

1. Compruebe que el cuaderno usa el kernel de **Python 3.8- AzureML**.
1. Ejecute todas las celdas del cuaderno.

## Eliminación de recursos de Azure

Cuando termine de explorar Azure Machine Learning, debe eliminar los recursos que ha creado para evitar costos innecesarios de Azure.

1. Cierre la pestaña Estudio de Azure Machine Learning y vuelva al Azure Portal.
1. En Azure Portal, en la página **Inicio**, seleccione **Grupos de recursos**.
1. Seleccione el grupo de recursos **rg-dp100-...** .
1. En la parte superior de la página **Información general** del grupo de recursos, seleccione **Eliminar grupo de recursos**.
1. Escribe el nombre del grupo de recursos para confirmar que quieres eliminarlo y selecciona **Eliminar**.
