---
title: Behandeln vorübergehender Konnektivitätsfehler für Azure Database for PostgreSQL
description: Erfahren Sie, wie Sie vorübergehende Konnektivitätsfehler für Azure Database for PostgreSQL behandeln.
keywords: PostgreSQL-Verbindung, Verbindungszeichenfolge, Verbindungsprobleme, vorübergehender Fehler, Verbindungsfehler
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 264656da38608026e3f9e866e2184ff55ba102d8
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536218"
---
# <a name="handling-of-transient-connectivity-errors-for-azure-database-for-postgresql"></a>Behandeln vorübergehender Konnektivitätsfehler für Azure Database for PostgreSQL

In diesem Artikel wird das Behandeln von vorübergehenden Fehlern bei der Verbindung mit Azure Database for PostgreSQL beschrieben.

## <a name="transient-errors"></a>Vorübergehende Fehler

Bei einem vorübergehenden Fehler handelt es sich um einen Fehler, der sich von alleine löst. Diese Fehler zeigen sich meist dadurch, dass die Verbindung mit dem Datenbankserver getrennt wird. Außerdem können keine neuen Verbindungen mit einem Server hergestellt werden. Vorübergehende Fehler können z.B. bei Hardware- oder Netzwerkfehlern auftreten. Ein weiterer Grund kann die Einführung einer neuen Version des PaaS-Diensts sein. Meist werden diese Ereignisse in weniger als 60 Sekunden automatisch vom System behoben. Sie sollten beim Entwerfen und Entwickeln von Anwendungen in der Cloud vorübergehende Fehler einkalkulieren. Gehen Sie davon aus, dass sie jederzeit und in jeder Komponente auftreten können, und entwickeln Sie entsprechende Logik zum Umgang mit diesen Situationen.

## <a name="handling-transient-errors"></a>Behandeln vorübergehender Fehler

Vorübergehende Fehler sollten mit Wiederholungslogik behandelt werden. Folgende Situationen sollten berücksichtigt werden:

* Beim Versuch, eine Verbindung zu öffnen, tritt ein Fehler auf.
* Eine Verbindung im Leerlauf wird auf Serverseite getrennt. Ein übermittelter Befehl kann nicht ausgeführt werden.
* Eine aktive Verbindung, die derzeit einen Befehl ausführt, wird getrennt.

Die ersten beiden Fälle können relativ unkompliziert behandelt werden. Versuchen Sie, die Verbindung erneut herzustellen. Wenn Sie Erfolg haben, wurde der vorübergehende Fehler durch das System behoben. Sie können Ihre Instanz von Azure Database for PostgreSQL wiederverwenden. Es wird empfohlen, vor dem Wiederherstellen der Verbindung Wartezeiten einzufügen. Wenn die ersten Verbindungsversuche zu Fehlern führen, brechen Sie die Versuche ab. Auf diese Weise kann das System alle Ressourcen nutzen, um den Fehler zu beheben. Wenden Sie das folgende bewährte Muster an:

* Warten Sie vor dem ersten Wiederholungsversuch fünf Sekunden.
* Erhöhen Sie bei jeder folgenden Wiederholung die Wartezeit exponentiell auf bis zu 60 Sekunden.
* Legen Sie eine maximale Anzahl von Wiederholungen fest, nach denen Ihre Anwendung den Vorgang als fehlerhaft ansieht.

Wenn bei einer Verbindung mit einer aktiven Transaktion ein Fehler auftritt, ist die ordnungsgemäße Wiederherstellung schwieriger. Es gibt zwei Fälle: Wenn die Transaktion schreibgeschützt war, können Sie die Verbindung ohne Gefahr erneut herstellen und die Transaktion wiederholen. Wenn die Transaktion jedoch auch in die Datenbank schreibt, müssen Sie bestimmen, ob für die Transaktion ein Rollback ausgeführt wurde oder ob sie vor dem Auftreten des vorübergehenden Fehlers erfolgreich ausgeführt wurde. In diesem Fall haben Sie möglicherweise nur keine Bestätigung für den Commit vom Datenbankserver erhalten.

Sie können dazu z.B. eine eindeutige ID auf dem Client generieren, die für alle Wiederholungen verwendet wird. Übergeben Sie diese eindeutige ID als Teil der Transaktion an den Server, und speichern Sie sie in einer Spalte mit einer unique-Einschränkung. Auf diese Weise können Sie die Transaktion ohne Risiko erneut ausführen. Sie ist erfolgreich, wenn für die vorherige Transaktion ein Rollback ausgeführt wurde und die vom Client generierte eindeutige ID noch nicht im System vorhanden ist. Sie führt zu einem Fehler, wenn ein doppelter Schlüssel vorhanden ist. Dies ist der Fall, wenn die eindeutige ID zuvor gespeichert wurde, da die vorherige Transaktion erfolgreich abgeschlossen wurde.

Wenn Ihr Programm über Middleware eines Drittanbieters mit Azure Database for PostgreSQL kommuniziert, erkundigen Sie sich beim Anbieter, ob die Middleware Wiederholungslogik für vorübergehende Fehler enthält.

Testen Sie die Wiederholungslogik unbedingt vorab. Versuchen Sie beispielsweise, Ihren Code auszuführen, während Sie die Computeressourcen Ihrer Instanz von Azure Database for PostgreSQL zentral hoch- oder herunterskalieren. Die Anwendung sollte die kurze Ausfallzeit, die während dieses Vorgangs auftritt, ohne Probleme behandeln.

## <a name="next-steps"></a>Nächste Schritte

* [Beheben von Verbindungsproblemen mit Azure Database for PostgreSQL](howto-troubleshoot-common-connection-issues.md)
