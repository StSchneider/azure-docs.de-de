---
title: Verwalten von Azure CDN mit PowerShell | Microsoft Docs
description: Erfahren Sie, wie Sie Azure PowerShell-Cmdlets zum Verwalten von Azure CDN verwenden.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: magattus
ms.openlocfilehash: d6a67bef831028426dec660a1c79feb4ab9340d1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957709"
---
# <a name="manage-azure-cdn-with-powershell"></a>Verwalten von Azure CDN mit PowerShell
PowerShell ermöglicht eine äußerst flexible Methode zur Verwaltung Ihrer Azure CDN-Profile und -Endpunkte.  Sie können PowerShell interaktiv oder durch Schreiben von Skripts verwenden, um Verwaltungsaufgaben zu automatisieren.  In diesem Tutorial werden einige allgemeine Aufgaben vorgestellt, die Sie mit PowerShell zum Verwalten Ihrer Azure CDN-Profile und -Endpunkte ausführen können.

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie PowerShell zur Verwaltung Ihrer Azure CDN-Profile und -Endpunkte verwenden möchten, muss das Azure PowerShell-Modul installiert sein.  Informationen zum Installieren von Azure PowerShell und zum Herstellen einer Verbindung mit Azure mithilfe des `Connect-AzureRmAccount` -Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Melden Sie sich mit `Connect-AzureRmAccount` an, um Azure PowerShell-Cmdlets ausführen zu können.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Auflisten der Azure CDN-Cmdlets
Mithilfe des `Get-Command` -Cmdlets können Sie alle Azure CDN-Cmdlets auflisten.

```text
PS C:\> Get-Command -Module AzureRM.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpointNameAvailability             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfileSsoUrl                        2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Publish-AzureRmCdnEndpointContent                  2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnCustomDomain                      2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnEndpoint                          2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnProfile                           2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Start-AzureRmCdnEndpoint                           2.0.0      AzureRm.Cdn
Cmdlet          Stop-AzureRmCdnEndpoint                            2.0.0      AzureRm.Cdn
Cmdlet          Test-AzureRmCdnCustomDomain                        2.0.0      AzureRm.Cdn
Cmdlet          Unpublish-AzureRmCdnEndpointContent                2.0.0      AzureRm.Cdn
```

## <a name="getting-help"></a>Hilfe
Mit dem `Get-Help` -Cmdlet können Sie Hilfe zu allen diesen Cmdlets aufrufen.  `Get-Help` zeigen Sie Nutzungsinformationen und die Syntax und optional auch Beispiele an.

```text
PS C:\> Get-Help Get-AzureRmCdnProfile

NAME
    Get-AzureRmCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzureRmCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzureRmCdnProfile -examples".
    For more information, type: "get-help Get-AzureRmCdnProfile -detailed".
    For technical information, type: "get-help Get-AzureRmCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Auflisten vorhandener Azure CDN-Profile
Das `Get-AzureRmCdnProfile` -Cmdlet ohne Parameter ruft alle vorhandenen CDN-Profile ab.

```powershell
Get-AzureRmCdnProfile
```

Diese Ausgabe kann zur Enumeration an Cmdlets übergeben werden.

```powershell
# Output the name of all profiles on this subscription.
Get-AzureRmCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzureRmCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

Sie können auch ein einzelnes Profil zurückgeben, indem Sie den Profilnamen und die Ressourcengruppe angeben.

```powershell
Get-AzureRmCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Es ist möglich, mehrere CDN-Profile mit demselben Namen zu verwenden, solange sich die Profile in verschiedenen Ressourcengruppen befinden.  Durch Auslassen des Parameters `ResourceGroupName` werden alle Profile mit dem gleichen Namen zurückgegeben.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Auflisten vorhandener CDN-Endpunkte
`Get-AzureRmCdnEndpoint` kann einen einzelnen Endpunkt oder alle Endpunkte in einem Profil abrufen.  

```powershell
# Get a single endpoint.
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Erstellen von CDN-Profilen und -Endpunkten
`New-AzureRmCdnProfile` und `New-AzureRmCdnEndpoint` werden zur Erstellung von CDN-Profilen und -Endpunkten verwendet. Die folgenden SKUs werden unterstützt:
- Standard_Verizon
- Premium_Verizon
- Custom_Verizon
- Standard_Akamai
- Standard_Microsoft
- Standard_ChinaCdn

```powershell
# Create a new profile
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US"

# Create a new endpoint
New-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US" | New-AzureRmCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Überprüfen der Verfügbarkeit von Endpunktnamen
`Get-AzureRmCdnEndpointNameAvailability` gibt ein Objekt zurück, das angibt, ob ein Endpunktname verfügbar ist.

```powershell
# Retrieve availability
$availability = Get-AzureRmCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Hinzufügen einer benutzerdefinierten Domäne
`New-AzureRmCdnCustomDomain` fügt einem vorhandenen Endpunkt den Namen einer benutzerdefinierten Domäne hinzu.

> [!IMPORTANT]
> CNAME muss mit dem DNS-Anbieter eingerichtet werden. Die Vorgehensweise ist unter [Zuordnen einer benutzerdefinierten Domäne zu einem CDN-Endpunkt (Content Delivery Network, Netzwerk für die Inhaltsübermittlung)](cdn-map-content-to-custom-domain.md) beschrieben.  Sie können die Zuordnung mithilfe von `Test-AzureRmCdnCustomDomain` testen, bevor Sie den Endpunkt ändern.
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzureRmCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzureRmCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Ändern eines Endpunkts
`Set-AzureRmCdnEndpoint` ändert einen vorhandenen Endpunkt.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzureRmCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Löschen/Vorabladen von CDN-Assets
`Unpublish-AzureRmCdnEndpointContent` löscht zwischengespeicherte Assets, und `Publish-AzureRmCdnEndpointContent` lädt Assets vorab auf unterstützten Endpunkten.

```powershell
# Purge some assets.
Unpublish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Unpublish-AzureRmCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Starten/Beenden von CDN-Endpunkten
`Start-AzureRmCdnEndpoint` und `Stop-AzureRmCdnEndpoint` können zum Starten und Beenden von einzelnen Endpunkten oder Gruppen mit Endpunkten verwendet werden.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Stop-AzureRmCdnEndpoint

# Start all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Start-AzureRmCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Löschen von CDN-Ressourcen
`Remove-AzureRmCdnProfile` und `Remove-AzureRmCdnEndpoint` können zum Entfernen von Profilen und Endpunkten verwendet werden.

```powershell
# Remove a single endpoint
Remove-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzureRmCdnEndpoint | Remove-AzureRmCdnEndpoint -Force

# Remove a single profile
Remove-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie Azure CDN mit [.NET](cdn-app-dev-net.md) oder [Node.js](cdn-app-dev-node.md) automatisieren.

Informationen zu CDN-Features finden Sie unter [Übersicht über das Azure Content Delivery Network (CDN)](cdn-overview.md).

