---
title: Horizontales Hochskalieren eines Azure Service Fabric-Clusters durch Hinzufügen von VM-Skalierungsgruppen | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie ein Service Fabric-Cluster durch Hinzufügen einer VM-Skalierungsgruppe skaliert wird.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/21/2018
ms.author: ryanwi
ms.openlocfilehash: 8f460b41cd2ce62b7a3e0138caa25f68e2fd22ad
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156492"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Skalieren eines Service Fabric-Clusters durch Hinzufügen einer VM-Skalierungsgruppe
In diesem Artikel wird beschrieben, wie Sie einen Azure Service Fabric-Cluster skalieren, indem Sie eine neue VM-Skalierungsgruppe zu einem vorhandenen Cluster hinzufügen. Ein Service Fabric-Cluster enthält eine per Netzwerk verbundene Gruppe von virtuellen oder physischen Computern, auf denen Ihre Microservices bereitgestellt und verwaltet werden. Ein physischer oder virtueller Computer, der Teil eines Clusters ist, wird als Knoten bezeichnet. VM-Skalierungsgruppen sind eine Azure-Computeressource, mit der Sie eine Sammlung von virtuellen Computern als Gruppe bereitstellen und verwalten können. Jeder Knotentyp, der in einem Azure-Cluster definiert ist, wird [als separate Skalierungsgruppe eingerichtet](service-fabric-cluster-nodetypes.md). Jeder Knotentyp kann dann separat verwaltet werden. Nach dem Erstellen eines Service Fabric-Clusters können Sie einen Clusterknotentyp vertikal skalieren (die Ressourcen der Knoten ändern), ein Upgrade für das Betriebssystem der Knotentyp-VMs durchführen oder eine neue VM-Skalierungsgruppe zu einem vorhandenen Cluster hinzufügen.  Sie können die Skalierung für den Cluster jederzeit durchführen – auch bei Ausführung von Workloads im Cluster.  Wenn der Cluster skaliert wird, werden Ihre Anwendungen ebenfalls automatisch skaliert.

> [!WARNING]
> Die VM-SKU des primären Knotentyps darf nicht geändert werden, wenn die Clusterintegrität beeinträchtigt ist. Im Falle einer Beeinträchtigung der Clusterintegrität wird der Cluster nur noch weiter destabilisiert, wenn Sie versuchen, die VM-SKU zu ändern.
>
> Wir empfehlen Ihnen, die VM-SKU einer Skalierungsgruppe bzw. eines Knotentyps nur dann zu ändern, wenn sie mit der [Dauerhaftigkeitsstufe „Silber“ oder höher](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) ausgeführt wird. Bei der Änderung der VM-SKU-Größe handelt es sich um einen für Daten schädlichen direkten Infrastrukturvorgang. Ohne eine Möglichkeit der Verzögerung oder Überwachung dieser Änderung ist es möglich, dass der Vorgang bei zustandsbehafteten Diensten zu Datenverlusten führt oder selbst bei zustandslosen Workloads unvorhergesehene Probleme auftreten. Dies betrifft Ihren primären Knotentyp, der zustandsbehaftete Service Fabric-Systemdienste ausführt, oder einen Knotentyp, der Ihre zustandsbehafteten Anwendungsworkloads ausführt.
>

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Aktualisieren der Größe und des Betriebssystems der VMs des primären Knotentyps
Im Folgenden wird der Vorgang zum Aktualisieren der VM-Größe und des Betriebssystems der VMs des primären Knotentyps beschrieben.  Nach dem Upgrade weisen die VMs des primären Knotentyps die Größe „Standard D4_V2“ auf und werden unter Windows Server 2016 Datacenter mit Containern ausgeführt.

> [!WARNING]
> Bevor Sie versuchen, diesen Vorgang für einen Produktionscluster auszuführen, sollten Sie sich die Beispielvorlagen ansehen und den Vorgang mithilfe eines Testclusters überprüfen. Beachten Sie zudem, dass der Cluster zeitweilig nicht verfügbar ist. Sie können KEINE Änderungen an mehreren VMSS vornehmen, die parallel als dasselbe NodeType-Element deklariert sind. Sie müssen separate Bereitstellungsvorgänge durchführen, um Änderungen an jeder NodeType-VMSS einzeln anzuwenden.

1. Stellen Sie den Anfangscluster mit zwei Knotentypen und zwei Skalierungsgruppen (eine Skalierungsgruppe pro Knotentyp) mithilfe dieser [Beispielvorlage](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) und diesen [Parameterdateien](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) bereit.  Beide Skalierungsgruppen weisen die Größe „Standard D2_V2“ auf und werden unter Windows Server 2012 R2 Datacenter ausgeführt.  Warten Sie, bis das Baselineupgrade des Clusters abgeschlossen ist.   
2. Optional: Stellen Sie ein zustandsbehaftetes Beispiel für den Cluster bereit.
3. Nachdem Sie sich für das Upgrade der VMs des primären Knotentyps entschieden haben, können Sie dem primären Knotentyp mithilfe dieser [Beispielvorlage](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) und diesen [Parameterdateien](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) eine neue Skalierungsgruppe hinzufügen, damit der primäre Knotentyp über zwei Skalierungsgruppen verfügt.  Systemdienste und Benutzeranwendungen können zwischen VMs in den zwei unterschiedlichen Skalierungsgruppen migriert werden.  Die neuen Skalierungsgruppen-VMs weisen die Größe „Standard D4_V2“ auf und werden unter Windows Server 2016 Datacenter mit Containern ausgeführt.  Mit der neuen Skalierungsgruppe werden auch ein neuer Lastenausgleich und eine öffentliche IP-Adresse hinzugefügt.  
    Um die neue Skalierungsgruppe in der Vorlage zu finden, suchen Sie nach der Ressource „Microsoft.Compute/virtualMachineScaleSets“, die durch den Parameter *vmNodeType2Name* benannt wird.  Die neue Skalierungsgruppe wird dem primären Knotentyp mithilfe der Einstellung „properties->virtualMachineProfile->extensionProfile->extensions->properties->settings->nodeTypeRef“ hinzugefügt.
4. Überprüfen Sie die Clusterintegrität und ob alle Knoten fehlerfrei sind.
5. Deaktivieren Sie die Knoten in der alten Skalierungsgruppe des primären Knotentyps mit der Absicht, die Knoten zu entfernen. Sie können alle gleichzeitig deaktivieren. Die Vorgänge werden dann in eine Warteschlange eingereiht. Warten Sie, bis alle Knoten deaktiviert sind. Dies kann einige Zeit dauern.  Nachdem die älteren Knoten im Knotentyp deaktiviert sind, werden die Systemdienste und Seed-Knoten zu den VMs der neuen Skalierungsgruppe im primären Knotentyp migriert.
6. Entfernen Sie die ältere Skalierungsgruppe aus dem primären Knotentyp.
7. Entfernen Sie den der alten Skalierungsgruppe zugeordneten Lastenausgleich. Während die neue öffentliche IP-Adresse und der Lastenausgleich für die neue Skalierungsgruppe konfiguriert werden, ist der Cluster nicht verfügbar.  
8. Speichern Sie die DNS-Einstellungen der öffentlichen IP-Adresse, die der alten Skalierungsgruppe des primären Knotentyps zugeordnet ist, in einer Variablen, und entfernen Sie diese öffentliche IP-Adresse.
9. Ersetzen Sie die DNS-Einstellungen der öffentlichen IP-Adresse, die der neuen Skalierungsgruppe des primären Knotentyps zugeordnet ist, durch die DNS-Einstellungen der gelöschten öffentlichen IP-Adresse.  Der Cluster ist jetzt wieder verfügbar.
10. Entfernen Sie den Knotenstatus der Knoten aus dem Cluster.  Wenn die alte Skalierungsgruppe die Dauerhaftigkeitsstufe „Silber“ oder „Gold“ aufwies, wird dieser Schritt automatisch vom System ausgeführt.
11. Wenn Sie die zustandsbehaftete Anwendung in einem vorherigen Schritt bereitgestellt haben, stellen Sie sicher, dass die Anwendung funktionsfähig ist.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.json" -Verbose

# Connect to the cluster and check the cluster health.
$ClusterName= "sfupgradetest.southcentralus.cloudapp.azure.com:19000"
$thumb="F361720F4BD5449F6F083DDE99DC51A86985B25B"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Get-ServiceFabricClusterHealth

# Deploy a new scale set into the primary node type.  Create a new load balancer and public IP address for the new scale set.
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.json" -Verbose

# Check the cluster health again. All 15 nodes should be healthy.
Get-ServiceFabricClusterHealth

# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}

Write-Host "Checking node status..."
foreach($name in $nodeNames){
 
    $state = Get-ServiceFabricNode -NodeName $name 

    $loopTimeout = 50

    do{
        Start-Sleep 5
        $loopTimeout -= 1
        $state = Get-ServiceFabricNode -NodeName $name
        Write-Host "$name state: " $state.NodeDeactivationInfo.Status
    }

    while (($state.NodeDeactivationInfo.Status -ne "Completed") -and ($loopTimeout -ne 0))
    

    if ($state.NodeStatus -ne [System.Fabric.Query.NodeStatus]::Disabled)
    {
        Write-Error "$name node deactivation failed with state" $state.NodeStatus
        exit
    }
}

# Remove the scale set
$scaleSetName="NTvm1"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="adding-an-additional-scale-set-to-an-existing-cluster"></a>Hinzufügen einer weiteren Skalierungsgruppe zu einem vorhandenen Cluster
Das Hinzufügen einer neuen NodeType-VM-Skalierungsgruppe zu einem vorhandenen Cluster lässt sich mit der zuvor erwähnten Durchführung eines Upgrades für den primären Knotentyp vergleichen. Der Unterschied ist lediglich, dass Sie nicht den gleichen NodeTypeRef verwenden, selbstverständlich nicht aktiv verwendete VM-Skalierungsgruppen deaktivieren und dass die Clusterverfügbarkeit nicht verloren geht, wenn der primäre Knotentyp nicht aktualisiert wird. 

Die NodeTypeRef-Eigenschaft wird in den Eigenschaften der Service Fabric-Erweiterung der VM-Skalierungsgruppe deklariert:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

Außerdem müssen Sie diesen neuen Knotentyp der Service Fabric-Clusterressource hinzufügen:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>Nächste Schritte
* Machen Sie sich mit der [Skalierbarkeit von Anwendungen](service-fabric-concepts-scalability.md) vertraut.
* [Skalieren eines Service Fabric-Clusters](service-fabric-tutorial-scale-cluster.md) (horizontal hoch oder herunter)
* [Programmgesteuertes Skalieren eines Service Fabric-Clusters](service-fabric-cluster-programmatic-scaling.md) (per Azure Fluent-Compute-SDK)
* [Horizontales Herunter- oder Hochskalieren eines eigenständigen Clusters](service-fabric-cluster-windows-server-add-remove-nodes.md)

