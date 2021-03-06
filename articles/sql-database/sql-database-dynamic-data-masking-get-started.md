---
title: Dynamische Datenmaskierung für Azure SQL-Datenbank | Microsoft Docs
description: Die dynamische Datenmaskierung für SQL-Datenbank schränkt die Offenlegung vertraulicher Daten ein, indem sie für nicht berechtigte Benutzer maskiert werden.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 12/16/2018
ms.openlocfilehash: 3e807033b109b8281057f6881a315f5c1c783a22
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536371"
---
# <a name="sql-database-dynamic-data-masking"></a>Dynamische Datenmaskierung für SQL-Datenbank

Die dynamische Datenmaskierung für SQL-Datenbank schränkt die Offenlegung vertraulicher Daten ein, indem sie für nicht berechtigte Benutzer maskiert werden. 

Die dynamische Datenmaskierung hilft beim Verhindern des unbefugten Zugriffs auf sensible Daten, indem Kunden festlegen dürfen, welcher Anteil der sensiblen Daten mit minimalen Auswirkungen auf die Anwendungsschicht offengelegt wird. Es handelt sich um eine richtlinienbasierte Sicherheitsfunktion, die die sensiblen Daten im Resultset einer Abfrage in festgelegten Datenbankfeldern ausblendet, ohne dass die Daten in der Datenbank geändert werden.

Ein Servicemitarbeiter in einem Callcenter kann Anrufer beispielsweise anhand mehrerer Ziffern ihrer Kreditkartennummer identifizieren, wobei diese Datenelemente dem Servicemitarbeiter jedoch nicht vollständig angezeigt werden sollen. Es kann eine Maskierungsregel definiert werden, mit der die Kreditkartennummer im Resultset einer Abfrage bis auf die letzten vier Ziffern ausgeblendet wird. In einem weiteren Beispiel kann eine entsprechende Datenmaske zum Schutz personenbezogener Daten definiert werden, damit ein Entwickler Produktionsumgebungen zu Problembehandlungszwecken abfragen kann, ohne gegen Vorschriften zu verstoßen.

## <a name="sql-database-dynamic-data-masking-basics"></a>Grundlagen der dynamischen Datenmaskierung für SQL-Datenbank
Sie richten eine Richtlinie für die dynamische Datenmaskierung im Azure-Portal durch Auswählen des Vorgangs „Dynamische Datenmaskierung“ auf dem Konfigurationsblatt oder auf dem Blatt mit den Einstellungen für Ihre SQL-Datenbank-Instanz ein.

### <a name="dynamic-data-masking-permissions"></a>Berechtigungen für die dynamische Datenmaskierung
Die dynamische Datenmaskierung kann von den Rollen „Azure-Datenbankadministrator“, „Serveradministrator“ oder [SQL-Sicherheits-Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) konfiguriert werden.

### <a name="dynamic-data-masking-policy"></a>Richtlinie für die dynamische Datenmaskierung
* **Von der Maskierung ausgeschlossene SQL-Benutzer:** Eine Gruppe von SQL-Benutzern oder AAD-Identitäten, die in den Ergebnissen von SQL-Abfragen Daten ohne Maskierung erhalten. Benutzer mit Administratorrechten sind immer von der Maskierung ausgeschlossen und bekommen Originaldaten ohne Maskierung angezeigt.
* **Maskierungsregeln:** Eine Gruppe von Regeln, die die zu maskierenden Felder und verwendete Maskierungsfunktion definieren. Mithilfe eines Datenbankschemanamens, Tabellennamens und Spaltennamens können die vorgesehenen Felder bestimmt werden.
* **Maskierungsfunktionen:** Eine Reihe von Methoden, die die Anzeige von Daten in verschiedenen Szenarios steuern.

| Maskierungsfunktion | Maskierungslogik |
| --- | --- |
| **Standard** |**Vollständige Maskierung anhand der Datentypen der festgelegten Felder**<br/><br/>• XXXX oder weniger X-Zeichen verwenden, wenn die Größe des Felds weniger als vier Zeichen für Zeichenfolgendatentypen (nchar, ntext, nvarchar) beträgt.<br/>• Für numerische Datentypen (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real) einen Nullwert verwenden.<br/>• Für Datum/Uhrzeit-Datentypen (date, datetime2, datetime, datetimeoffset, smalldatetime, time) 01-01-1900 verwenden.<br/>• Für SQL-Varianten wird der Standardwert des aktuellen Typs verwendet.<br/>• Für XML wird das Dokument <masked/> verwendet.<br/>• Für spezielle Datentypen (timestamp table, hierarchyid, GUID, binary, image, räumliche varbinary-Typen) einen leeren Wert verwenden. |
| **Kreditkarte** |**Maskierungsmethode, die die letzten vier Ziffern der festgelegten Felder anzeigt** und eine Konstantenzeichenfolge als Präfix in Form einer Kreditkarte hinzufügt.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-Mail** |**Maskierungsmethode, die den ersten Buchstaben und die Domäne durch „XXX.com“ ersetzt** und dafür eine Konstantenzeichenfolge als Präfix in Form einer E-Mail-Adresse verwendet.<br/><br/>aXX@XXXX.com |
| **Zufallszahl** |**Maskierungsmethode, die eine Zufallszahl** entsprechend den ausgewählten Grenzen und den tatsächlichen Datentypen generiert. Wenn die festgelegten Grenzen gleich sind, ist die Maskierungsfunktion eine konstante Zahl.<br/><br/>![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Benutzerdefinierter Text** |**Maskierungsmethode, die die ersten und letzten Zeichen anzeigt** und in der Mitte eine benutzerdefinierte Auffüllzeichenfolge hinzufügt. Wenn die ursprüngliche Zeichenfolge kürzer als das verfügbar gemachte Präfix und Suffix ist, wird nur die Auffüllzeichenfolge verwendet. <br/>prefix[padding]suffix<br/><br/>![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Empfohlene Felder für die Maskierung
Von der DDM-Empfehlungs-Engine werden bestimmte Felder Ihrer Datenbank als potenzielle Felder mit vertraulichen Daten angegeben, bei denen es sich um gute Kandidaten für die Maskierung handelt. Auf dem Blatt „Dynamische Datenmaskierung“ im Portal werden die empfohlenen Spalten für Ihre Datenbank angezeigt. Sie können einfach für eine oder mehrere Spalten auf **Maske hinzufügen** und dann auf **Speichern** klicken, um eine Maskierung auf diese Felder anzuwenden.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Einrichten der dynamischen Datenmaskierung für Ihre Datenbank mithilfe von Powershell-Cmdlets
Siehe [Azure SQL-Datenbank-Cmdlets](https://docs.microsoft.com/powershell/module/azurerm.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Einrichten der dynamischen Datenmaskierung für Ihre Datenbank mithilfe der REST-API
Siehe [Vorgänge für Azure SQL-Datenbank](https://msdn.microsoft.com/library/dn505719.aspx).

