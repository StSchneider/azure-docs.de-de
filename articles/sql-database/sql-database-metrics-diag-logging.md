---
title: Protokollierung von Metriken und Diagnosen für Azure SQL-Datenbank | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure SQL-Datenbank zum Speichern von Statistiken für den Ressourcenverbrauch und die Abfrageausführung konfigurieren.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 49c411487a29a7faa5a6cec5087a85d472309a4b
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044568"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Protokollierung von Metriken und Diagnosen für Azure SQL-Datenbank

Azure SQL-Datenbank, Pools für elastische Datenbanken, die verwaltete Instanz und Datenbanken in der verwalteten Instanz können Metriken und Diagnoseprotokolle zur einfacheren Leistungsüberwachung streamen. Sie können eine Datenbank dazu konfigurieren, Informationen zum Ressourcenverbrauch, zu Workern und Sitzungen sowie zur Konnektivität in eine der folgenden Azure-Ressourcen zu übertragen:

- **Azure SQL-Analyse**: Ermöglicht die intelligente Überwachung Ihrer Azure-Datenbanken mit Leistungsberichten, Warnungen und Empfehlungen zur Risikominderung.
- **Azure Event Hubs**: Ermöglicht die Integration von SQL-Datenbank-Telemetrie in Ihre benutzerdefinierten Überwachungslösungen oder Hotpipelines.
- **Azure Storage**: Ermöglicht zu äußerst kostengünstigen Preisen die Archivierung großer Mengen von Telemetriedaten.

    ![Architecture](./media/sql-database-metrics-diag-logging/architecture.png)

Weitere Informationen zu den Metriken und Protokollkategorien, die von verschiedenen Azure-Diensten unterstützt werden, finden Sie in den folgenden Themen:

- [Überblick über Metriken in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Übersicht über Azure-Diagnose](../azure-monitor/platform/diagnostic-logs-overview.md)

Dieser Artikel enthält Anleitungen zum Aktivieren der Diagnosetelemetrie für Datenbanken, Pools für elastische Datenbanken und die verwaltete Instanz. Zudem erfahren Sie, wie Sie die Azure SQL-Analyse als Überwachungstool zum Anzeigen der Diagnosetelemetriedaten für eine Datenbank konfigurieren.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Aktivieren der Protokollierung von Diagnosetelemetriedaten

Sie können die Protokollierung von Metriken und Diagnosetelemetriedaten mit einer der folgenden Methoden aktivieren und verwalten:

- Azure-Portal
- PowerShell
- Azure-Befehlszeilenschnittstelle
- Azure Monitor-REST-API
- Azure Resource Manager-Vorlage

Wenn Sie die Protokollierung von Metriken und Diagnosen aktivieren, müssen Sie das Azure-Ressourcenziel für die Erfassung der Diagnosetelemetriedaten angeben. Folgende Optionen sind verfügbar:

- Azure SQL-Analysen
- Azure Event Hubs
- Azure Storage

Sie können eine neue Azure-Ressource bereitstellen oder eine vorhandene Ressource auswählen. Nachdem Sie mithilfe der Option **Diagnoseeinstellungen** eine Ressource ausgewählt haben, geben Sie an, welche Daten erfasst werden sollen.

> [!NOTE]
> Wenn Sie auch Pools für elastische Datenbanken oder die verwaltete Instanz verwenden, empfehlen wir, die Diagnosetelemetrie für diese Ressourcen ebenfalls zu aktivieren. Datenbankcontainer in Pools für elastische Datenbanken und die verwaltete Instanz verfügen über eigene separate Diagnosetelemetrie.

## <a name="enable-logging-for-azure-sql-database-or-databases-in-managed-instance"></a>Aktivieren der Protokollierung für Azure SQL-Datenbank bzw. Datenbanken in einer verwalteten Instanz

Aktivieren Sie die Protokollierung von Metriken und Diagnosen für SQL-Datenbank und Datenbanken in der verwalteten Instanz. Sie ist nicht standardmäßig aktiviert.

Sie können Azure SQL-Datenbanken und Datenbanken in der verwalteten Instanz zur Erfassung der folgenden Diagnosetelemetriedaten einrichten:

| Überwachen von Telemetriedaten für Datenbanken | Unterstützung für Azure-SQL-Datenbank | Unterstützung für Datenbanken der verwalteten Instanz |
| :------------------- | ------------------- | ------------------- |
| [AllMetrics](sql-database-metrics-diag-logging.md#all-metrics): Enthält DTU-/CPU-Prozentsatz, DTU/CPU-Limit, gelesene physische Daten in Prozent, Protokollschreibvorgänge in Prozent, Verbindungen mit dem Status „Erfolgreich“, „Fehler“ und „Durch Firewall blockiert“, Sitzungen in Prozent, Worker in Prozent, Speicher, Speicher in Prozent und XTP-Speicher in Prozent. | JA | Nein  |
| [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): Enthält Informationen zur Laufzeitstatistik der Abfrage, z. B. CPU-Nutzung und Abfragedauer. | JA | JA |
| [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): Enthält Informationen zur Wartestatistik der Abfrage (worauf Ihre Abfragen gewartet haben), z. B. CPU, PROTOKOLL und SPERRUNG. | JA | JA |
| [Errors](sql-database-metrics-diag-logging.md#errors-dataset): Enthält Informationen zu SQL-Fehlern in der Datenbank. | JA | Nein  |
| [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset): Enthält Informationen zur Wartezeit der Datenbank für die verschiedenen Wartezeittypen. | JA | Nein  |
| [Timeouts](sql-database-metrics-diag-logging.md#time-outs-dataset): Enthält Informationen zu Zeitlimits für die Datenbank. | JA | Nein  |
| [Blocks](sql-database-metrics-diag-logging.md#blockings-dataset): Enthält Informationen zu blockierenden Ereignissen für die Datenbank. | JA | Nein  |
| [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): Enthält Intelligent Insights-Informationen zur Leistung. Weitere Informationen finden Sie unter [Intelligent Insights](sql-database-intelligent-insights.md). | JA | JA |

### <a name="azure-portal"></a>Azure-Portal

Sie verwenden das Menü **Diagnoseeinstellungen** für die einzelnen Datenbanken im Azure-Portal, um das Streaming von Diagnosetelemetriedaten für Azure SQL-Datenbanken und Datenbanken in der verwalteten Instanz zu konfigurieren. Sie können die folgenden Ziele festlegen: Azure Storage, Azure Event Hubs und Azure Log Analytics.

### <a name="configure-streaming-of-diagnostics-telemetry-for-azure-sql-database"></a>Konfigurieren des Streamings von Diagnosetelemetriedaten für Azure SQL-Datenbank

   ![Symbol der SQL-Datenbank](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Führen Sie die folgenden Schritte aus, um das Streaming von Diagnosetelemetriedaten für Azure SQL-Datenbank zu aktivieren:

1. Navigieren Sie zu Ihrer Azure SQL-Datenbank-Ressource.
1. Wählen Sie **Diagnoseeinstellungen** aus.
1. Klicken Sie auf **Diagnose aktivieren**, wenn noch keine Einstellungen vorgenommen wurden, oder auf **Einstellung bearbeiten**, um eine vorhandene Einstellung zu bearbeiten.
   - Sie können bis zu drei parallele Verbindungen zum Streamen von Diagnosetelemetriedaten erstellen.
   - Wählen Sie **+ Diagnoseeinstellung hinzufügen** aus, um das parallele Streaming von Diagnosedaten in mehrere Ressourcen zu konfigurieren.

   ![Aktivieren der Diagnose für SQL-Datenbank](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. Geben Sie einen Einstellungsnamen zu Referenzzwecken ein.
1. Wählen Sie eine Zielressource für das Streaming von Diagnosedaten aus: **In einem Speicherkonto archivieren**, **An einen Event Hub streamen** oder **An Log Analytics senden**.
1. Aktivieren Sie für die standardmäßige, ereignisbasierte Überwachungsoberfläche die folgenden Kontrollkästchen für Datenbankdiagnoseprotokoll-Telemetrie: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeouts**, **Blocks** und **Deadlocks**.
1. Aktivieren Sie für eine erweiterte Überwachungsoberfläche mit einem Aktualisierungsintervall von einer Minute das Kontrollkästchen für **AllMetrics**.
1. Wählen Sie **Speichern** aus.

   ![Konfigurieren der Diagnose für SQL-Datenbank](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)

> [!NOTE]
> Sicherheitsüberwachungsprotokolle können nicht über die Einstellungen für die Datenbankdiagnose aktiviert werden. Informationen zum Aktivieren des Streamings von Überwachungsprotokollen finden Sie unter [Einrichten der Überwachung für Ihre Datenbank](sql-database-auditing.md#subheading-2) und [SQL Audit logs in Azure Log Analytics and Azure Event Hubs](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/) (SQL-Überwachungsprotokolle in Azure Log Analytics und Azure Event Hubs).
> [!TIP]
> Wiederholen Sie diese Schritte für jede Azure SQL-Datenbank, die überwacht werden soll.

### <a name="configure-streaming-of-diagnostics-telemetry-for-databases-in-managed-instance"></a>Konfigurieren des Streamings von Diagnosetelemetriedaten für Datenbanken in verwalteten Instanzen

   ![Symbol der Datenbank in der verwalteten Instanz](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Um das Streaming von Diagnosetelemetriedaten für Datenbanken in der verwalteten Instanz zu aktivieren, gehen Sie folgendermaßen vor:

1. Navigieren Sie zu Ihrer Datenbank in der verwalteten Instanz.
2. Wählen Sie **Diagnoseeinstellungen** aus.
3. Klicken Sie auf **Diagnose aktivieren**, wenn noch keine Einstellungen vorgenommen wurden, oder auf **Einstellung bearbeiten**, um eine vorhandene Einstellung zu bearbeiten.
   - Sie können bis zu drei parallele Verbindungen zum Streamen von Diagnosetelemetriedaten erstellen.
   - Wählen Sie **+ Diagnoseeinstellung hinzufügen** aus, um das parallele Streaming von Diagnosedaten in mehrere Ressourcen zu konfigurieren.

   ![Aktivieren der Diagnose für die Datenbank in einer verwalteten Instanz](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. Geben Sie einen Einstellungsnamen zu Referenzzwecken ein.
5. Wählen Sie eine Zielressource für das Streaming von Diagnosedaten aus: **In einem Speicherkonto archivieren**, **An einen Event Hub streamen** oder **An Log Analytics senden**.
6. Aktivieren Sie die Kontrollkästchen für die Datenbankdiagnosetelemetrie: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** und **Errors**.
7. Wählen Sie **Speichern** aus.

   ![Konfigurieren der Diagnose für die Datenbank in einer verwalteten Instanz](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)

> [!TIP]
> Wiederholen Sie diese Schritte für jede Datenbank in der verwalteten Instanz, die überwacht werden soll.

## <a name="enable-logging-for-elastic-pools-or-managed-instance"></a>Aktivieren der Protokollierung für Pools für elastische Datenbanken oder die verwaltete SQL-Datenbank-Instanz

Aktivieren Sie Diagnosetelemetrie für Pools für elastische Datenbanken und die verwaltete Instanz als Datenbankcontainer. Diese verfügen über eigene Diagnosetelemetrie, die standardmäßig nicht aktiviert ist.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Konfigurieren des Streamings von Diagnosetelemetriedaten für Pools für elastische Datenbanken

   ![Symbol des Pools für elastische Datenbanken](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

Sie können eine Ressource des Pools für elastische Datenbanken zum Erfassen der folgenden Diagnosetelemetriedaten einrichten:

| Ressource | Überwachte Telemetriedaten |
| :------------------- | ------------------- |
| **Pool für elastische Datenbanken** | [Alle Metriken](sql-database-metrics-diag-logging.md#all-metrics) enthält eDTU-/CPU-Prozentsatz, eDTU-/CPU-Limit, Prozentsatz der gelesenen physischen Daten, Prozentsatz für Protokollschreibvorgang, Sitzungen in Prozent, Worker in Prozent, Speicher, Speicher in Prozent, Speicherbegrenzung, XTP-Speicher in Prozent. |

Um das Streaming von Diagnosetelemetriedaten für eine Ressource des Pools für elastische Datenbanken zu aktivieren, gehen Sie folgendermaßen vor:

1. Navigieren Sie im Azure-Portal zur Ressource des Pools für elastische Datenbanken.
1. Wählen Sie **Diagnoseeinstellungen** aus.
1. Klicken Sie auf **Diagnose aktivieren**, wenn noch keine Einstellungen vorgenommen wurden, oder auf **Einstellung bearbeiten**, um eine vorhandene Einstellung zu bearbeiten.

   ![Aktivieren der Diagnose für Pools für elastische Datenbanken](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Geben Sie einen Einstellungsnamen zu Referenzzwecken ein.
1. Wählen Sie eine Zielressource für das Streaming von Diagnosedaten aus: **In einem Speicherkonto archivieren**, **An einen Event Hub streamen** oder **An Log Analytics senden**.
1. Wählen Sie für Log Analytics **Konfigurieren** aus, und erstellen Sie einen neuen Arbeitsbereich, indem Sie auf **+ Neuen Arbeitsbereich erstellen** klicken, oder wählen Sie einen vorhandenen Arbeitsbereich aus.
1. Aktivieren Sie das Kontrollkästchen für Diagnosetelemetrie des Pools für elastische Datenbanken: **AllMetrics**.
1. Wählen Sie **Speichern** aus.

   ![Konfigurieren der Diagnose für Pools für elastische Datenbanken](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

> [!TIP]
> Wiederholen Sie diese Schritte für jeden Pool für elastische Datenbanken, der überwacht werden soll.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instance"></a>Konfigurieren des Streamings von Diagnosetelemetriedaten für verwaltete Instanzen

   ![Symbol der verwalteten Instanz](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

Sie können eine Ressource der verwalteten Instanz zum Erfassen der folgenden Diagnosetelemetriedaten einrichten:

| Ressource | Überwachte Telemetriedaten |
| :------------------- | ------------------- |
| **Verwaltete Instanz** | [ResourceUsageStats](sql-database-metrics-diag-logging.md#logs-for-managed-instance) enthält die Anzahl virtueller Kerne, den durchschnittlichen CPU-Prozentsatz, E/A-Anforderungen, gelesene/geschriebene Bytes, den reservierten und genutzten Speicherplatz. |

Führen Sie die folgenden Schritte aus, um das Streaming von Diagnosetelemetriedaten für eine Ressource der verwalteten Instanz zu aktivieren:

1. Navigieren Sie im Azure-Portal zur Ressource der verwalteten Instanz.
1. Wählen Sie **Diagnoseeinstellungen** aus.
1. Klicken Sie auf **Diagnose aktivieren**, wenn noch keine Einstellungen vorgenommen wurden, oder auf **Einstellung bearbeiten**, um eine vorhandene Einstellung zu bearbeiten.

   ![Aktivieren der Diagnose für verwaltete Instanzen](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Geben Sie einen Einstellungsnamen zu Referenzzwecken ein.
1. Wählen Sie eine Zielressource für das Streaming von Diagnosedaten aus: **In einem Speicherkonto archivieren**, **An einen Event Hub streamen** oder **An Log Analytics senden**.
1. Wählen Sie für Log Analytics **Konfigurieren** aus, und erstellen Sie einen neuen Arbeitsbereich, indem Sie auf **+ Neuen Arbeitsbereich erstellen** klicken, oder verwenden Sie einen vorhandenen Arbeitsbereich.
1. Aktivieren Sie das Kontrollkästchen für Diagnosetelemetrie der Instanz: **ResourceUsageStats**.
1. Wählen Sie **Speichern** aus.

   ![Konfigurieren der Diagnose für verwaltete Instanzen](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

> [!TIP]
> Wiederholen Sie diese Schritte für jede verwaltete Instanz, die überwacht werden soll.

### <a name="powershell"></a>PowerShell

Sie können die Protokollierung von Metriken und Diagnosen mit PowerShell aktivieren.

- Verwenden Sie den folgenden Befehl, um das Speichern von Diagnoseprotokollen in einem Speicherkonto zu aktivieren:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Die Speicherkonto-ID ist die Ressourcen-ID für das Zielspeicherkonto.

- Verwenden Sie den folgenden Befehl, um das Streamen von Diagnoseprotokollen an eine Event Hub-Instanz zu aktivieren:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Die Azure Service Bus-Regel-ID ist eine Zeichenfolge mit dem folgenden Format:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Verwenden Sie den folgenden Befehl, um das Senden von Diagnoseprotokollen an einen Log Analytics-Arbeitsbereich zu aktivieren:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Sie können die Ressourcen-ID mit dem folgenden Befehl aus Ihrem Log Analytics-Arbeitsbereich abrufen:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Sie können diese Parameter miteinander kombinieren, um mehrere Ausgabeoptionen zu aktivieren.

### <a name="to-configure-multiple-azure-resources"></a>So konfigurieren Sie mehrere Azure-Ressourcen

Wenn mehrere Abonnements unterstützt werden, verwenden Sie das PowerShell-Skript aus [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/) (Aktivieren der Protokollierung der Azure-Ressourcenmetriken mit PowerShell).

Geben Sie die Arbeitsbereichsressourcen-ID \<$WSID\> als Parameter an, wenn Sie das Skript `Enable-AzureRMDiagnostics.ps1` ausführen, um Diagnosedaten von mehreren Ressourcen an den Arbeitsbereich zu senden.

- Verwenden Sie zum Abrufen der Arbeitsbereichs-ID \<$WSID\> des Ziels für Ihre Diagnosedaten das folgende Skript:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```
   Ersetzen Sie \<subID\> durch die Abonnement-ID, \<RG_NAME\> durch den Namen der Ressourcengruppe und \<WS_NAME\> durch den Namen des Arbeitsbereichs.

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Sie können die Protokollierung von Metriken und Diagnosen mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) aktivieren.

- Verwenden Sie den folgenden Befehl, um das Speichern von Diagnoseprotokollen in einem Speicherkonto zu aktivieren:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   Die Speicherkonto-ID ist die Ressourcen-ID für das Zielspeicherkonto.

- Verwenden Sie den folgenden Befehl, um das Streaming von Diagnoseprotokollen an einen Event Hub zu aktivieren:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   Die Service Bus-Regel-ID ist eine Zeichenfolge mit dem folgenden Format:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Verwenden Sie den folgenden Befehl, um das Senden von Diagnoseprotokollen an einen Log Analytics-Arbeitsbereich zu aktivieren:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Sie können diese Parameter miteinander kombinieren, um mehrere Ausgabeoptionen zu aktivieren.

### <a name="rest-api"></a>REST-API

Informieren Sie sich darüber, wie Sie [Diagnoseeinstellungen mithilfe der Azure Monitor-REST-API ändern](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Resource Manager-Vorlage

Informieren Sie sich darüber, wie Sie [Diagnoseeinstellungen beim Erstellen von Ressourcen mithilfe der Resource Manager-Vorlage aktivieren](../azure-monitor/platform/diagnostic-logs-stream-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Streamen in Azure SQL-Analyse

Die Azure SQL-Analyse ist eine Cloudlösung, die die Leistung von Azure SQL-Datenbanken, Pools für elastische Datenbanken und der verwalteten Instanz in großem Maßstab abonnementübergreifend überwacht. Die Lösung ermöglicht Ihnen das Erfassen und Visualisieren von Leistungsmetriken für Azure SQL-Datenbank und verfügt über integrierte intelligente Funktionen für die Behandlung von Leistungsproblemen.

![Azure SQL-Analyse – Übersicht](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

Metriken und Diagnoseprotokolle für SQL-Datenbank können mit der integrierten Option **An Log Analytics senden** auf der Registerkarte „Diagnoseeinstellungen“ im Portal in die Azure SQL-Analyse gestreamt werden. Sie können Log Analytics auch mithilfe einer Diagnoseeinstellung über PowerShell-Cmdlets, die Azure-Befehlszeilenschnittstelle oder die Azure Monitor-REST-API aktivieren.

### <a name="installation-overview"></a>Übersicht über die Installation

Sie können eine SQL-Datenbankflotte mit der Azure SQL-Analyse überwachen. Führen Sie die folgenden Schritte aus:

1. Erstellen Sie im Azure Marketplace eine Azure SQL-Analyselösung.
2. Erstellen Sie in der Lösung einen Überwachungsarbeitsbereich.
3. Konfigurieren Sie Datenbanken zum Streamen von Diagnosetelemetriedaten in den Arbeitsbereich.

Wenn Sie Pools für elastische Datenbanken oder die verwaltete Instanz verwenden, müssen Sie auch das Streaming von Diagnosetelemetriedaten aus diesen Ressourcen konfigurieren.

### <a name="create-azure-sql-analytics-resource"></a>Erstellen von Azure SQL-Analyse-Ressourcen

1. Suchen Sie im Azure Marketplace nach „Azure SQL-Analyse“, und wählen Sie die Lösung aus.

   ![Suchen von Azure SQL-Analyse im Portal](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Wählen Sie auf dem Übersichtsbildschirm der Lösung die Option **Erstellen** aus.

3. Tragen Sie in das Azure SQL-Analyse-Formular die erforderlichen zusätzlichen Informationen ein: Name des Arbeitsbereichs, Abonnement, Ressourcengruppe, Ort und Tarif.

   ![Konfigurieren von Azure SQL-Analyse im Portal](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Klicken Sie zum Bestätigen auf **OK** und anschließend auf **Erstellen**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Konfigurieren von Datenbanken zum Aufzeichnen von Metrik- und Diagnoseprotokollen

Die Verwendung des Azure-Portals ist die einfachste Möglichkeit zum Konfigurieren des Ziels, in dem Datenbanken Metriken aufzeichnen. Navigieren Sie wie zuvor beschrieben im Azure-Portal zu Ihrer SQL-Datenbank-Ressource, und wählen Sie **Diagnoseeinstellungen** aus.

Falls Sie Pools für elastische Datenbanken oder die verwaltete Instanz verwenden, müssen Sie auch Diagnoseeinstellungen in diesen Ressourcen konfigurieren, um das Streamen der Diagnosetelemetriedaten in den Arbeitsbereich zu aktivieren.

### <a name="use-the-sql-analytics-solution"></a>Verwenden der Lösung „SQL-Analyse“

Sie können die SQL-Analyse als ein hierarchisches Dashboard verwenden, um Ihre SQL-Datenbank-Ressourcen anzuzeigen. Informationen zum Verwenden der Lösung „SQL-Analyse“ finden Sie unter [Überwachen der SQL-Datenbank mithilfe der Lösung „SQL-Analyse“](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Streamen an Event Hubs

Sie können Metriken und Diagnoseprotokolle für SQL-Datenbank mit der integrierten Option **An einen Event Hub streamen** im Azure-Portal in Event Hubs streamen. Sie können die Service Bus-Regel-ID auch aktivieren, indem Sie eine Diagnoseeinstellung über PowerShell-Cmdlets, die Azure CLI oder die Azure Monitor-REST-API verwenden.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Welche Vorgänge können mit Metrik- und Diagnoseprotokollen in Event Hub ausgeführt werden?

Nachdem die ausgewählten Daten an Event Hubs gestreamt wurden, sind Sie der Einrichtung fortgeschrittener Überwachungsszenarien ein ganzes Stück näher gekommen. Event Hubs fungiert als Eingangsportal für eine Ereignispipeline. Nachdem Daten in einem Event Hub erfasst wurden, können sie mit einem beliebigen Echtzeitanalyseanbieter oder Speicheradapter transformiert und gespeichert werden. Event Hubs entkoppelt die Produktion eines Ereignisdatenstroms von der Nutzung dieser Ereignisse. Auf diese Weise können Ereignisconsumer auf die Ereignisse gemäß ihrem eigenen Zeitplan zugreifen. Weitere Informationen zu Event Hubs finden Sie hier:

- [Was sind Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Erste Schritte mit Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Sie können gestreamte Metriken in Event Hubs für folgende Zwecke verwenden:

- **Anzeigen der Dienstintegrität durch Streamen von Daten zum langsamsten Pfad an PowerBI**. Mithilfe von Event Hubs, Stream Analytics und PowerBI können Sie sich anhand Ihrer Metrik- und Diagnosedaten problemlos und nahezu in Echtzeit einen Einblick in Ihre Azure-Dienste verschaffen. [Stream Analytics und Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md) bietet eine gute Übersicht über die Einrichtung von Event Hubs, die Verarbeitung von Daten mit Stream Analytics und die Verwendung von PowerBI als Ausgabe.

- **Streamen von Protokollen in Protokollierungs- und Telemetriedatenströme von Drittanbietern**. Mithilfe des Event Hubs-Streamings können Sie Ihre Metriken und Diagnoseprotokolle in verschiedene Überwachungs- und Protokollanalyselösungen von Drittanbietern übertragen.

- **Erstellen einer benutzerdefinierten Telemetrie- und Protokollierungsplattform** Haben Sie bereits eine benutzerdefinierte Telemetrieplattform, oder erwägen Sie die Erstellung einer solchen Plattform? Der Event Hubs-Dienst beruht auf dem hochgradig skalierbaren Veröffentlichen-Abonnieren-Muster und ermöglicht Ihnen daher die flexible Erfassung von Diagnoseprotokollen. Informationen zur Verwendung von Event Hubs für eine globale Telemetrieplattform finden Sie in der [Anleitung von Dan Rosanova](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Streamen in den Speicher

Sie können Metriken und Diagnoseprotokolle für SQL-Datenbank mithilfe der integrierten Option **In ein Speicherkonto archivieren** im Azure-Portal in Azure Storage speichern. Sie können Storage auch mithilfe einer Diagnoseeinstellung über PowerShell-Cmdlets, die Azure-Befehlszeilenschnittstelle oder die Azure Monitor-REST-API aktivieren.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Schema der Metrik- und Diagnoseprotokolle im Speicherkonto

Nachdem Sie die Sammlung von Metrik- und Diagnoseprotokollen eingerichtet haben, wird ein Speichercontainer im ausgewählten Speicherkonto erstellt, wenn die ersten Datenzeilen verfügbar sind. Die Struktur der Blobs sieht wie folgt aus:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Einfacher ausgedrückt:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ein Blobname für alle Metriken kann beispielsweise wie folgt lauten:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Ein Blobname für das Speichern von Daten aus einem Pool für elastische Datenbanken sieht wie folgt aus:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Herunterladen von Metriken und Protokollen aus Storage

Weitere Informationen zum [Herunterladen von Metrik- und Diagnoseprotokollen aus Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="data-retention-policy-and-pricing"></a>Datenaufbewahrungsrichtlinie und Preisübersicht

Wenn Sie Event Hubs oder ein Speicherkonto auswählen, können Sie eine Aufbewahrungsrichtlinie angeben. Diese Richtlinie löscht Daten, die älter als ein ausgewählter Zeitraums sind. Wenn Sie Log Analytics angeben, hängt die Aufbewahrungsrichtlinie vom ausgewählten Tarif ab. In diesem Fall können Sie mit den verfügbaren kostenlosen Datenerfassungseinheiten jeden Monat mehrere Datenbanken kostenlos überwachen. Für die Nutzung von Diagnosetelemetrie, die über die kostenlosen Einheiten hinausgeht, können Kosten anfallen. Beachten Sie, dass bei aktiven Datenbanken mit umfangreicheren Workloads mehr Daten als bei Datenbanken im Leerlauf erfasst werden. Weitere Informationen finden Sie unter [Log Analytics-Preise](https://azure.microsoft.com/pricing/details/monitor/).

Wenn Sie die Azure SQL-Analyse verwenden, können Sie Ihre Datenerfassungsnutzung in der Lösung überwachen, indem Sie im Navigationsmenü der Azure SQL-Analyse die Option **OMS-Arbeitsbereich** und dann **Nutzung und** **geschätzte Kosten** auswählen.

## <a name="metrics-and-logs-available"></a>Verfügbare Metriken und Protokolle

Mit der [SQL-Analyse-Sprache](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) können Sie die gesammelten Überwachungstelemetriedaten für Ihre eigene _benutzerdefinierte Analyse_ und _Anwendungsentwicklung_ verwenden.

## <a name="all-metrics"></a>Alle Metriken

In den folgenden Tabellen finden Sie Details zu allen Metriken für die verschiedenen Ressourcen.

### <a name="all-metrics-for-elastic-pools"></a>Alle Metriken für Pools für elastische Datenbanken

|**Ressource**|**Metriken**|
|---|---|
|Pool für elastische Datenbanken|eDTU-Prozentsatz, eDTU-Verwendung, eDTU-Limit, CPU-Prozentsatz, Prozentsatz der gelesen physischen Daten, Prozentsatz für Protokollschreibvorgang, Sitzungen in Prozent, Worker in Prozent, Speicher, Speicher in Prozent, Speicherbegrenzung, XTP-Speicher in Prozent |

### <a name="all-metrics-for-azure-sql-databases"></a>Alle Metriken für Azure SQL-Datenbanken

|**Ressource**|**Metriken**|
|---|---|
|Azure SQL-Datenbank|DTU-Prozentsatz, DTU-Verwendung, DTU-Limit, CPU-Prozentsatz, Prozentsatz der gelesen physischen Daten, Prozentsatz für Protokollschreibvorgang, Verbindungen mit Status Erfolgreich/Fehlgeschlagen/Durch Firewall blockiert, Sitzungen in Prozent, Worker in Prozent, Speicher, Speicher in Prozent, XTP-Speicher in Prozent und Deadlocks |

## <a name="logs-for-managed-instance"></a>Protokolle für die verwaltete Instanz

In der folgenden Tabelle finden Sie Details zu den Protokollen für die verwaltete Instanz.

### <a name="resource-usage-statistics"></a>Statistiken zur Ressourcennutzung

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure|
|TimeGenerated [UTC]|Zeitstempel für die Aufzeichnung des Protokolls |
|Typ|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category (Kategorie)|Name der Kategorie Immer: ResourceUsageStats |
|Ressource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: MANAGEDINSTANCES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name der verwalteten Instanz |
|ResourceId|Ressourcen-URI |
|SKU_s|Produkt-SKU der verwalteten Instanz. |
|virtual_core_count_s|Anzahl verfügbarer virtueller Kerne |
|avg_cpu_percent_s|Durchschnittlicher CPU-Prozentsatz |
|reserved_storage_mb_s|Reservierte Speicherkapazität auf einer verwalteten Instanz. |
|storage_space_used_mb_s|Genutzter Speicherplatz auf einer verwalteten Instanz. |
|io_requests_s|Anzahl von IOPS |
|io_bytes_read_s|Gelesene IOPS-Bytes |
|io_bytes_written_s|Geschriebene IOPS-Bytes |

## <a name="logs-for-azure-sql-databases-and-managed-instance-databases"></a>Protokolle für Azure SQL-Datenbanken und Datenbanken der verwalteten Instanz

In den folgenden Tabellen finden Sie Details zu Protokollen für Azure SQL-Datenbanken und Datenbanken der verwalteten Instanz.

### <a name="query-store-runtime-statistics"></a>Laufzeitstatistiken für den Abfragespeicher

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure |
|TimeGenerated [UTC]|Zeitstempel für die Aufzeichnung des Protokolls |
|Typ|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category (Kategorie)|Name der Kategorie Immer: QueryStoreRuntimeStatistics |
|NameVorgang|Name des Vorgangs. Immer: QueryStoreRuntimeStatisticsEvent |
|Ressource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: SERVERS/DATABASES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name des Servers für die Datenbank |
|ElasticPoolName_s|Name des Pools für elastische Datenbanken für die Datenbank (sofern vorhanden) |
|DatabaseName_s|Name der Datenbank |
|ResourceId|Ressourcen-URI |
|query_hash_s|Abfragehash |
|query_plan_hash_s|Hash des Abfrageplans |
|statement_sql_handle_s|SQL-Anweisungshandle |
|interval_start_time_d|Beginn des DateTimeOffsets des Intervalls in der Anzahl von Takten ab 1900-1-1 |
|interval_end_time_d|Ende des DateTimeOffsets des Intervalls in der Anzahl von Takten ab 1900-1-1 |
|logical_io_writes_d|Gesamtanzahl von logischen E/A-Schreibvorgängen |
|max_logical_io_writes_d|Maximale Anzahl von logischen E/A-Schreibvorgängen pro Ausführung |
|physical_io_reads_d|Gesamtanzahl von physischen E/A-Lesevorgängen |
|max_physical_io_reads_d|Maximale Anzahl von logischen E/A-Lesevorgängen pro Ausführung |
|logical_io_reads_d|Gesamtanzahl von logischen E/A-Lesevorgängen |
|max_logical_io_reads_d|Maximale Anzahl von logischen E/A-Lesevorgängen pro Ausführung |
|execution_type_d|Ausführungstyp |
|count_executions_d|Anzahl von Ausführungen der Abfrage |
|cpu_time_d|Gesamte von der Abfrage verbrauchte CPU-Zeit in Mikrosekunden |
|max_cpu_time_d|Maximale von einer einzelnen Ausführung verbrauchte CPU-Zeit in Mikrosekunden |
|dop_d|Summe des Grads an Parallelität |
|max_dop_d|Maximaler Grad an Parallelität für eine einzelne Ausführung |
|rowcount_d|Gesamtanzahl zurückgegebener Zeilen |
|max_rowcount_d|Maximale Anzahl zurückgegebener Zeilen in einer einzelnen Ausführung |
|query_max_used_memory_d|Gesamtmenge des verwendeten Arbeitsspeichers in KB |
|max_query_max_used_memory_d|Maximale von einer einzelnen Ausführung verwendete Menge an Arbeitsspeicher in KB |
|duration_d|Gesamtausführungszeit in Mikrosekunden |
|max_duration_d|Maximale Ausführungszeit einer einzelnen Ausführung |
|num_physical_io_reads_d|Gesamtanzahl von physischen Lesevorgängen |
|max_num_physical_io_reads_d|Maximale Anzahl von physischen Lesevorgängen pro Ausführung |
|log_bytes_used_d|Gesamtanzahl verwendeter Protokollbytes |
|max_log_bytes_used_d|Maximale Anzahl verwendeter Protokollbytes pro Ausführung |
|query_id_d|ID der Abfrage im Abfragespeicher |
|plan_id_d|ID des Plans im Abfragespeicher |

Weitere Informationen zu [Laufzeitstatistikdaten des Abfragespeichers](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Wartestatistiken des Abfragespeichers

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure |
|TimeGenerated [UTC]|Zeitstempel für die Aufzeichnung des Protokolls |
|Typ|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category (Kategorie)|Name der Kategorie Immer: QueryStoreWaitStatistics |
|NameVorgang|Name des Vorgangs. Immer: QueryStoreWaitStatisticsEvent |
|Ressource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: SERVERS/DATABASES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name des Servers für die Datenbank |
|ElasticPoolName_s|Name des Pools für elastische Datenbanken für die Datenbank (sofern vorhanden) |
|DatabaseName_s|Name der Datenbank |
|ResourceId|Ressourcen-URI |
|wait_category_s|Kategorie des Wartevorgangs |
|is_parameterizable_s|Parametrisierbarkeit der Abfrage |
|statement_type_s|Anweisungstyp |
|statement_key_hash_s|Schlüsselhash der Anweisung |
|exec_type_d|Ausführungstyp |
|total_query_wait_time_ms_d|Gesamte Wartezeit der Abfrage für die spezifische Wartekategorie |
|max_query_wait_time_ms_d|Maximale Wartezeit der Abfrage in einer einzelnen Ausführung der bestimmten Wartekategorie |
|query_param_type_d|0 |
|query_hash_s|Abfragehash im Abfragespeicher |
|query_plan_hash_s|Hash des Abfrageplans im Abfragespeicher |
|statement_sql_handle_s|Anweisungshandle im Abfragespeicher |
|interval_start_time_d|Beginn des DateTimeOffsets des Intervalls in der Anzahl von Takten ab 1900-1-1 |
|interval_end_time_d|Ende des DateTimeOffsets des Intervalls in der Anzahl von Takten ab 1900-1-1 |
|count_executions_d|Anzahl von Ausführungen der Abfrage |
|query_id_d|ID der Abfrage im Abfragespeicher |
|plan_id_d|ID des Plans im Abfragespeicher |

Weitere Informationen zu [Wartestatistikdaten des Abfragespeichers](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Fehlerdataset

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure |
|TimeGenerated [UTC]|Zeitstempel für die Aufzeichnung des Protokolls |
|Typ|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQ |
|Category (Kategorie)|Name der Kategorie Immer: Errors |
|NameVorgang|Name des Vorgangs. Immer: ErrorEvent |
|Ressource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: SERVERS/DATABASES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name des Servers für die Datenbank |
|ElasticPoolName_s|Name des Pools für elastische Datenbanken für die Datenbank (sofern vorhanden) |
|DatabaseName_s|Name der Datenbank |
|ResourceId|Ressourcen-URI |
|Message|Fehlermeldungen in Nur-Text |
|user_defined_b|Benutzerdefiniertes Fehlerbit |
|error_number_d|Fehlercode |
|Severity|Schweregrad des Fehlers |
|state_d|Status des Fehlers |
|query_hash_s|Der Abfragehash der fehlerhaften Abfrage (falls verfügbar) |
|query_plan_hash_s|Hash des Abfrageplans der fehlerhaften Abfrage (falls verfügbar) |

Weitere Informationen zu [SQL Server-Fehlermeldungen](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Dataset zur Wartestatistik der Datenbank

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure |
|TimeGenerated [UTC]|Zeitstempel für die Aufzeichnung des Protokolls |
|Typ|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category (Kategorie)|Name der Kategorie Immer: DatabaseWaitStatistics |
|NameVorgang|Name des Vorgangs. Immer: DatabaseWaitStatisticsEvent |
|Ressource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: SERVERS/DATABASES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name des Servers für die Datenbank |
|ElasticPoolName_s|Name des Pools für elastische Datenbanken für die Datenbank (sofern vorhanden) |
|DatabaseName_s|Name der Datenbank |
|ResourceId|Ressourcen-URI |
|wait_type_s|Name des Wartetyps |
|start_utc_date_t [UTC]|Startzeit des gemessenen Zeitraums |
|end_utc_date_t [UTC]|Endzeit des gemessenen Zeitraums |
|delta_max_wait_time_ms_d|Maximale Wartezeit pro Ausführung |
|delta_signal_wait_time_ms_d|Gesamte Signalwartezeit |
|delta_wait_time_ms_d|Gesamtwartezeit im Zeitraum |
|delta_waiting_tasks_count_d|Anzahl von wartenden Aufgaben |

Weitere Informationen zu [Datenbankwartestatistiken](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Dataset zu Timeouts

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure |
|TimeGenerated [UTC]|Zeitstempel für die Aufzeichnung des Protokolls |
|Typ|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category (Kategorie)|Name der Kategorie Immer: Zeitlimits |
|NameVorgang|Name des Vorgangs. Immer: TimeoutEvent |
|Ressource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: SERVERS/DATABASES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name des Servers für die Datenbank |
|ElasticPoolName_s|Name des Pools für elastische Datenbanken für die Datenbank (sofern vorhanden) |
|DatabaseName_s|Name der Datenbank |
|ResourceId|Ressourcen-URI |
|error_state_d|Fehlerstatuscode |
|query_hash_s|Abfragehash (falls verfügbar) |
|query_plan_hash_s|Hash des Abfrageplans (falls verfügbar) |

### <a name="blockings-dataset"></a>Dataset der Blockierungen

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure |
|TimeGenerated [UTC]|Zeitstempel für die Aufzeichnung des Protokolls |
|Typ|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category (Kategorie)|Name der Kategorie Immer: Blöcke |
|NameVorgang|Name des Vorgangs. Immer: BlockEvent |
|Ressource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: SERVERS/DATABASES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name des Servers für die Datenbank |
|ElasticPoolName_s|Name des Pools für elastische Datenbanken für die Datenbank (sofern vorhanden) |
|DatabaseName_s|Name der Datenbank |
|ResourceId|Ressourcen-URI |
|lock_mode_s|Von der Abfrage verwendeter Sperrmodus |
|resource_owner_type_s|Besitzer der Sperre |
|blocked_process_filtered_s|XML-Bericht über blockierte Prozesse |
|duration_d|Die Dauer der Sperre in Millisekunden |

### <a name="deadlocks-dataset"></a>Dataset zu Deadlocks

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure |
|TimeGenerated [UTC] |Zeitstempel für die Aufzeichnung des Protokolls |
|Typ|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category (Kategorie)|Name der Kategorie Immer: Deadlocks |
|NameVorgang|Name des Vorgangs. Immer: DeadlockEvent |
|Ressource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: SERVERS/DATABASES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name des Servers für die Datenbank |
|ElasticPoolName_s|Name des Pools für elastische Datenbanken für die Datenbank (sofern vorhanden) |
|DatabaseName_s|Name der Datenbank |
|ResourceId|Ressourcen-URI |
|deadlock_xml_s|XML-Deadlockbericht |

### <a name="automatic-tuning-dataset"></a>Dataset zur automatischen Optimierung

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure |
|TimeGenerated [UTC]|Zeitstempel für die Aufzeichnung des Protokolls |
|Typ|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category (Kategorie)|Name der Kategorie Immer: AutomaticTuning |
|Ressource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: SERVERS/DATABASES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name des Servers für die Datenbank |
|LogicalDatabaseName_s|Name der Datenbank |
|ElasticPoolName_s|Name des Pools für elastische Datenbanken für die Datenbank (sofern vorhanden) |
|DatabaseName_s|Name der Datenbank |
|ResourceId|Ressourcen-URI |
|RecommendationHash_s|Eindeutiger Hash der Empfehlung zur automatischen Optimierung |
|OptionName_s|Vorgang der automatischen Optimierung |
|Schema_s|Datenbankschema |
|Table_s|Betroffene Tabelle |
|IndexName_s|Indexname |
|IndexColumns_s|Spaltenname |
|IncludedColumns_s|Enthaltene Spalten |
|EstimatedImpact_s|Geschätzte Auswirkung der JSON der Empfehlung zur automatischen Optimierung |
|Event_s|Typ des Ereignisses der automatischen Optimierung |
|Timestamp_t|Zeitstempel der letzten Aktualisierung |

### <a name="intelligent-insights-dataset"></a>Dataset von Intelligent Insights

Weitere Informationen zum [Protokollformat von Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Aktivieren der Protokollierung und zu den Metriken und Protokollkategorien, die von den verschiedenen Azure-Diensten unterstützt werden, finden Sie in den folgenden Themen:

- [Überblick über Metriken in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Übersicht über Azure-Diagnose](../azure-monitor/platform/diagnostic-logs-overview.md)

Weitere Informationen zu Event Hubs finden Sie hier:

- [Was ist Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Erste Schritte mit Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Weitere Informationen zu Azure Storage finden Sie unter [Herunterladen von Metriken und Diagnoseprotokollen aus Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
