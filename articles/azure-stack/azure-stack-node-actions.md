---
title: Knotenaktionen für Skalierungseinheiten in Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Knotenstatus anzeigen und die Knotenaktionen für das Ein- und Ausschalten, das Deaktivieren und das Fortsetzen in einem in Azure Stack integrierten System verwenden.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 12/06/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: ced6e2edb570e12b17d14e0552030902161b5d53
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725251"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Knotenaktionen für Skalierungseinheiten in Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme*

In diesem Artikel wird beschrieben, wie Sie den Status einer Skalierungseinheit anzeigen. Sie können die Knoten der Einheit anzeigen. Sie können Knotenaktionen wie Ein-/Ausschalten, Herunterfahren, Entladen, Fortsetzen und Reparieren ausführen. Normalerweise verwenden Sie diese Knotenaktionen für den Austausch von Teilen im Betrieb oder bei der Wiederherstellung eines Knotens.

> [!Important]  
> Alle in diesem Artikel beschriebenen Knotenaktionen sollten immer nur auf einen Knoten gleichzeitig ausgerichtet sein.

## <a name="view-the-node-status"></a>Anzeigen des Knotenstatus

Im Administratorportal können Sie den Status einer Skalierungseinheit und ihrer zugehörigen Knoten anzeigen.

So zeigen Sie den Status einer Skalierungseinheit an

1. Klicken Sie auf die Kachel **Regionsverwaltung**, und wählen Sie die Region aus.
2. Wählen Sie auf der linken Seite unter **Infrastrukturressourcen** die Option **Skalierungseinheiten** aus.
3. Wählen Sie in den Ergebnissen die Skalierungseinheit aus.
4. Wählen Sie links unter **Allgemein** den Eintrag **Knoten** aus.

  Zeigen Sie die folgenden Informationen an:

  - Die Liste der einzelnen Knoten
  - Betriebsstatus (siehe unten die Liste)
  - Energiestatus („wird ausgeführt“ oder „beendet“)
  - Servermodell
  - IP-Adresse des Baseboard-Verwaltungscontrollers (BMC)
  - Gesamtanzahl von Kernen
  - Gesamtspeichermenge

![Status einer Skalierungseinheit](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>Betriebszustände von Knoten

| Status | BESCHREIBUNG |
|----------------------|-------------------------------------------------------------------|
| Wird ausgeführt | Der Knoten ist aktiv an der Skalierungseinheit beteiligt. |
| Beendet | Der Knoten ist nicht verfügbar. |
| Wird hinzugefügt | Der Knoten wird aktiv zur Skalierungseinheit hinzugefügt. |
| Wird repariert | Der Knoten wird aktiv repariert. |
| Wartung  | Der Knoten wurde angehalten, und es wird kein aktiver Benutzerworkload ausgeführt. |
| Korrektur erforderlich | Ein Fehler wurde ermittelt, der eine Reparatur des Knotens erfordert. |

## <a name="scale-unit-node-actions"></a>Knotenaktionen für Skalierungseinheiten

Beim Anzeigen von Informationen zu einem Knoten einer Skalierungseinheit können Sie Knotenaktionen wie die folgenden ausführen:
 - Starten und Beenden (je nach aktuellem Energiestatus)
 - Deaktivieren und Fortsetzen (je nach Betriebsstatus)
 - Reparieren
 - Shutdown

Der Betriebszustand des Knotens bestimmt, welche Optionen verfügbar sind.

Sie müssen Azure Stack PowerShell-Module installieren. Diese Cmdlets befinden sich im **Azs.Fabric.Admin**-Modul. Informationen zum Installieren oder Überprüfen Ihrer Installation der PowerShell für Azure Stack finden Sie unter [Installieren der PowerShell für Azure Stack](azure-stack-powershell-install.md).

## <a name="stop"></a>Beenden

Die Aktion **Beenden** schaltet den Knoten aus. Dies entspricht dem Drücken des Netzschalters. Es wird kein Signal zum Herunterfahren an das Betriebssystem gesendet. Probieren Sie bei geplanten Beenden-Vorgängen immer zuerst den Vorgang zum Herunterfahren aus. 

Diese Aktion wird normalerweise verwendet, wenn ein Knoten nicht mehr reagiert und keine Anforderungen mehr verarbeitet.

Um die Beenden-Aktion auszuführen, öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus:

```PowerShell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Versuchen Sie in dem unwahrscheinlichen Fall, dass die Beenden-Aktion nicht funktioniert, den Vorgang noch mal, und falls er ein zweites Mal fehlschlägt, verwenden Sie stattdessen die BMC-Webbenutzeroberfläche.

Weitere Informationen finden Sie unter [Stop-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Start

Die Aktion **Starten** schaltet den Knoten ein. Dies entspricht dem Drücken des Netzschalters. 
 
Um die Starten-Aktion auszuführen, öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus:

```PowerShell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Versuchen Sie in dem unwahrscheinlichen Fall, dass die Starten-Aktion nicht funktioniert, den Vorgang noch mal, und falls er ein zweites Mal fehlschlägt, verwenden Sie stattdessen die BMC-Webbenutzeroberfläche.

Weitere Informationen finden Sie unter [Start-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Entladen

Die Aktion **Entladen** verschiebt alle aktiven Workloads auf die übrigen Knoten in dieser spezifischen Skalierungseinheit.

Diese Aktion wird in der Regel für den Austausch von Teilen im Betrieb, z.B. beim Ersetzen eines vollständigen Knotens, verwendet.

> [!Important]
> Stellen Sie sicher, dass Sie einen Entladen-Vorgang nur während eines geplanten Wartungsfensters auf einen Knoten anwenden, über das Benutzer benachrichtigt wurden. Unter bestimmten Umständen können bei aktiven Workloads Unterbrechungen auftreten.

Um die Entladen-Aktion auszuführen, öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus:

```PowerShell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Weitere Informationen finden Sie unter [Disable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Fortfahren

Die Aktion **Fortsetzen** setzt einen entladenen Knoten fort und markiert ihn für die Verteilung von Workloads als aktiv. Für frühere Workloads, die auf dem Knoten ausgeführt wurden, wird kein Failback durchgeführt. (Wenn Sie einen Entladen-Vorgang auf einen Knoten anwenden, stellen Sie sicher, dass sie dann ausschalten.  Wenn Sie den Knoten wieder einschalten, ist er nicht als aktiv für die Workloadverteilung gekennzeichnet. Sie müssen zunächst noch die Aktion „Fortsetzen“ verwenden, um den Knoten als aktiv zu kennzeichnen.)

Um die Fortsetzen-Aktion auszuführen, öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus:

```PowerShell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Weitere Informationen finden Sie unter [Enable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Reparieren

Die Aktion **Reparieren** repariert einen Knoten. Verwenden Sie diese Aktion nur für eines der folgenden Szenarien:
 - Vollständiger Knotenaustausch (mit neuen Datenträgern oder ohne diese)
 - Nach dem Ausfall oder dem Austausch einer Hardwarekomponente (sofern in der Dokumentation der FRU (Field Replaceable Unit) empfohlen).

> [!Important]  
> Wenn Sie einen Knoten oder einzelne Hardwarekomponenten ersetzen müssen, finden Sie die genauen Schritte in der FRU-Dokumentation Ihres OEM-Hardwareherstellers. In der FRU-Dokumentation ist auch angegeben, ob Sie die Reparaturaktion nach dem Ersetzen einer Hardwarekomponente ausführen müssen. 

Beim Ausführen der Reparaturaktion müssen Sie die BMC-IP-Adresse angeben. 

Um die Reparieren-Aktion auszuführen, öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie das folgende Cmdlet aus:

  ````PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ````

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Fabric-Administratormodul von Azure Stack finden Sie unter [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.5.0).
