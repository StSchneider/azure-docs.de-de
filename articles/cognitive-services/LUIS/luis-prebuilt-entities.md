---
title: Vordefinierte Entitäten
titleSuffix: Azure Cognitive Services
description: LUIS umfasst eine Reihe von vordefinierten Entitäten zur Erkennung allgemeiner Informationstypen, z.B. Datumsangaben, Zeitangaben, Zahlen, Maße und Währungen. Die Unterstützung von vordefinierten Entität variiert je nach Kultur Ihrer LUIS-App.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/05/2018
ms.author: diberry
ms.openlocfilehash: 9e0d1ae39431ca75b43680981802b82f16703d4d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103930"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>Vordefinierte Entitäten zum Erkennen allgemeiner Datentypen

LUIS umfasst eine Reihe von vordefinierten Entitäten zur Erkennung allgemeiner Informationstypen, z.B. Datumsangaben, Zeitangaben, Zahlen, Maße und Währungen. 

## <a name="add-a-prebuilt-entity"></a>Hinzufügen einer vordefinierten Entität

1. Öffnen Sie Ihre App, indem Sie auf der Seite **Meine Apps** auf ihren Namen und anschließend links auf **Entitäten** klicken. 

1. Klicken Sie auf der Seite **Entitäten** auf **Add prebuilt entity** (Vordefinierte Entität hinzufügen).

1. Wählen Sie im Dialogfeld **Add prebuilt entities** (Vordefinierte Entitäten hinzufügen) die vordefinierte Entität „datetimeV2“ aus. 

    ![Dialogfeld „Vordefinierte Entität hinzufügen“](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Wählen Sie **Fertig**aus.

## <a name="publish-the-app"></a>Veröffentlichen der App

Die einfachste Möglichkeit, den Wert einer vordefinierten Entität anzuzeigen, ist eine Abfrage an den veröffentlichten Endpunkt. 

1. Wählen Sie auf der oberen Symbolleiste **Veröffentlichen** aus. Veröffentlichen Sie für die **Produktion**. 

1. Wenn die grüne Erfolgsmeldung angezeigt wird, wählen Sie den Link **Refer to the list of endpoints** (Endpunktliste anzeigen) aus, um die Endpunkte anzuzeigen.

1. Wählen Sie einen Endpunkt aus. An diesen Endpunkt wird eine neue Browserregisterkarte geöffnet. Lassen Sie die Browserregisterkarte geöffnet, und fahren Sie mit dem Abschnitt **Testen** fort.

## <a name="test"></a>Test
Nachdem die Entität hinzugefügt wurde, müssen Sie die App nicht trainieren. 

Testen Sie die neue Absicht am Endpunkt, indem Sie einen Wert für den **q**-Parameter hinzufügen. Verwenden Sie die folgende Tabelle für vorgeschlagene Äußerungen zu **q**:

|Testen der Äußerung|Entitätswert|
|--|:--|
|Buche einen Flug morgen|2018-10-19|
|Sage Termin am 3. März ab|LUIS hat den letzten 3. März (3.3.2018) und den nächsten 3. März (3.3.2019) zurückgegeben, da in der Äußerung kein Jahr angegeben wurde.|
|Plane eine Besprechung um 10 Uhr|10:00:00|

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Markieren von Entitäten mit einem vordefinierten Entitätstoken
 Wenn Sie Text wie z.B. `HH-1234` als benutzerdefinierte Entität markieren möchten _und_ dem Modell eine [vordefinierte Anzahl](luis-reference-prebuilt-number.md) hinzugefügt haben, können Sie die benutzerdefinierte Entität nicht im LUIS-Portal markieren. Sie können sie mit der API markieren. 

 Um diese Art von Token, bei der ein Teil bereits mit einer vordefinierten Entität markiert ist, zu markieren, entfernen Sie die vordefinierte Entität aus der LUIS-App. Sie müssen die App nicht trainieren. Markieren Sie anschließend das Token mit Ihrer eigenen benutzerdefinierten Entität. Fügen Sie danach die vordefinierte Entität wieder in die LUIS-App ein.

 Betrachten Sie als weiteres Beispiel die Äußerung als eine Liste von Klasseneinstellungen: `I want first year spanish, second year calculus, and fourth year english lit.` Wenn der LUIS-App die vordefinierte Ordnungszahl hinzugefügt wurde, sind `first`, `second` und `fourth` bereits mit Ordnungszahlen markiert. Wenn Sie die Ordnungszahl und die Klasse erfassen möchten, können Sie eine zusammengesetzte Entität erstellen und die vordefinierte Ordnungszahl und die benutzerdefinierte Entität als Klassennamen umschließen.

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Referenz zu vordefinierten Entitäten](./luis-reference-prebuilt-entities.md)
