---
title: 'Analytics: Das leistungsfähige Such- und Abfragetool von Azure Application Insights | Microsoft-Dokumentation'
description: 'Übersicht über Analytics, das leistungsfähige Diagnosesuchtool von Application Insights. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: 24a911978d6c9f9a33c81254a5e2f5951029ff3a
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120885"
---
# <a name="analytics-in-application-insights"></a>Analytics in Application Insights
Analytics ist das leistungsfähige Such- und Abfragetool von [Application Insights](../../azure-monitor/app/app-insights-overview.md). Analytics ist ein Webtool, sodass keine Installation erforderlich ist. Wenn Sie Application Insights bereits für eine Ihrer Apps konfiguriert haben, können Sie die Daten Ihrer App analysieren, indem Sie Analytics vom [Übersichtsblatt](../../azure-monitor/app/app-insights-dashboards.md) Ihrer App aus öffnen.

![Öffnen Sie unter „portal.azure.com“ die Application Insights-Ressource, und wählen Sie „Analytics“.](./media/analytics/001.png)

Sie können auch den [Analytics-Playground](https://go.microsoft.com/fwlink/?linkid=859557) verwenden, eine kostenlose Demo-Umgebung mit einer Vielzahl von Beispieldaten.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="query-data-in-analytics"></a>Abfragen von Daten in Analytics
Eine typische Abfrage beginnt mit einem Tabellennamen, gefolgt von einer Reihe von *Operatoren*, die durch `|` getrennt sind.
Wir möchten z.B. herausfinden, wie viele Anforderungen unsere App während der letzten 3 Stunden aus verschiedenen Ländern empfangen hat:
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

Wir beginnen mit dem Tabellennamen *requests* und fügen nach Bedarf weitergeleitete Elemente hinzu.  Zuerst definieren wir einen Zeitfilter, um nur Datensätze aus den letzten 3 Stunden zu überprüfen.
Wir zählen dann die Anzahl der Datensätze pro Land (diese Daten befinden sich in der Spalte *client_CountryOrRegion*). Schließlich werden die Ergebnisse in einem Kreisdiagramm ausgegeben.
<br>

![Abfrageergebnisse](./media/analytics/030.png)

Die Sprache verfügt über viele attraktive Features:

* [Filtern](/azure/kusto/query/whereoperator) der Rohdaten Ihrer App-Telemetrie nach beliebigen Feldern, einschließlich Ihrer benutzerdefinierten Eigenschaften und Metriken.
* [Verbinden](/azure/kusto/query/joinoperator) mehrerer Tabellen – Korrelation von Anforderungen mit Seitenansichten, Aufrufen von Abhängigkeiten, Ausnahmen und Protokollablaufverfolgungen.
* Leistungsstarke statistische [Aggregationen](/azure/kusto/query/summarizeoperator).
* Sofortige und leistungsfähige Visualisierungen.
* Eine [REST-API](https://dev.applicationinsights.io/), die Sie verwenden können, um Abfragen programmgesteuert auszuführen, z.B. über PowerShell.

Die [vollständige Sprachreferenz](https://go.microsoft.com/fwlink/?linkid=856079) erläutert jeden unterstützten Befehl und wird regelmäßig aktualisiert.

## <a name="next-steps"></a>Nächste Schritte
* Erste Schritte mit dem [Analytics-Portal](https://go.microsoft.com/fwlink/?linkid=856587)
* Erste Schritte mit dem [Schreiben von Abfragen](https://go.microsoft.com/fwlink/?linkid=856078)
* Überprüfen Sie, ob der [Spickzettel für SQL-Benutzer](https://aka.ms/sql-analytics) Übersetzungen der am häufigsten verwendeten Begriffe enthält.
* Testen Sie Analytics auf unserem [Playground](https://analytics.applicationinsights.io/demo), wenn Ihre App noch keine Daten an Application Insights sendet.
* Sehen Sie sich das [Einführungsvideo](https://applicationanalytics-media.azureedge.net/home_page_video.mp4) an.
