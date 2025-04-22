---
lab:
  title: Entrene un modelo con el diseñador de Azure Machine Learning
---

# Entrene un modelo con el diseñador de Azure Machine Learning

El Diseñador de Azure Machine Learning proporciona una interfaz de arrastrar y colocar con la que puede definir un flujo de trabajo. Puede crear un flujo de trabajo para entrenar un modelo, probar y comparar varios algoritmos con facilidad.

En este ejercicio, usará el Diseñador para entrenar y comparar rápidamente dos algoritmos de clasificación.

## Antes de empezar

Necesitará una [suscripción de Azure](https://azure.microsoft.com/free?azure-portal=true) en la que tenga acceso de nivel administrativo.

## Aprovisionar un área de trabajo de Azure Machine Learning

Un *área de trabajo* de Azure Machine Learning proporciona un lugar central para administrar todos los recursos y recursos que necesita para entrenar y administrar los modelos. Puede interactuar con el área de trabajo de Azure Machine Learning a través de Studio, el SDK de Python y la CLI de Azure.

Usará un script de Shell que usa la CLI de Azure para aprovisionar el área de trabajo y los recursos necesarios. A continuación, usará el Diseñador en el Estudio de Azure Machine Learning para entrenar y comparar modelos.

### Creación del área de trabajo y el clúster de proceso

Para crear el área de trabajo de Azure Machine Learning y un clúster de proceso, usará la CLI de Azure. Todos los comandos necesarios se agrupan en un script de Shell para que se ejecute.

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

1. Una vez clonado el repositorio, escriba los siguientes comandos para cambiar a la carpeta de este laboratorio y ejecute el script setup.sh que contiene:

    ```azurecli
    cd azure-ml-labs/Labs/05
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

## Configuración de una nueva canalización

Cuando haya creado el área de trabajo y el clúster de proceso necesario, puede abrir el Estudio de Azure Machine Learning y crear una canalización de entrenamiento con el Diseñador.

1. En el Azure Portal, vaya al área de trabajo de Azure Machine Learning denominada **mlw-dp100-...** .
1. Seleccione el área de trabajo de Azure Machine Learning y, en su página **Información general**, seleccione **Iniciar Studio**. Se abrirá otra pestaña en el explorador para abrir el Estudio de Azure Machine Learning.
1. Cierre los elementos emergentes que aparecen en Studio.
1. En el Estudio de Azure Machine Learning, vaya a la página **Proceso** y compruebe que el clúster de proceso que creó en la sección anterior existen. El clúster debe estar inactivo y tener 0 nodos en ejecución.
1. Vaya a la página **Diseñador**.
1. Seleccione la pestaña **Personalizar** en la parte superior de la página.
1. Cree una canalización vacía mediante componentes personalizados.
1. Cambie el nombre de canalización predeterminado (**Pipeline-Created-on-* date***) a `Train-Diabetes-Classifier` al seleccionar el icono de lápiz de su derecha.


## Creación de una canalización

Para entrenar un modelo, necesitará datos. Puede usar cualquier dato almacenado en un almacén de datos o usar una dirección URL accesible públicamente.

1. En el menú de la izquierda, seleccione la pestaña **Datos**.
1. Arrastre y suelte el componente **capeta-diabetes** al lienzo.

    Ahora que tiene los datos, puede continuar creando una canalización mediante componentes personalizados que ya existen en el área de trabajo (se crearon automáticamente durante la instalación).

1. En el menú izquierdo, seleccione la pestaña **Componentes**.
1. Arrastre y coloque el componente **Quitar filas vacías** en el lienzo, debajo de **carpeta-diabetes.**
1. Conecte la salida de los datos a la entrada del nuevo componente.
1. Arrastre y coloque el componente **Normalizar columnas numéricas** en el lienzo, debajo de **Quitar filas vacías**.
1. Conecte la salida del componente anterior a la entrada del nuevo componente.
1. Arrastre y suelte el componente **Entrenar un modelo clasificador de árbol de decisión** en el lienzo, debajo de **Normalizar columnas numéricas**.
1. Conecte la salida del componente anterior a la entrada del nuevo componente.
1. Seleccione **Configurar y enviar** y, en la página **Configurar trabajo de canalización**, cree un nuevo experimento y asígnele el nombre `diabetes-designer-pipeline`. Después, seleccione **Siguiente**.
1. En **Entradas y salidas**, no realice ningún cambio y seleccione **Siguiente**.
1. En **Configuración del entorno de ejecución**, seleccione **Clúster de proceso** y, en **Seleccionar clúster de proceso de Azure ML**, seleccione el clúster *aml-cluster*.
1. Seleccione **Revisar y enviar** y, después, seleccione **Enviar** para iniciar la ejecución de la canalización.
1. Para comprobar el estado de la ejecución, vaya a la página **Canalizaciones** y seleccione la canalización **Train-Diabetes-Classifier**.
1. Espere hasta que todos los componentes se hayan completado correctamente.

    El envío del trabajo inicializará el clúster de proceso. Como el clúster de proceso estaba inactivo hasta ahora, puede tardar algún tiempo en cambiar el tamaño del clúster a más de 0 nodos. Una vez que el clúster haya cambiado de tamaño, se iniciará automáticamente la ejecución de la canalización.

Podrá realizar un seguimiento de la ejecución de cada componente. Cuando se produce un error en la canalización, podrá explorar qué componente produjo un error y por qué se produjo un error. Los mensajes de error se mostrarán en la pestaña **Salidas y registros** de la información general del trabajo.

## Entrenamiento de un segundo modelo para comparar

Para comparar entre algoritmos y evaluar qué funciona mejor, puede entrenar dos modelos dentro de una canalización y compararlos.

1. Vuelva al **Diseñador** y seleccione el borrador de canalización **Train-Diabetes-Classifier**.
1. Agregue el componente **Entrenar un modelo clasificador de regresión logística** al lienzo, junto al otro componente de entrenamiento.
1. Conecte la salida del componente **Normalizar columnas numéricas** a la entrada del nuevo componente de entrenamiento.
1. En la parte superior, seleccione **Configurar y enviar**.
1. En la página **Aspectos básicos**, cree un nuevo experimento denominado `designer-compare-classification` y ejecútelo.
1. Seleccione **Revisar y enviar** y, después, seleccione **Enviar** para iniciar la ejecución de la canalización.
1. Para comprobar el estado de la ejecución, vaya a la página **Canalizaciones** y seleccione la canalización **Train-Diabetes-Classifier** con el experimento **designer-compare-classification**.
1. Espere hasta que todos los componentes se hayan completado correctamente.  
1. Seleccione **Información general del trabajo** y, después, seleccione la pestaña **Métricas** para revisar los resultados de ambos componentes de entrenamiento.
1. Pruebe y determine qué modelo ha realizado mejor.

## Eliminación de recursos de Azure

Cuando termine de explorar Azure Machine Learning, debe eliminar los recursos que ha creado para evitar costos innecesarios de Azure.

1. Cierre la pestaña Estudio de Azure Machine Learning y vuelva al Azure Portal.
1. En Azure Portal, en la página **Inicio**, seleccione **Grupos de recursos**.
1. Seleccione el grupo de recursos **rg-dp100-...** .
1. En la parte superior de la página **Información general** del grupo de recursos, seleccione **Eliminar grupo de recursos**.
1. Escribe el nombre del grupo de recursos para confirmar que quieres eliminarlo y selecciona **Eliminar**.
