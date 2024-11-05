---
lab:
  title: Exploración del área de trabajo de Azure Machine Learning
---

# Exploración del área de trabajo de Azure Machine Learning

Azure Machine Learning proporciona una plataforma de ciencia de datos para entrenar y administrar modelos de aprendizaje automático. En este laboratorio, creará un área de trabajo de Azure Machine Learning y explorará las distintas formas de trabajar con el área de trabajo. El laboratorio está diseñado como una introducción de las distintas funcionalidades principales de Azure Machine Learning y las herramientas de desarrollo. Si quiere obtener información sobre las funcionalidades en mayor profundidad, hay otros laboratorios que explorar.

## Antes de empezar

Necesitará una [suscripción de Azure](https://azure.microsoft.com/free?azure-portal=true) en la que tenga acceso de nivel administrativo.

## Aprovisionar un área de trabajo de Azure Machine Learning

Un **área de trabajo** de Azure Machine Learning proporciona un lugar central para administrar todos los recursos y recursos que necesita para entrenar y administrar los modelos. Puede aprovisionar un área de trabajo mediante la interfaz interactiva en el Azure Portal, o bien puede usar la CLI de Azure con la extensión de Azure Machine Learning. En la mayoría de los escenarios de producción, es mejor automatizar el aprovisionamiento con la CLI para poder incorporar la implementación de recursos en un proceso de desarrollo y operaciones repetibles (*DevOps*). 

En este ejercicio, usará el Azure Portal para aprovisionar Azure Machine Learning para explorar todas las opciones.

1. Inicie sesión en `https://portal.azure.com/`.
2. Cree un recurso de **Azure Machine Learning** con la siguiente configuración:
    - **Suscripción**: *suscripción de Azure*
    - **Grupo de recursos**: `rg-dp100-labs`
    - **Nombre del espacio de trabajo**: `mlw-dp100-labs`
    - **Región**: *seleccione la región geográfica más cercana*.
    - **Cuenta de almacenamiento**: *tenga en cuenta la nueva cuenta de almacenamiento predeterminada que se creará para el área de trabajo*.
    - **Almacén de claves**: *tenga en cuenta el nuevo almacén de claves predeterminado que se creará para el área de trabajo*.
    - **Application Insights**: *tenga en cuenta el nuevo recurso de Application Insights predeterminado que se creará para el área de trabajo*.
    - **Registro de contenedor**: ninguno (*se creará uno automáticamente la primera vez que implemente un modelo en un contenedor*).
3. Espere a que se cree el área de trabajo y sus recursos asociados: normalmente tarda unos 5 minutos.

> **Nota**: Al crear un área de trabajo de Azure Machine Learning, puede usar algunas opciones avanzadas para restringir el acceso a través de un *punto de conexión privado* y especificar claves personalizadas para el cifrado de datos. No usaremos estas opciones en este ejercicio, pero debe tenerlas en cuenta.

## Explore Estudio de Azure Machine Learning

*Estudio de Azure Machine Learning* es un portal basado en web a través del cual puede acceder al área de trabajo de Azure Machine Learning. Puede usar el Estudio de Azure Machine Learning para administrar todos los recursos y recursos del área de trabajo.

1. Vaya al grupo de recursos denominado **rg-dp100-labs**.
1. Confirme que el grupo de recursos contiene el área de trabajo de Azure Machine Learning, Application Insights, un Key Vault y una cuenta de almacenamiento.
1. Seleccione su área de trabajo de Azure Machine Learning.
1. Seleccione **Iniciar estudio** en la página **Información general**. Se abrirá otra pestaña en el explorador para abrir el Estudio de Azure Machine Learning.
1. Cierre los elementos emergentes que aparecen en Studio.
1. Observe las distintas páginas que se muestran en el lado izquierdo del estudio. Si solo los símbolos están visibles en el menú, seleccione el icono de &#9776; para expandir el menú y explorar los nombres de las páginas.
1. Fíjese en la sección **Creación**, que incluye **Cuadernos**, **ML automatizado**, y **Diseñador**. Estas son las tres formas en que puede crear sus propios modelos de aprendizaje automático dentro de la Estudio de Azure Machine Learning.
1. Observe la sección **Activos**, que incluye **Datos**, **Trabajos** y **Modelos**, entre otras cosas. Los recursos se consumen o crean al entrenar o puntuar un modelo. Los recursos se usan para entrenar, implementar y administrar los modelos y se pueden versionar para realizar un seguimiento del historial.
1. Tenga en cuenta la sección **Administrar**, que incluye **Proceso**, entre otras cosas. Estos son recursos infraestructurales necesarios para entrenar o implementar un modelo de aprendizaje automático.

## Creación de una canalización de entrenamiento

Para explorar el uso de los recursos y recursos en el área de trabajo de Azure Machine Learning, se probará y entrenará un modelo.

Una forma rápida de crear una canalización de entrenamiento de modelos es mediante el **Diseñador**.

> **Nota**: Es posible que aparezcan ventanas emergentes que le guiarán por el estudio. Puede cerrar e ignorar todos los elementos emergentes y centrarse en las instrucciones de este laboratorio.

1. Seleccione la página **Diseñador** en el menú del lado izquierdo de Studio.
1. Seleccione el ejemplo **Regresión - Predicción de precios de automóviles (básico).**

    Aparece una nueva canalización. En la parte superior de la canalización, se muestra un componente para cargar **datos de precios de automóviles (sin procesar).** La canalización procesa los datos y entrena un modelo de regresión lineal para predecir el precio de cada automóvil.
1. Seleccione **Configurar y enviar** en la parte superior de la página para abrir el diálogo **Configurar trabajo de canalización**
1. En la página **Aspectos básicos** seleccione **Crear nuevo** y llame al experimento `train-regression-designer`, a continuación, seleccione **Siguiente**.
1. En la página **Entradas y salidas** seleccione **Siguiente** sin hacer cambios.
1. En la página **Configuración de runtime** aparece un error porque no tiene un proceso predeterminado para ejecutar la canalización.

Se creará un destino de proceso.

## Creación de un destino de proceso

Para ejecutar cualquier carga de trabajo dentro del área de trabajo de Azure Machine Learning, necesitará un recurso informático. Una de las ventajas de Azure Machine Learning es la capacidad de crear un proceso basado en la nube en el que pueda ejecutar experimentos y scripts de entrenamiento a gran escala.

1. En el Estudio de Azure Machine Learning, seleccione la página **Proceso** en el menú del lado izquierdo. Puede usar cuatro tipos de recursos de proceso:
    - **Instancias de proceso**: una máquina virtual administrada por Azure Machine Learning. Ideal para el desarrollo al explorar datos y experimentar de forma iterativa con modelos de aprendizaje automático.
    - **Clústeres de proceso**: clústeres escalables de máquinas virtuales para el procesamiento a petición de código de experimento. Ideal para ejecutar código de producción o trabajos automatizados.
    - **Clústeres de Kubernetes**: un clúster de Kubernetes que se usa para el entrenamiento y la puntuación. Ideal para la implementación de modelos en tiempo real a gran escala.
    - **Proceso asociado**: adjunte los recursos de proceso de Azure existentes al área de trabajo, como Virtual Machines o clústeres de Azure Databricks.

    Para entrenar un modelo de Machine Learning creado con el Diseñador, puede usar una instancia de proceso o un clúster de proceso.

2. En la pestaña **Instancias de proceso**, agregue una nueva instancia de proceso con los valores siguientes. 
    - **Nombre del proceso**: *escriba un nombre único*
    - **Ubicación**: *Automáticamente la misma ubicación que su área de trabajo*
    - **Tipo de máquina virtual**:`CPU`
    - **Tamaño de la máquina virtual**:`Standard_DS11_v2`
    - **Cuotas disponibles**: muestra los núcleos dedicados disponibles.
    - **Mostrar la configuración avanzada**: tenga en cuenta la siguiente configuración, pero no la seleccione.
        - **Habilitar acceso SSH**: `Unselected`  *(se puede usar para habilitar el acceso directo a la máquina virtual mediante un cliente SSH)*
        - **Habilitar red virtual**: `Unselected`  *(normalmente se usaría en un entorno empresarial para mejorar la seguridad de la red)*
        - **Asignar a otro usuario**: `Unselected` *(puede usarlo para asignar una instancia de cálculo a un científico de datos)*
        - **Aprovisionar con un script de configuración**: `Unselected` *(puede usarla para agregar un script a fin de ejecutarlo en la instancia remota cuando se cree)*
        - **Asignar una identidad administrada**: `Unselected` *(puede conectar identidades administradas asignadas por el sistema o por el usuario para conceder acceso a los recursos)*

3. Seleccione **Crear** y espere a que la instancia de cálculo se inicie y su estado cambie a **En ejecución**.

> **Nota**: Las instancias de proceso y los clústeres se basan en imágenes de máquina virtual de Azure estándar. Para este ejercicio, se recomienda la imagen *Standard_DS11_v2* para lograr el equilibrio óptimo entre el costo y el rendimiento. Si la suscripción tiene una cuota que no incluye esta imagen, elija una imagen alternativa, pero tenga en cuenta que una imagen más grande puede incurrir en un costo mayor y una imagen más pequeña puede no ser suficiente para completar las tareas. Como alternativa, pida al administrador de Azure que amplíe la cuota.

## Ejecute la canalización de entrenamiento

Ha creado un destino de proceso y ahora puede ejecutar la canalización de entrenamiento de ejemplo en el Diseñador.

1. Vaya a la página **Diseñador**.
1. Seleccione el borrador de canalización **Regresión - Predicción de precios de automóviles (básico)** .
1. Seleccione **Configurar y enviar** en la parte superior de la página para abrir el diálogo **Configurar trabajo de canalización**
1. En la página **Aspectos básicos** seleccione **Crear nuevo** y llame al experimento `train-regression-designer`, a continuación, seleccione **Siguiente**.
1. En la página **Entradas y salidas** seleccione **Siguiente** sin hacer cambios.
1. En la **Configuración del entorno de ejecución**, en la lista desplegable **Seleccionar tipo de proceso**, seleccione *Instancia de proceso* y, en la lista desplegable **Seleccionar instancia de proceso de Azure ML**, seleccione la instancia de proceso recién creada.
1. Seleccione **Revisar y enviar** para revisar el trabajo de canalización y, a continuación, seleccione **Enviar** para ejecutar la canalización de entrenamiento.

La canalización de entrenamiento se enviará ahora a la instancia de proceso. La canalización tardará aproximadamente 10 minutos en completarse. Se explorarán otras páginas mientras tanto.

## Uso de trabajos para ver el historial

Cada vez que ejecute un script o una canalización en el área de trabajo de Azure Machine Learning, se registra como un **trabajo**. Los trabajos le permiten realizar un seguimiento de las cargas de trabajo que ejecutó y compararlas entre sí. Los trabajos pertenecen a un **experimento**, que permite agrupar las ejecuciones de trabajos.

1. Vaya a la página **Trabajos** mediante el menú del lado izquierdo del Estudio de Azure Machine Learning.
1. Seleccione el experimento **train-regression-designer** para ver sus ejecuciones de trabajo. Aquí verá información general de todos los trabajos que forman parte de este experimento. Si ejecutó varias canalizaciones de entrenamiento, esta vista le permite comparar las canalizaciones e identificar la mejor.
1. Seleccione el último trabajo del experimento **train-regression-designer**.
1. Tenga en cuenta que la canalización de entrenamiento se muestra donde puede ver qué componentes se ejecutaron correctamente o con errores. Si el trabajo todavía se está ejecutando, también puede identificar lo que se está ejecutando actualmente.
1. Para ver los detalles del trabajo de canalización, seleccione el **Resumen de trabajos** en la parte superior derecha para ampliar el **Resumen de trabajos de canalización**.
1. Tenga en cuenta que en los parámetros **Información general** puede encontrar el estado del trabajo, quién creó la canalización, cuándo se creó y cuánto tiempo tardó en ejecutarse la canalización completa (entre otras cosas).

    Al ejecutar un script o una canalización como un trabajo, puede definir las entradas y documentar las salidas. Azure Machine Learning también realiza automáticamente un seguimiento de las propiedades del trabajo. Mediante el uso de trabajos, puede ver fácilmente su historial para comprender lo que usted o sus compañeros ya han hecho.

    Durante la experimentación, los trabajos ayudan a realizar un seguimiento de los diferentes modelos que entrena para comparar e identificar el mejor modelo. Durante la producción, los trabajos permiten comprobar si las cargas de trabajo automatizadas se ejecutaron según lo previsto.

1. Una vez completado el trabajo, también puede ver los detalles de cada ejecución de componente individual, incluida la salida. No dude en explorar la canalización para comprender cómo se entrena el modelo.

## Eliminación de recursos de Azure

Cuando termine de explorar Azure Machine Learning, debe eliminar los recursos que ha creado para evitar costos innecesarios de Azure.

1. Cierre la pestaña Estudio de Azure Machine Learning y vuelva al Azure Portal.
1. En Azure Portal, en la página **Inicio**, seleccione **Grupos de recursos**.
1. Seleccione el grupo de recursos **rg-dp100-labs**.
1. En la parte superior de la página **Información general** del grupo de recursos, seleccione **Eliminar grupo de recursos**.
1. Escribe el nombre del grupo de recursos para confirmar que quieres eliminarlo y selecciona **Eliminar**.
