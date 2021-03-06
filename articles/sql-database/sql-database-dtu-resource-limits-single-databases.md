---
title: Azure SQL-Datenbank – auf DTU basierende Ressourcenlimits für Einzeldatenbanken | Microsoft-Dokumentation
description: Diese Seite beschreibt einige allgemeine auf DTU basierende Ressourcenlimits für Einzeldatenbanken in Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 10/23/2018
ms.openlocfilehash: 5ddcacbf805ae9d2ce093058b21d53189457ffb9
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233768"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Ressourcenlimits für Einzeldatenbanken, die das Kaufmodell auf DTU-Basis verwenden

Dieser Artikel enthält ausführliche Angaben zu Ressourcenlimits für Einzeldatenbanken in Azure SQL-Datenbank, die das DTU-basierte Kaufmodell verwenden.

Informationen zu Ressourcenlimits für Pools für elastische Datenbanken beim Kaufmodell auf DTU-Basis finden Sie unter [Einschränkungen beim auf virtuellen Kernen basierenden Kaufmodell für Azure SQL-Datenbank für Pools für elastische Datenbanken (Vorschau)](sql-database-vcore-resource-limits-elastic-pools.md). Informationen zu Einschränkungen beim auf virtuellen Kernen basierenden Kaufmodell finden Sie unter [Einschränkungen beim auf virtuellen Kernen basierenden Kaufmodell für Azure SQL-Datenbank (Vorschau) für eine einzelne Datenbank](sql-database-vcore-resource-limits-single-databases.md) und [Einschränkungen beim auf virtuellen Kernen basierenden Kaufmodell für Azure SQL-Datenbank (Vorschau) für elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md). Weitere Informationen zu den verschiedenen Kaufmodellen finden Sie unter [Kaufmodelle und Diensttarife](sql-database-service-tiers.md).

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](sql-database-file-space-management.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Einzeldatenbank: Speicher- und Computegrößen

Die folgende Tabelle enthält die verfügbaren Ressourcen für Einzeldatenbanken für alle Diensttarife und Computegrößen. Sie können mit [Azure-Portal](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-logical-servers-and-databases), [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-logical-servers-and-databases) oder [REST-API](sql-database-single-databases-manage.md#rest-api-manage-logical-servers-and-databases) Diensttarif, Computegröße und Speichermenge für eine einzelne Datenbank festlegen.

### <a name="basic-service-tier"></a>Basic-Dienstebene

| **Computegröße** | **Basic** |
| :--- | --: |
| Max. DTU-Anzahl | 5 |
| Inbegriffener Speicher (GB) | 2 |
| Max. Speicherlösungen (GB) | 2 |
| Max. In-Memory-OLTP-Speicher (GB) |N/V |
| Max. gleichzeitige Worker (Anforderungen) | 30 |
| Max. gleichzeitige Sitzungen | 300 |
|||

### <a name="standard-service-tier"></a>Standard-Dienstebene

| **Computegröße** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Max. DTU-Anzahl | 10 | 20 | 50 | 100 |
| Inbegriffener Speicher (GB) | 250 | 250 | 250 | 250 |
| Max. Speicherlösungen (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Max. In-Memory-OLTP-Speicher (GB) | N/V | N/V | N/V | N/V |
| Max. gleichzeitige Worker (Anforderungen)| 60 | 90 | 120 | 200 |
| Max. gleichzeitige Sitzungen |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Standard-Dienstebene (Fortsetzung)

| **Computegröße** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Max. DTU-Anzahl | 200 | 400 | 800 | 1600 | 3000 |
| Inbegriffener Speicher (GB) | 250 | 250 | 250 | 250 | 250 |
| Max. Speicherlösungen (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Max. In-Memory-OLTP-Speicher (GB) | N/V | N/V | N/V | N/V |N/V |
| Max. gleichzeitige Worker (Anforderungen)| 400 | 800 | 1600 | 3200 |6000 |
| Max. gleichzeitige Sitzungen |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Premium-Dienstebene

| **Computegröße** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Max. DTU-Anzahl | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Inbegriffener Speicher (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Max. Speicherlösungen (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Max. In-Memory-OLTP-Speicher (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Max. gleichzeitige Worker (Anforderungen)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Max. gleichzeitige Sitzungen | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

> [!IMPORTANT]
> Mehr als 1 TB Speicher im Premium-Tarif ist derzeit in allen Regionen verfügbar, mit Ausnahme von: „China, Osten“, „China, Norden“, „Deutschland, Mitte“, „Deutschland, Nordosten“, „Vereinigtes Königreich, Norden“, „Vereinigtes Königreich, Süden“, „US DoD, Mitte“, „US DoD, Osten“, „US Gov, Mitte“, „USA, Westen-Mitte“.  In diesen Regionen ist der Speicher im Tarif „Premium“ auf 1 TB begrenzt. Siehe [Aktuelle Einschränkungen für P11–P15](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="single-database-change-storage-size"></a>Einzeldatenbank: Ändern der Speichergröße

- Der DTU-Preis für eine einzelne Datenbank enthält eine bestimmte Menge Speicher ohne zusätzliche Kosten. Zusätzlicher Speicher über die inbegriffene Speichermenge hinaus kann gegen zusätzliche Gebühren bis zur Obergrenze in Inkrementen von 250 GB bis zu 1 TB und dann in Inkrementen von 256 GB über 1 TB hinaus bereitgestellt werden. Informationen zu enthaltenen Speichermengen und Maximalgrößen finden Sie unter [Einzeldatenbank: Speicher- und Computegrößen](#single-database-storage-sizes-and-compute-sizes).
- Zusätzlicher Speicher für eine einzelne Datenbank kann durch Erhöhen der maximalen Größe mithilfe von [Azure-Portal](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update) oder [REST-API](https://docs.microsoft.com/rest/api/sql/databases/update) bereitgestellt werden.
- Der Preis für zusätzlichen Speicher für eine einzelne Datenbank errechnet sich aus der Menge an zusätzlich bereitgestelltem Speicher multipliziert mit dem Einheitenpreis für zusätzlichen Speicher für die Dienstebene. Ausführliche Informationen zu den Preisen für zusätzlichen Speicherplatz siehe [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Einzeldatenbank: Ändern von DTUs

Nach der ersten Auswahl des Diensttarifs, der Computegröße und Speichermenge können Sie eine Einzeldatenbank anhand der tatsächlichen Erfahrung über das [Azure-Portal](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), die [Azure CLI](/cli/azure/sql/db#az-sql-db-update) oder die [REST-API](https://docs.microsoft.com/rest/api/sql/databases/update) dynamisch hoch- oder herunterskalieren.

Das folgende Video zeigt die dynamische Änderung von Diensttarif und Computegröße zum Heraufsetzen verfügbarer DTUs für eine einzelne Datenbank.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Wenn Sie den Diensttarif und/oder die Computegröße einer Datenbank ändern, wird ein Replikat der ursprünglichen Datenbank mit der neuen Computegröße erstellt, und anschließend werden die Verbindungen auf dieses Replikat umgestellt. Während dieses Vorgangs gehen keine Daten verloren. Allerdings sind die Verbindungen zur Datenbank inaktiv, während kurz auf das Replikat umgestellt wird. Daher werden möglicherweise einige aktive Transaktionen zurückgesetzt. Die Dauer der Umstellung kann variieren, aber sie liegt in 99 Prozent der Fälle unter 30 Sekunden. Falls im Moment der Verbindungstrennung viele Transaktionen stattfinden, kann die Umstellung unter Umständen auch länger dauern.

Die Dauer des gesamten zentralen Hochskalierungsvorgangs hängt sowohl von der Größe als auch vom Diensttarif der Datenbank vor und nach der Änderung ab. Beispielsweise sollte eine 250-GB-Datenbank beim Wechsel in einen, aus einem oder innerhalb eines Standarddiensttarifs innerhalb von sechs Stunden zentral hochskalieren. Bei einer Datenbank der gleichen Größe, deren Computegrößen innerhalb des Diensttarifs „Premium“ geändert werden, sollte das zentrale Hochskalieren innerhalb von drei Stunden erfolgen.

> [!TIP]
> Weitere Informationen zum Überwachen laufender Vorgänge finden Sie unter [Verwalten von Vorgängen mithilfe der SQL-REST-API](https://docs.microsoft.com/rest/api/sql/databaseoperations/listbydatabase
), [Verwalten von Vorgängen mithilfe der Befehlszeilenschnittstelle](/cli/azure/sql/db/op) und [Überwachen von Vorgängen mit T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). Verwenden Sie auch diese beiden PowerShell-Befehle: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) und [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

- Wenn Sie ein Upgrade auf einen höheren Diensttarif oder eine höhere Computegröße durchführen, wird die maximale Datenbankgröße nicht erhöht, sofern Sie nicht ausdrücklich eine höhere Maximalgröße angeben.
- Für das Downgrade einer Datenbank muss die verwendete Datenbankmenge kleiner als die maximal zulässige Größe des Zieldiensttarifs und der Zielcomputegröße sein.
- Bei einem Downgrade vom Diensttarif **Premium** zu **Standard** fallen zusätzliche Speicherkosten an, wenn (1) die maximale Größe der Datenbank von der Zielcomputegröße unterstützt wird und (2) die maximale Größe in der Zielcomputegröße die enthaltene Speichermenge überschreitet. Wenn z.B. eine P1-Datenbank mit einer maximalen Größe von 500 GB auf S3 reduziert wird, fallen zusätzliche Speicherkosten an, da S3 eine maximale Größe von 500 GB unterstützt und die enthaltene Speichermenge nur 250 GB beträgt. Daher beträgt die zusätzliche Speichermenge 500 GB – 250 GB = 250 GB. Ausführliche Informationen zu den Preisen für zusätzlichen Speicherplatz siehe [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/). Wenn die tatsächlich verwendete Speichermenge kleiner als die enthaltene Speichermenge ist, können Sie diese zusätzlichen Kosten vermeiden, indem Sie die maximale Datenbankgröße auf die enthaltene Menge reduzieren.
- Beim Upgrade einer Datenbank mit aktivierter [Georeplikation](sql-database-geo-replication-portal.md) führen Sie zuerst ein Upgrade der sekundären Datenbanken auf den gewünschten Diensttarif und die gewünschte Computegröße durch, bevor Sie ein Upgrade für die primäre Datenbank vornehmen (allgemeine Richtlinie für optimale Leistung). Bei einem Upgrade auf eine andere Edition muss zunächst ein Upgrade der sekundären Datenbank durchgeführt werden.
- Beim Downgrade einer Datenbank mit aktivierter [Georeplikation](sql-database-geo-replication-portal.md) führen Sie zuerst ein Downgrade der primären Datenbank auf den gewünschten Diensttarif und die gewünschte Computegröße durch, bevor Sie ein Downgrade für die sekundären Datenbanken vornehmen (allgemeine Richtlinie für optimale Leistung). Bei einem Downgrade auf eine andere Edition muss zunächst ein Downgrade der primären Datenbank durchgeführt werden.
- Die Angebote des Wiederherstellungsdienstes variieren für die verschiedenen Dienstebenen. Wenn Sie ein Downgrade auf den Tarif **Basic** durchführen, verfügen Sie über einen kürzeren Aufbewahrungszeitraum von Sicherungen. Weitere Informationen finden Sie unter [Azure SQL-Datenbanksicherungen](sql-database-automated-backups.md).
- Die neuen Eigenschaften für die Datenbank werden erst angewendet, wenn die Änderungen abgeschlossen sind.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Einzeldatenbank: Einschränkungen von P11 und P15 bei Maximalgrößen über 1 TB

Die folgenden Aspekte und Einschränkungen gelten für P11- und P15-Datenbanken mit einer maximalen Größe von mehr als 1 TB:

- Wenn Sie beim Erstellen einer Datenbank eine Maximalgröße von mehr als 1 TB wählen (Wert 4 TB oder 4.096 GB), tritt für den Erstellungsbefehl ein Fehler auf, falls die Datenbank in einer nicht unterstützten Region bereitgestellt wird.
- Für vorhandene P11- und P15-Datenbanken, die sich in einer der unterstützten Regionen befinden, können Sie den maximalen Speicher über 1 TB hinaus in Inkrementen von 256 GB auf bis zu 4 TB erhöhen. Um festzustellen, ob eine größere Größe in Ihrer Region unterstützt wird, verwenden Sie die Funktion [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql), oder überprüfen Sie die Größe der Datenbank im Azure-Portal. Die Aktualisierung einer vorhandenen P11- oder P15-Datenbank kann nur mithilfe einer Prinzipalanmeldung auf Serverebene oder von Mitgliedern der Datenbankrolle „dbmanager“ ausgeführt werden.
- Bei Ausführung eines Upgradevorgangs in einer unterstützten Region wird die Konfiguration umgehend aktualisiert. Die Datenbank bleibt während des Upgradevorgangs online. Allerdings können Sie die vollständige Menge an Speicher über 1 TB erst dann nutzen, wenn die tatsächlichen Datenbankdateien auf die neue Maximalgröße aktualisiert wurden. Die erforderliche Dauer hängt von der Größe der zu aktualisierenden Datenbank ab.
- Beim Erstellen oder Aktualisieren einer P11- oder P15-Datenbank können Sie nur in Inkrementen von 256 GB zwischen einer Maximalgröße von 1 TB und 4 TB wählen. Beim Erstellen einer P11-/P15-Datenbank ist die Standardspeicheroption von 1 TB vorausgewählt. Für Datenbanken, die sich in einer der unterstützten Regionen befinden, können Sie das Speichermaximum für eine neue oder vorhandene Einzeldatenbank auf maximal 4 TB erhöhen. Für alle anderen Regionen kann die Maximalgröße nicht mehr als 1 TB betragen. Der Preis ändert sich nicht, wenn Sie 4TB Speicher wählen.
- Wenn die maximale Größe einer Datenbank auf mehr als 1 TB festgelegt ist, kann sie auch dann nicht in 1 TB geändert werden, wenn die tatsächlich verwendete Speichermenge unter 1 TB liegt. Folglich können Sie eine P11- oder P15-Datenbank mit einer maximalen Größe über 1 TB nicht auf eine 1-TB-P11- oder 1-TB-P15- oder niedrigere Computegröße (z.B. P1-P6) herabstufen. Diese Einschränkung gilt auch für Wiederherstellungs- und Kopierszenarien, einschließlich Point-in-Time- und Geowiederherstellung, langfristige Aufbewahrung von Sicherungen und Datenbankkopiervorgänge. Sobald eine Datenbank mit einer maximalen Größe über 1 TB konfiguriert wird, müssen alle Wiederherstellungsvorgänge dieser Datenbank für ein P11-/P15-Ziel mit einer maximalen Größe von über 1 TB ausgeführt werden.
- Szenarien für aktive Georeplikation:
  - Einrichten einer Georeplikationsbeziehung: Falls es sich bei der primären Datenbank um eine P11- oder P15-Datenbank handelt, müssen auch die sekundären Datenbanken vom Typ „P11“ oder „P15“ sein. Niedrigere Computegrößen werden für sekundäre Datenbanken abgelehnt, da sie nicht mehr als 1 TB unterstützen können.
  - Aktualisieren der primären Datenbank in einer Georeplikationsbeziehung: Die Änderung der maximalen Größe für eine primäre Datenbank in mehr als 1 TB löst die gleiche Änderung für die sekundäre Datenbank aus. Beide Upgrades müssen erfolgreich ausgeführt werden, damit die Änderung für die primäre Datenbank wirksam wird. Für die Option mit mehr als 1 TB gelten Regionseinschränkungen. Wenn sich die sekundäre Datenbank in einer Region befindet, die nicht mehr als 1 TB unterstützt, wird die primäre Datenbank nicht aktualisiert.
- Die Verwendung des Import/Export-Diensts zum Laden von Datenbanken des Typs „P11-P15“ mit mehr als 1 TB wird nicht unterstützt. Verwenden Sie „SqlPackage.exe“, um Daten zu [importieren](sql-database-import.md) und [exportieren](sql-database-export.md).

## <a name="next-steps"></a>Nächste Schritte

- Antworten auf häufig gestellte Fragen finden Sie unter [SQL-Datenbank – Häufig gestellte Fragen](sql-database-faq.md).
- Informationen zu Grenzwerten auf Server- und Abonnementebene finden Sie unter [Übersicht über Ressourcenlimits auf einem logischen Server](sql-database-resource-limits-logical-server.md).
- Informationen zu allgemeinen Azure-Einschränkungen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).
- Informationen zu DTUs und eDTUs finden Sie unter [Datenbanktransaktionseinheiten (DTUs) und elastische Datenbanktransaktionseinheiten (eDTUs)](sql-database-service-tiers.md#dtu-based-purchasing-model).
- Weitere Informationen zu Größenbeschränkungen für tempdb finden Sie unter [Einschränkungen für tempdb-Datenbanken in SQL-Datenbank](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
