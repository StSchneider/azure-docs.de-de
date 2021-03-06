---
title: Behandeln von Aktivitätsprotokollwarnungen in Azure Monitor
description: Allgemeine Probleme, Fehler und deren Behandlung für Protokollwarnungsregeln in Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: cffc3ac8808992f7884839329e5bf152d318820c
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789363"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Behandeln von Aktivitätsprotokollwarnungen in Azure Monitor  

## <a name="overview"></a>Übersicht

Dieser Artikel zeigt die Lösung häufiger Probleme beim Einrichten von Protokollwarnungen in Azure Monitor. Er enthält außerdem Lösungen zu häufig gestellten Fragen bezüglich der Funktionalität oder der Konfiguration von Protokollwarnungen. 

Der Begriff **Protokollwarnungen** beschreibt Warnungen, die basierend auf einer benutzerdefinierten Abfrage in [Log Analytics](../learn/tutorial-viewdata.md) oder [Application Insights](../../azure-monitor/app/analytics.md) ausgelöst werden. Erfahren Sie mehr über Funktionen, Terminologie und Typen unter [Protokollwarnungen – Übersicht](../platform/alerts-unified-log.md).

> [!NOTE]
> In diesem Artikel werden keine Fälle berücksichtigt, in denen das Azure-Portal eine ausgelöste Warnungsregel und eine durchgeführte Benachrichtigung über zugeordnete Aktionsgruppen anzeigt. Informationen zu solchen Situationen finden Sie im Artikel zu [Aktionsgruppen](../platform/action-groups.md).


## <a name="log-alert-didnt-fire"></a>Protokollwarnung wurde nicht ausgelöst.

Im Folgenden finden Sie einige häufige Gründe, warum ein konfigurierter Status einer [Protokollwarnungsregel in Azure Monitor](../platform/alerts-log.md) nicht wie erwartet [als *Ausgelöst* angezeigt wird](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Datenerfassungszeit für Protokolle

Die Protokollwarnung führt Ihre Abfrage in regelmäßigen Abständen basierend auf [Log Analytics](../learn/tutorial-viewdata.md) oder [Application Insights](../../azure-monitor/app/analytics.md) aus. Da Log Analytics viele Terabyte an Daten von Tausenden Kunden und aus verschiedenen Quellen auf der ganzen Welt verarbeitet, ist der Dienst anfällig für unterschiedliche zeitliche Verzögerungen. Weitere Informationen finden Sie unter [Datenerfassungszeit in Log Analytics](../platform/data-ingestion-time.md).

Um Verzögerungen bei der Datenerfassung zu verringern, wartet das System und wiederholt die Warnungsabfrage mehrere Male, falls die benötigten Daten noch nicht erfasst wurden. Für das System ist eine exponentiell zunehmende Wartezeit festgelegt. Die Protokollwarnung wird erst ausgelöst, sobald die Daten verfügbar sind. Ursache für die Verzögerung kann also eine langsame Protokolldatenerfassung sein. 

### <a name="incorrect-time-period-configured"></a>Falscher Zeitraum konfiguriert

Wie im Artikel zur [Terminologie für Protokollwarnungen](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types) beschrieben, steht der in der Konfiguration angegebene Zeitraum für den Zeitbereich der Abfrage. Die Abfrage gibt nur Datensätze zurück, die innerhalb dieses Zeitbereichs erstellt wurden. Mit dem Zeitraum werden die Daten eingeschränkt, die für eine Protokollabfrage abgerufen werden, um Missbrauch zu verhindern, und alle in einer Protokollabfrage verwendeten Zeitbefehle (z.B. *ago*) werden umgangen. Wenn der Zeitraum beispielsweise auf 60 Minuten festgelegt ist und die Abfrage um 13:15 Uhr ausgeführt wird, werden nur Datensätze, die zwischen 12:15 und 13:15 Uhr erstellt wurden, für die Protokollabfrage verwendet. Wenn die Protokollabfrage einen Zeitbefehl wie *ago (1d)* verwendet, nutzt die Abfrage nach wie vor nur Daten zwischen 12:15 und 13:15 Uhr, da der Zeitraum auf dieses Intervall festgelegt ist.*

Überprüfen Sie also, ob dieser Zeitraum in der Konfiguration mir Ihrer Abfrage übereinstimmt. Wird für die Protokollabfrage aus dem bereits erwähnten Beispiel *ago (1d)* verwendet (mit grünem Marker dargestellt), sollte der Zeitraum auf 24 Stunden oder 1440 Minuten (in Rot dargestellt) festgelegt werden, um sicherzustellen, dass die Abfrage wie geplant ausgeführt wird.

![Zeitraum](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Option „Warnungen unterdrücken“ ist festgelegt.

Wie in Schritt 8 des Artikels über das [Erstellen einer Protokollwarnungsregel im Azure-Portal](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) beschrieben, bieten Protokollwarnungen die Option **Warnungen unterdrücken**, um Auslöse- und Benachrichtigungsaktionen für einen konfigurierten Zeitraum zu unterdrücken. Daher könnten Sie annehmen, dass eine Warnung nicht ausgelöst wurde, obwohl dies eigentlich geschehen ist, sie aber unterdrückt wurde.  

![Warnungen unterdrücken](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Warnungsregel für metrische Maßeinheit ist falsch.

**Protokollwarnungen vom Typ „Metrische Maßeinheit“** sind ein Untertyp von Protokollwarnungen, die spezielle Funktionen und eine eingeschränkte Warnungsabfragesyntax aufweisen. Eine Protokollwarnungsregel vom Typ „Metrische Maßeinheit“ erfordert, dass die Ausgabe der Abfrage eine metrische Zeitreihe ist: eine Tabelle mit eindeutigen gleich langen Zeiträumen zusammen mit entsprechenden aggregierten Werten. Darüber hinaus können Benutzer in die Tabelle neben AggregatedValue zusätzliche Variablen aufnehmen. Diese Variablen können zum Sortieren der Tabelle verwendet werden. 

Beispiel: Eine Protokollwarnungsregel vom Typ „Metrische Maßeinheit“ wurde folgendermaßen konfiguriert:

- Abfrage: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Zeitraum: 6 Stunden
- Schwellenwert: 50
- Warnungslogik: drei aufeinanderfolgende Sicherheitsverletzungen
- „Aggregate Upon“: $table

Da der Befehl *summarize … by* umfasst und zwei Variablen (timestamp und $table) angibt, wählt das System $table für *Aggregate Upon* aus. Die Ergebnistabelle wird nach dem Feld *$table* sortiert, wie unten dargestellt. Dann wird AggregatedValue mehrmals für jeden Tabellentyp (z.B. availabilityResults) überprüft, um festzustellen, ob es mindestens drei aufeinanderfolgende Sicherheitsverletzungen gab.

![Abfrageausführung für „Metrische Maßeinheit“ mit mehreren Werten](media/alert-log-troubleshoot/LogMMQuery.png)

Da *Aggregate Upon* den Wert *$table* hat, werden die Daten nach der Spalte „$table“ sortiert (in Rot dargestellt), dann werden sie gruppiert und auf Typen des Felds *Aggregate Upon* untersucht, die „$table“ entsprechen. Beispiel: Werte für availabilityResults werden als ein Plot/eine Einheit betrachtet (in Orange dargestellt). In diesem Plot bzw. dieser Entität des Werts sucht der Warnungsdienst nach drei aufeinanderfolgenden Sicherheitsverletzungen (in Grün dargestellt), bei denen eine Warnung für den Tabellenwert availabilityResults ausgelöst wird. Wenn bei einem anderen Wert von „$table“ drei aufeinanderfolgende Sicherheitsverletzungen erkannt werden, wird analog dazu eine weitere Warnungsbenachrichtigungen ausgelöst, wobei der Warnungsdienst automatisch die Werte in einem Plot bzw. einer Entität nach Zeit sortiert (in Orange dargestellt).

Angenommen, eine Protokollwarnungsregel vom Typ „Metrische Maßeinheit“ wurde geändert, und die Abfrage lautete `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`, wobei die restliche Konfiguration unverändert bliebt, einschließlich der Warnungslogik für drei aufeinanderfolgende Sicherheitsverletzungen. Die Option „Aggregate Upon“ weist in diesem Fall den Standardwert „timestamp“ auf. Da in der Abfrage nur ein Wert für *summarize … by* mit dem Wert *timestamp* vorhanden ist, entspricht die Ausgabe am Ende der Ausführung ähnlich wie im Beispiel zuvor der nachstehenden Darstellung.

   ![Abfrageausführung für „Metrische Maßeinheit“ mit einzelnem Wert](media/alert-log-troubleshoot/LogMMtimestamp.png)

Da *Aggregate Upon* nach „timestamp“ definiert ist, werden die Daten nach der Spalte *timestamp* sortiert (in Rot dargestellt). Anschließend wird nach „timestamp“ gruppiert. Beispiel: Werte für `2018-10-17T06:00:00Z` werden als ein Plot/eine Entität betrachtet (in Orange dargestellt). In diesem Plot bzw. dieser Entität des Werts findet der Warnungsdienst keine drei aufeinanderfolgenden Sicherheitsverletzungen (da für jeden timestamp-Wert nur ein Eintrag vorhanden ist), daher wird nie eine Warnung ausgelöst. In einem solchen Fall muss der Benutzer eine der folgenden Aktionen ausführen:

- Hinzufügen einer Dummyvariable oder einer vorhandenen Variable (z.B. $table) für eine richtige Sortierung mit konfiguriertem Feld „Aggregate Upon“
- (Oder:) Neukonfigurieren der Warnungsregel für die Verwendung von Warnungslogik basierend auf *Sicherheitsverletzungen insgesamt*

## <a name="log-alert-fired-unnecessarily"></a>Protokollwarnung wird unnötigerweise ausgelöst.

Im Folgenden finden Sie einige häufige Gründe, warum eine konfigurierte [Protokollwarnungsregel in Azure Monitor](../platform/alerts-log.md) bei der Anzeige in [Azure-Warnungen](../platform/alerts-managing-alert-states.md) ausgelöst werden kann, obwohl Sie keine Auslösung erwarten.

### <a name="alert-triggered-by-partial-data"></a>Warnung wird durch unvollständige Daten ausgelöst.

Da Analytics (und damit Log Analytics) und Application Insights Verzögerungen bei der Erfassung und Verarbeitung unterliegen, kann es vorkommen, dass zum Ausführungszeitpunkt der angegebenen Protokollwarnungsabfrage keine Daten oder nur ein Teil der Daten verfügbar sind. Weitere Informationen finden Sie unter [Datenerfassungszeit in Log Analytics](../platform/data-ingestion-time.md).

Abhängig von der Konfiguration der Warnungsregel kann es zu einer fehlerhaften Auslösung kommen, wenn zum Zeitpunkt der Warnungsausführung keine Daten oder nur ein Teil der Daten vorhanden sind. In solchen Fällen ist es empfehlenswert, die Warnungsabfrage oder -konfiguration zu ändern. 

Beispiel: Wenn die Protokollwarnungsregel so konfiguriert ist, dass sie während einer Analyseabfrage bei weniger als fünf Ergebnissen ausgelöst wird, dann wird die Warnung bereits bei keinen Daten (null Datensätze) oder Teilergebnissen (ein Datensatz) ausgelöst. Nachdem alle Daten erfasst wurden, kann dieselbe Abfrage mit vollständigen Daten jedoch zu einem Ergebnis von zehn Datensätzen führen.

### <a name="alert-query-output-misunderstood"></a>Die Ausgabe der Warnungsabfrage wurde falsch verstanden.

Die Logik für Protokollwarnungen in einer Analyseabfrage stellen Sie bereit. Die Analyseabfrage kann verschiedene Big-Data-Funktionen und mathematische Funktionen verwenden.  Der Warnungsdienst führt die Abfrage in Intervallen aus, die mit Daten für eine bestimmte Zeitspanne angegeben wurden. Er nimmt basierend auf dem Warnungstyp geringfügige Änderungen an der bereitgestellten Abfrage vor. Dies wird im Bildschirm *Signallogik konfigurieren* im Abschnitt „Query to be executed“ (Auszuführende Abfrage) dargestellt (s. unten): ![Auszuführende Abfrage](media/alert-log-troubleshoot/LogAlertPreview.png)

Im Feld **Auszuführende Abfrage** wird das dargestellt, was der Warnungsprotokolldienst ausführt. Sie können die angegebene Abfrage und die Zeitspanne über das [Analytics-Portal](../log-query/portals.md) oder die [Textanalyse-API](https://docs.microsoft.com/rest/api/loganalytics/) ausführen, wenn Sie vor der Erstellung der eigentlichen Warnung erfahren möchten, wie die Ausgabe der Warnungsabfrage aussehen könnte.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Protokollwarnungen in Azure-Warnungen](../platform/alerts-unified-log.md).
- Weitere Informationen zu [Application Insights](../../azure-monitor/app/analytics.md)
- Erfahren Sie mehr über [Log Analytics](../../log-analytics/log-analytics-overview.md).