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

## Entrenamiento de un modelo mediante AutoML

Para explorar el uso de los recursos y recursos en el área de trabajo de Azure Machine Learning, se probará y entrenará un modelo.

Una forma rápida de entrenar y encontrar el mejor modelo para una tarea en función de los datos es mediante la opción **AutoML**.

> **Nota**: Es posible que aparezcan ventanas emergentes que le guiarán por el estudio. Puede cerrar e ignorar todos los elementos emergentes y centrarse en las instrucciones de este laboratorio.

1. Descarga los datos de entrenamiento que se usarán en `https://github.com/MicrosoftLearning/mslearn-azure-ml/raw/refs/heads/main/Labs/02/diabetes-data.zip` y extrae los archivos comprimidos.
1. En el Estudio de Azure Machine Learning, selecciona la página **AutoML** en el menú del lado izquierdo del estudio.
1. Selecciona **+ Nuevo trabajo de ML automatizado**.
1. En el paso **Configuración básica**, asigna un nombre único al trabajo de entrenamiento y experimenta o usa los valores predeterminados asignados. Seleccione **Siguiente**.
1. En el paso **Tipo de tarea y datos**, selecciona **Clasificación** como tipo de tarea y selecciona **+ Crear** para agregar los datos de entrenamiento.
2. En la página **Crear recurso de datos**, en el paso **Tipo de datos**, asigna un nombre al recurso de datos (por ejemplo `training-data`) y selecciona **Siguiente**.
1. En el paso **Origen de datos**, selecciona **Desde archivos locales** para cargar los datos de entrenamiento que descargaste anteriormente. Seleccione **Siguiente**.
1. En el paso **Tipo de almacenamiento de destino**, comprueba que **Azure Blob Storage** está seleccionado como el tipo de almacén de datos y que está seleccionado **workspaceblobstore** como el almacén de datos. Seleccione **Siguiente**.
1. En el paso de **Selección MLTable**, selecciona **Cargar carpeta** y selecciona la carpeta que extrajiste del archivo comprimido que descargaste anteriormente. Seleccione **Siguiente**.
1. Revisa la configuración del recurso de datos y selecciona **Crear**.
1. De nuevo en el paso **Tipo de tarea y datos**, selecciona los datos que acabas de cargar y selecciona **Siguiente**.

> **Sugerencia**: es posible que tengas que seleccionar de nuevo el tipo de tarea **Clasificación** antes de pasar al paso siguiente.

1. En el paso **Configuración de tareas**, selecciona **Diabetic (Boolean)** como columna de destino y, a continuación, abre la opción **Ver opciones de configuración adicionales**.
1. En el panel **Configuración adicional**, cambia la métrica principal a **Precisión** y, a continuación, selecciona **Guardar**.
1. Expande la opción **Límites** y establece las siguientes propiedades:
    * **Pruebas máximas**: 10
    * **Tiempo de espera de experimento (minutos)**: 60
    * **Tiempo de espera de iteración (minutos)**: 15
    * **Habilitar terminación anticipada**: seleccionado

1. En **Datos de prueba**, selecciona **División de pruebas de entrenamiento** y comprueba que el **Porcentaje de prueba de datos** es 10. Seleccione **Siguiente**.
1. En el paso **Proceso**, comprueba que el tipo de proceso es **Serveless** y que el tamaño de la máquina virtual seleccionado es **Standard_DS3_v2**. Seleccione **Siguiente**.

> **Nota**: Las instancias de proceso y los clústeres se basan en imágenes de máquina virtual de Azure estándar. Para este ejercicio, se recomienda la imagen *Standard_DS3_v2* para lograr el equilibrio óptimo entre el coste y el rendimiento. Si la suscripción tiene una cuota que no incluye esta imagen, elija una imagen alternativa, pero tenga en cuenta que una imagen más grande puede incurrir en un costo mayor y una imagen más pequeña puede no ser suficiente para completar las tareas. Como alternativa, pida al administrador de Azure que amplíe la cuota.

1. Revisa la configuración y selecciona **Enviar trabajo de entrenamiento**.

## Uso de trabajos para ver el historial

Después de enviar el trabajo, se te redirigirá a la página del trabajo. Los trabajos le permiten realizar un seguimiento de las cargas de trabajo que ejecutó y compararlas entre sí. Los trabajos pertenecen a un **experimento**, que permite agrupar las ejecuciones de trabajos. 

1. Ten en cuenta que en los parámetros **Información general** puedes encontrar el estado del trabajo, quién lo creó, cuándo se creó y cuánto tiempo tardó en ejecutarse (entre otras cosas).
1. El trabajo de entrenamiento tardará entre 10 y 20 minutos. Una vez completado el trabajo, también puedes ver los detalles de cada ejecución de componente individual, incluida la salida. No dudes en explorar la página de trabajo para comprender cómo se entrena el modelo.

    Azure Machine Learning realiza automáticamente un seguimiento de las propiedades del trabajo. Mediante el uso de trabajos, puede ver fácilmente su historial para comprender lo que usted o sus compañeros ya han hecho.
    Durante la experimentación, los trabajos ayudan a realizar un seguimiento de los diferentes modelos que entrena para comparar e identificar el mejor modelo. Durante la producción, los trabajos permiten comprobar si las cargas de trabajo automatizadas se ejecutaron según lo previsto.

## Eliminación de recursos de Azure

Cuando termine de explorar Azure Machine Learning, debe eliminar los recursos que ha creado para evitar costos innecesarios de Azure.

1. Cierre la pestaña Estudio de Azure Machine Learning y vuelva al Azure Portal.
1. En Azure Portal, en la página **Inicio**, seleccione **Grupos de recursos**.
1. Seleccione el grupo de recursos **rg-dp100-labs**.
1. En la parte superior de la página **Información general** del grupo de recursos, seleccione **Eliminar grupo de recursos**.
1. Escribe el nombre del grupo de recursos para confirmar que quieres eliminarlo y selecciona **Eliminar**.
