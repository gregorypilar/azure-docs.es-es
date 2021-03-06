---
title: "Características de seguridad para proteger copias de seguridad híbridas mediante Azure Backup | Microsoft Docs"
description: "Aprenda a usar las características de seguridad de Azure Backup para que las copias de seguridad sean más seguras"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 47bc8423-0a08-4191-826d-3f52de0b4cb8
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: pajosh
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 41a7024b51bc7a3c9cf34dba97255ea61fd27924
ms.lasthandoff: 04/07/2017


---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Características de seguridad para proteger copias de seguridad híbridas mediante Azure Backup
Cada vez es mayor la preocupación que generan problemas de seguridad como malware, ransomware e intrusión. Estos problemas de seguridad pueden ser costosos, en términos de dinero y datos. Para protegerse contra dichos ataques, Azure Backup proporciona características de seguridad que protegen las copias de seguridad híbridas. En este artículo se habla de cómo habilitar y usar estas características mediante un agente de Azure Recovery Services y Azure Backup Server. Estas características son:

- **Prevención**. Se agrega una capa adicional de autenticación cada vez que se realiza una operación crítica, como cambiar la frase de contraseña. Esta validación se realiza para asegurarse de que dichas operaciones solo pueden realizarlas usuarios que tengan credenciales de Azure válidas.
- **Alertas**. Se envía una notificación por correo electrónico al administrador de suscripciones cada vez que se realiza una operación crítica, como eliminar datos de copia de seguridad. Este correo electrónico garantiza que el usuario reciba una notificación rápidamente acerca de dichas acciones.
- **Recuperación**. Los datos de copia de seguridad eliminados se conservan durante 14 días a partir de la fecha de la eliminación. Esto garantiza la capacidad de recuperación de los datos en un período dado, con el fin de que no haya pérdida de datos, aunque se produzca un ataque. Además, se mantiene un mayor número de puntos de recuperación mínimos para protegerse contra datos dañados.

> [!NOTE]
> Las características de seguridad no se deben habilitar si usa la copia de seguridad de VM de infraestructura como servicio (IaaS). Estas características no están aún disponibles para la copia de seguridad de VM de IaaS, por lo que su habilitación no tendrá ningún impacto. Las características de seguridad solo se deben habilitar si se usa: <br/>
>  * **Agente de Azure Backup**. La versión mínima del agente es la 2.0.9052. Cuando haya habilitado estas características, debe realizar la actualización a esta versión del agente para realizar operaciones críticas. <br/>
>  * **Azure Backup Server**. La versión mínima del agente de Azure Backup es la 2.0.9052 con Update 1 de Azure Backup Server. <br/>
>  * **System Center Data Protection Manager**. La versión mínima del agente de Azure Backup es la 2.0.9052 con Data Protection Manager 2012 R2 UR12 o Data Protection Manager 2016 UR2. <br/> 


> [!NOTE]
> Estas características solo están disponibles para el almacén de Recovery Services. Todos los almacenes de Recovery Services recién creados tienen las siguientes características habilitadas de forma predeterminada. En el caso de los almacenes de Recovery Services existentes, los usuarios habilitan estas características mediante los pasos mencionados en la sección siguiente. Una vez habilitadas las características, se aplican a todos los equipos agente de Recovery Services, instancias de Azure Backup Server y servidores Data Protection Manager registrados con el almacén. La habilitación a esta configuración es una acción única, por lo que una vez que se habiliten estas características no será posible deshabilitarlas.
>

## <a name="enable-security-features"></a>Habilitar características de seguridad
Si va a crear un almacén de Recovery Services, puede usar todas las características de seguridad. Si trabaja con un almacén existente, habilite las características de seguridad siguiendo estos pasos:

1. Inicie sesión en Azure Portal con las credenciales de Azure.
2. Seleccione **Examinar** y escriba **Recovery Services**.

    ![Captura de pantalla de opción Examinar de Azure Portal](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Aparece la lista de almacenes de Servicios de recuperación. Seleccione un almacén en ella. Se abre el panel del almacén seleccionado.
3. En la lista de elementos que aparece en el almacén, en **Configuración**, haga clic en **Propiedades**.

    ![Captura de pantalla de opciones del almacén de Recovery Services](./media/backup-azure-security-feature/vault-list-properties.png)
4. En **Configuración de seguridad**, haga clic en **Actualizar**.

    ![Captura de pantalla de propiedades del almacén de Recovery Services](./media/backup-azure-security-feature/security-settings-update.png)

    El vínculo de actualización abre la hoja **Configuración de seguridad**, que proporciona un resumen de las características y permite habilitarlas.
5. En el menú desplegable **Have you configured Azure Multi-Factor Authentication?** (¿Ha configurado Azure Multi-Factor Authentication?), seleccione un valor para confirmar si ha habilitado [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md). Si está habilitado, se le pedirá que realice la autenticación desde otro dispositivo (por ejemplo, un teléfono móvil) al iniciar sesión en Azure Portal.

   Al realizar operaciones críticas en Backup, debe especificar un PIN de seguridad, disponible en Azure Portal. Al habilitar Azure Multi-Factor Authentication, se agrega una capa de seguridad. Solo los usuarios autorizados con credenciales de Azure válidas y autenticados desde un segundo dispositivo pueden tener acceso a Azure Portal.
6. Para guardar la configuración de seguridad, seleccione **Habilitar** y haga clic en **Guardar**. Solo se puede seleccionar **Habilitar** después de seleccionar un valor en la lista **Have you configured Azure Multi-Factor Authentication?** (¿Ha configurado Azure Multi-Factor Authentication?) del paso anterior.

    ![Captura de pantalla de configuración de seguridad](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Recuperar datos de copia de seguridad eliminados
Backup conserva los datos de copia de seguridad eliminados durante 14 días adicionales y no los elimina inmediatamente si se realiza la operación de **detener copia de seguridad con la eliminación de datos de copia de seguridad**. Para restaurar estos datos dentro del período de 14 días, siga los pasos que se muestran a continuación, según lo que use:

En el caso de los usuarios del **agente de Azure Recovery Services**:

1. Si el equipo en el que se realizaron las copias de seguridad aún está disponible, use [Recuperar los datos en la misma máquina](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) en Azure Recovery Services para realizar la recuperación desde todos los puntos de recuperación antiguos.
2. Si este equipo no está disponible, utilice [Recuperar en una máquina alternativa](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) para usar otro equipo de Azure Recovery Services para obtener estos datos.

Para los usuarios del **servidor de copia de seguridad de Azure**:

1. Si el servidor en el que se realizaron las copias de seguridad está aún disponible, vuelva a proteger los orígenes de datos eliminados y use la característica **Recuperar datos** para realizar la recuperación desde todos los puntos de recuperación antiguos.
2. Si este servidor no está disponible, utilice [Recuperación de datos de otro servidor de copia de seguridad de Azure](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server) para usar otra instancia de Azure Backup Server para obtener estos datos.

En el caso de los usuarios de **Data Protection Manager**:

1. Si el servidor en el que se realizaron las copias de seguridad está aún disponible, vuelva a proteger los orígenes de datos eliminados y use la característica **Recuperar datos** para realizar la recuperación desde todos los puntos de recuperación antiguos.
2. Si este servidor no está disponible, utilice [Agregar DPM externo](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server) para usar otro servidor Data Protection Manager para obtener estos datos.

## <a name="prevent-attacks"></a>Prevenir ataques
Se han agregado comprobaciones para asegurarse de que los usuarios válidos son los únicos que pueden realizar varias operaciones. Entre estas se incluyen la adición de una capa de autenticación adicional y el mantenimiento de una duración de retención mínima con fines de recuperación.

### <a name="authentication-to-perform-critical-operations"></a>Autenticación para realizar operaciones críticas
Como parte de la adición de una capa de autenticación adicional para las operaciones críticas, se le solicita que escriba un PIN se seguridad al realizar las operaciones **Detener la protección con eliminación de datos** y **Cambio de la frase de contraseña**.

Para recibir este PIN:

1. Inicie sesión en el Portal de Azure.
2. Vaya al **almacén de Recovery Services** > **Configuración** > **Propiedades**.
3. En **PIN de seguridad**, haga clic en **Generar**. Se abrirá una hoja que contiene el PIN que se va a escribir en la interfaz de usuario del agente de Azure Recovery Services.
    Este PIN solo es válido durante cinco minutos y se genera automáticamente después de ese período.

### <a name="maintain-a-minimum-retention-range"></a>Mantener una duración de retención mínima
Para asegurarse de que siempre hay un número válido de puntos de recuperación disponibles, se han agregado las siguientes comprobaciones:

- Para la retención diaria, se deben realizar un mínimo de **siete** días de retención.
- Para la retención semanal, se deben realizar un mínimo de **cuatro** semanas de retención.
- Para la retención mensual, se deben realizar un mínimo de **tres** meses de retención.
- Para la retención anual, se debe realizar un mínimo de **un** año de retención.

## <a name="notifications-for-critical-operations"></a>Notificaciones de operaciones críticas
Normalmente, al realizarse una operación crítica, se envía una notificación por correo electrónico al administrador de suscripciones con detalles sobre la operación. Puede configurar destinatarios de correo electrónico adicionales para estas notificaciones con Azure Portal.

Las características de seguridad que se mencionan en este artículo proporcionan mecanismos de defensa contra ataques dirigidos. Lo que es más importante, en caso de producirse un ataque, es que estas características permiten recuperar los datos.

## <a name="next-steps"></a>Pasos siguientes
* [Comience a usar el almacén de Azure Recovery Services](backup-azure-vms-first-look-arm.md) para habilitar estas características.
* [Descargue la versión más reciente del agente de Azure Recovery Services](http://aka.ms/azurebackup_agent) para proteger los equipos con Windows y los datos de copia de seguridad frente a ataques.
* [Descargue la versión más reciente de Azure Backup Server](https://aka.ms/latest_azurebackupserver) para proteger las cargas de trabajo y los datos de copia de seguridad frente a ataques.
* [Descargue UR12 para System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) o [descargue UR2 para System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) para proteger las cargas de trabajo y los datos de copia de seguridad frente a ataques.

