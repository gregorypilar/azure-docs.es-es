---
title: "Administración de Azure Analysis Services con PowerShell | Microsoft Docs"
description: "Administración de Azure Analysis Services con PowerShell."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: dbe1dba78fb2597dc595f9e6895d0a15181c1f05
ms.contentlocale: es-es
ms.lasthandoff: 04/27/2017


---

# <a name="manage-azure-analysis-services-with-powershell"></a>Administración de Azure Analysis Services con PowerShell

En este artículo se describen los cmdlets de PowerShell que se usan para realizar tareas de administración de bases de datos y del servidor de Azure Analysis Services. 

Las tareas de administración del servidor como crear o eliminar un servidor, suspender o reanudar las operaciones del servidor o cambiar el nivel de servicio, usan cmdlets de Azure Resource Manager (AzureRM). Otras tareas para administrar las bases de datos como agregar o quitar miembros del rol, procesar o realizar particiones, usan los mismos cmdlets en el módulo [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) que SQL Server Analysis Services.

## <a name="permissions"></a>Permisos
Para la mayoría de las tareas de PowerShell, es necesario disponer de privilegios de administración en el servidor de Analysis Services que esté administrando. Las tareas programadas de PowerShell son operaciones desatendidas. La cuenta que ejecuta Scheduler debe tener privilegios de administrador en el servidor de Analysis Services. 

Para las operaciones del servidor mediante cmdlets de AzureRm, su cuenta o la cuenta que ejecuta Scheduler también debe pertenecer al rol de propietario para el recurso en [Control de acceso basado en rol (RBAC) de Azure](../active-directory/role-based-access-control-what-is.md). 

## <a name="server-operations"></a>Operaciones del servidor 
Los cmdlets de Azure Analysis Services se incluyen en el módulo del componente [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices). Para instalar los módulos de cmdlets de AzureRM, consulte [Azure Resource Manager cmdlets](/powershell/azure/overview) (Cmdlets de Azure Resource Manager) en la Galería de PowerShell.

|Cmdlet|Descripción| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Obtiene los detalles de una instancia del servidor.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Crea una nueva instancia de servidor.|
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Quita una instancia de servidor.|  
|[Suspend-AzureRmAnalysisServicesServe](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Suspende una instancia de servidor.| 
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Reanuda una instancia de servidor.|  
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Modifica una instancia de servidor.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Comprueba la existencia de una instancia del servidor.| 

## <a name="database-operations"></a>Operaciones de la base de datos
Las operaciones de la base de datos de Azure Analysis Services utilizan el mismo módulo [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) que SQL Server Analysis Services. Pero no todos los cmdlets son compatibles con Azure Analysis Services. 

El módulo SQLASCMDLETS proporciona cmdlets de administración de base de datos específicos de la tarea, así como el cmdlet de uso general Invoke-ASCmd que acepta una consulta o script de Tabular Model Scripting Language (TMSL). Los siguientes cmdlets del módulo SQLASCMDLETS son compatibles con Azure Analysis Services.
  
|Cmdlet|Descripción|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Agrega un miembro a un rol de base de datos.| 
|[Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet)|Realiza una copia de seguridad de una base de datos de Analysis Services.|  
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Quita un miembro de un rol de base de datos.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Ejecuta un script de TMSL.|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|Procesa una base de datos.|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|Procesa una partición.| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|Procesa una tabla.|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|Combina una partición.|  
|[Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet)|Restaura una base de datos de Analysis Services.| 
  

## <a name="related-information"></a>Información relacionada
* [Scripting de PowerShell en Analysis Services](https://msdn.microsoft.com/library/hh213141.aspx).
* [Programación de modelo tabular para el nivel de compatibilidad 1200](https://msdn.microsoft.com/library/mt712541.aspx)

