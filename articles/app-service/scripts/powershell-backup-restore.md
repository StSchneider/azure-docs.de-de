---
title: 'Azure PowerShell-Skriptbeispiel: Wiederherstellen einer Web-App aus einer Sicherung | Microsoft-Dokumentation'
description: 'Azure PowerShell-Skriptbeispiel: Wiederherstellen einer Web-App aus einer Sicherung'
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: caee00130efdea253ced16d090eafeee22c16ac3
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53586861"
---
# <a name="restore-a-web-app-from-a-backup-using-azure-powershell"></a>Wiederherstellen einer Web-App aus einer Sicherung mit Azure PowerShell

Mit dem folgenden Skriptbeispiel wird eine zuvor erstellte Sicherung aus einer vorhandenen Web-App abgerufen und durch Überschreiben des Inhalts wiederhergestellt. 

Installieren Sie bei Bedarf Azure PowerShell anhand der Anleitung im [Azure PowerShell-Handbuch](/powershell/azure/overview), und führen Sie dann `Connect-AzureRmAccount` aus, um eine Verbindung mit Azure herzustellen. 

## <a name="sample-script"></a>Beispielskript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore/backup-restore.ps1?highlight=1-2 "Restore a web app from a backup")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung 

Wenn Sie die Web-App nicht mehr benötigen, entfernen Sie mit dem folgenden Befehl die Ressourcengruppe, die Web-App und alle zugehörigen Ressourcen:

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Ruft eine Liste der Sicherungen für eine Web-App ab. |
| [Restore-AzureRmWebAppBackup](/powershell/module/azurerm.websites/restore-azurermwebappbackup) | Stellt eine Web-App aus einer zuvor ausgeführten Sicherung wieder her. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche Azure PowerShell-Beispiele für Azure App Service-Web-Apps finden Sie unter [Azure PowerShell-Beispiele](../samples-powershell.md).
