---
title: "Uso del complemento para máquinas subordinadas de Azure con Hudson Continuous Integration | Microsoft Docs"
description: "Describe cómo usar el complemento esclavo de Azure con Hudson Continuous Integration."
services: virtual-machines-linux
documentationcenter: 
author: rmcmurray
manager: wpickett
editor: 
ms.assetid: b2083d1c-4de8-4a19-a615-ccc9d9b6e1d9
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 0661f26e62a465ccd096773cbabd47268491f22d
ms.lasthandoff: 01/05/2017


---
# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Uso del complemento esclavo de Azure con Hudson Continuous Integration
El complemento esclavo de Azure para Hudson le permite aprovisionar los nodos subordinados en Azure cuando se ejecutan compilaciones distribuidas.

## <a name="install-the-azure-slave-plug-in"></a>Instalación del complemento subordinado de Azure
1. En el panel de Hudson, haga clic en **Manage Hudson**(Administrar Hudson).
2. En la página **Manage Hudson** (Administrar Hudson), haga clic en **Manage Plugins** (Administrar complementos).
3. Haga clic en la pestaña **Available** (Disponible).
4. Haga clic en **Buscar** y escriba **Azure** para limitar la lista de complementos pertinentes.
   
    Si opta por desplazarse por la lista de complementos disponibles, encontrará el complemento para máquinas subordinadas de Azure en la sección **Cluster Management and Distributed Build** (Administración de clústeres y compilación distribuida) de la pestaña **Others** (Otros).
5. Active la casilla **Azure Slave Plugin**(Complemento esclavo de Azure).
6. Haga clic en **Instalar**.
7. Reinicie Hudson.

Ahora que está instalado el complemento, los siguientes pasos serían configurarlo con el perfil de suscripción de Azure y crear una plantilla que se usará en la creación de la máquina virtual para el nodo subordinado.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Configuración del complemento esclavo de Azure con el perfil de suscripción
Un perfil de suscripción, también conocido como configuración de publicación, es un archivo XML que contiene credenciales seguras y alguna información adicional que necesitará para trabajar con Azure en el entorno de desarrollo. Para configurar el complemento esclavo de Azure, necesitará:

* Su id. de suscripción
* Un certificado de administración para la suscripción

Estos se pueden encontrar en su [perfil de suscripción]. A continuación se muestra un ejemplo de un perfil de suscripción.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

          <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

              ServiceManagementUrl="https://management.core.windows.net"

              Id="<Subscription ID>"

              Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

          </PublishProfile>

    </PublishData>

Una vez que tiene el perfil de suscripción, siga estos pasos para configurar el complemento esclavo de Azure.

1. En el panel de Hudson, haga clic en **Manage Hudson**(Administrar Hudson).
2. Haga clic en **Configure System**(Configurar sistema).
3. Desplácese hacia abajo en la página para encontrar la sección **Cloud** (Nube).
4. Haga clic en **Add new cloud > Microsoft Azure** (Agregar nueva nube > Microsoft Azure).
   
    ![agregar nube nueva][add new cloud]
   
    Se mostrarán los campos donde debe especificar los detalles de suscripción.
   
    ![configurar perfil][configure profile]
5. Copie el certificado de administración y el id. de suscripción del perfil de suscripción y péguelos en los campos correspondientes.
   
    Al copiar el certificado de administración y el identificador de suscripción, **no** incluya las comillas que delimitan los valores.
6. Haga clic en **Verify configuration**(Comprobar configuración).
7. Cuando se compruebe que la configuración es correcta, haga clic en **Save**(Guardar).

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Configuración de una plantilla de máquina virtual para el complemento esclavo de Azure
Una plantilla de máquina virtual define los parámetros que usará el complemento para crear un nodo subordinado en Azure. En los pasos siguientes crearemos plantilla para una VM de Ubuntu.

1. En el panel de Hudson, haga clic en **Manage Hudson**(Administrar Hudson).
2. Haga clic en **Configure System**(Configurar sistema).
3. Desplácese hacia abajo en la página para encontrar la sección **Cloud** (Nube).
4. Dentro de la sección **Cloud** (Nube), busque **Add Azure Virtual Machine Template **(Agregar plantilla de máquina virtual de Azure) y haga clic en el botón **Add** (Agregar).
   
    ![agregar plantilla de vm][add vm template]
5. Especifique un nombre de servicio en la nube en el campo **Name** (Nombre). Si el nombre que especifique se refiere a un servicio en la nube existente, la máquina virtual se aprovisionará en dicho servicio. De lo contrario, Azure creará uno nuevo.
6. En el campo **Description** (Descripción), escriba el texto que describe la plantilla que está creando. Esta información es solo para fines documentales y no se usa en el aprovisionamiento de una máquina virtual.
7. En el campo **Labels** (Etiquetas), escriba **linux**. Esta etiqueta se usa para identificar la plantilla que está creando y posteriormente para hacer referencia a la plantilla al crear un trabajo de Hudson.
8. Seleccione una región donde se creará la máquina virtual.
9. Seleccione el tamaño adecuado de la máquina.
10. Especifique una cuenta de almacenamiento donde se creará la máquina virtual. Asegúrese de que se encuentra en la misma región que el servicio en la nube que se va a usar. Si desea crear nuevo almacenamiento, puede dejar este campo en blanco.
11. El tiempo de retención especifica el número de minutos antes de que Hudson elimine un subordinado inactivo. Deje el valor predeterminado de 60.
12. En **Usage**(Uso), seleccione la condición adecuada de uso de este nodo subordinado. Por ahora, seleccione **Utilize this node as much as possible**(Usar este nodo tanto como sea posible).
    
     En este punto, su formulario sería parecido al siguiente:
    
     ![configuración de plantilla][template config]
13. En **Image Family or Id** (Familia o identificador de imagen), debe especificar la imagen de sistema que se instalará en la máquina virtual. Puede seleccionar de una lista de familias de imagen o especificar una imagen personalizada.
    
     Si desea seleccionar de una lista de familias de imagen, escriba el primer carácter (distingue mayúsculas de minúsculas) del nombre de familia de imagen. Por ejemplo, al escribir **U** aparecerá una lista de familias de Ubuntu Server. Una vez que seleccione de la lista, Jenkins usará la versión más reciente de esa imagen de sistema de esa familia al aprovisionar la máquina virtual.
    
     ![lista de familias de SO][OS family list]
    
     Si tiene una imagen personalizada que desea usar en su lugar, escriba su nombre. Los nombres de imagen personalizada no se muestran en una lista, por lo que debe asegurarse de escribir el nombre correctamente.    
    
     Para este tutorial, escriba **U** para que aparezca una lista de imágenes de Ubuntu y seleccione **Ubuntu Server 14.04 LTS**.
14. Para **Launch method** (Método de inicio), seleccione **SSH**.
15. Copie el siguiente script y péguelo en el campo **Init script** (Script de inicialización).
    
         # Install Java
    
         sudo apt-get -y update
    
         sudo apt-get install -y openjdk-7-jdk
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y openjdk-7-jdk
    
         # Install git
    
         sudo apt-get install -y git
    
         #Install ant
    
         sudo apt-get install -y ant
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y ant
    
     El **script de inicialización** se ejecutará después de crearse la máquina virtual. En este ejemplo, el script instala Java, git y ant.
16. En los campos **Username** (Nombre de usuario) y **Password** (Contraseña), escriba los valores preferidos para la cuenta de administrador que se creará en la máquina virtual.
17. Haga clic en **Verify Template** (Comprobar plantilla) para comprobar si los parámetros especificados son válidos.
18. Haga clic en **Save**(Guardar).

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Creación de un trabajo de Hudson que se ejecuta en un nodo subordinado en Azure
En esta sección, creará una tarea de Hudson que se ejecutará en un nodo subordinado en Azure.

1. En el panel de Hudson, haga clic en **New Job**(Nuevo trabajo).
2. Escriba un nombre para el trabajo que se va a crear.
3. Para el tipo de trabajo, seleccione **Build a free-style software job**(Crear un trabajo de software de estilo libre).
4. Haga clic en **Aceptar**.
5. En la página de configuración del trabajo, seleccione **Restrict where this project can be run**(Restringir dónde se puede ejecutar este proyecto).
6. Seleccione **Node and label menu** (Menú de nodo y etiqueta) y seleccione **linux** (esta etiqueta se especificó al crear la plantilla de máquina virtual en la sección anterior).
7. En la sección **Build** (Compilar), haga clic en **Add build step** (Agregar paso de compilación) y seleccione **Execute shell** (Ejecutar shell).
8. Edite el siguiente script; para ello, sustituya **{your github account name}**, **{your project name}** y **{your project directory}** por los valores adecuados y pegue el script editado en el área de texto que aparece.
   
        # Clone from git repo
   
        currentDir="$PWD"
   
        if [ -e {your project directory} ]; then
   
              cd {your project directory}
   
              git pull origin master
   
        else
   
              git clone https://github.com/{your github account name}/{your project name}.git
   
        fi
   
        # change directory to project
   
        cd $currentDir/{your project directory}
   
        #Execute build task
   
        ant
9. Haga clic en **Save**(Guardar).
10. En el panel de Hudson, busque el trabajo que acaba de crear y haga clic en icono **Schedule a build** (Programar una compilación).

Hudson creará luego un nodo subordinado con la plantilla que creó en la sección anterior y ejecutará el script especificado en el paso de compilación de esta tarea.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[perfil de suscripción]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png


