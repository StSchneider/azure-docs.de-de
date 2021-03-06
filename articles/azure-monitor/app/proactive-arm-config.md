---
title: Konfigurieren der Einstellungen für die intelligente Erkennungsregel von Azure Application Insights mit Azure Resource Manager-Vorlagen | Microsoft-Dokumentation
description: Automatisieren der Verwaltung und Konfiguration der intelligenten Erkennungsregeln von Azure Application Insights mit Azure Resource Manager-Vorlagen
services: application-insights
documentationcenter: ''
author: harelbr
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/19/2018
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: b1f4d278079b81b4a224dc4712426d1f078de110
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020403"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Verwalten von intelligenten Erkennungsregeln von Azure Application Insights mit Azure Resource Manager-Vorlagen

Intelligente Erkennungsregeln in Application Insights können mit [Azure Resource Manager-Vorlagen](../../azure-resource-manager/resource-group-authoring-templates.md) verwaltet und konfiguriert werden.
Diese Methode kann bei der Bereitstellung neuer Application Insights-Ressourcen mit Azure Resource Manager-Automatisierung oder zur Änderung der Einstellungen vorhandener Ressourcen verwendet werden.

## <a name="smart-detection-rule-configuration"></a>Konfiguration der intelligenten Erkennungsregel

Sie können die folgenden Einstellungen für intelligente Erkennungsregel konfigurieren:
- Wenn die Regel aktiviert ist (der Standardwert ist **true**.)
- Wenn E-Mails an den Abonnementbesitzer, die Mitwirkenden und Leser gesendet werden sollen, wenn eine Erkennung gefunden wird (der Standardwert ist **true**.)
- Alle weiteren E-Mail-Empfänger, die eine Benachrichtigung erhalten sollen, wenn eine Erkennung gefunden wird.

Um die Konfiguration der Regeleinstellungen über den Azure Resource Manager zu ermöglichen, ist die Konfiguration der intelligenten Erkennungsregeln nun als interne Ressource innerhalb der Application Insights-Ressource mit dem Namen **ProactiveDetectionConfigs** verfügbar.
Für maximale Flexibilität kann jede intelligente Erkennungsregel mit individuellen Benachrichtigungseinstellungen konfiguriert werden.

## <a name="examples"></a>Beispiele

Im Folgenden finden Sie einige Beispiele dafür, wie Sie die Einstellungen der intelligenten Erkennungsregeln mithilfe von Azure Resource Manager-Vorlagen konfigurieren können.
Alle Beispiele beziehen sich auf eine Application Insights-Ressource namens _„myApplication“_ und auf die „long dependency duration smart detection rule“, die intern als _„longdependencyduration“_ bezeichnet ist.
Stellen Sie sicher, dass Sie den Application Insights-Ressourcennamen ersetzen und den entsprechenden internen Namen der intelligenten Erkennungsregel angeben. In der folgenden Tabelle finden Sie eine Liste der entsprechenden internen Azure Resource Manager-Namen für jede intelligente Erkennungsregel.

### <a name="disable-a-smart-detection-rule"></a>Deaktivieren einer intelligenten Erkennungsregel

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Deaktivieren der Sendung von E-Mail-Benachrichtigungen für eine intelligente Erkennungsregel

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Hinzufügen zusätzlicher E-Mail-Empfänger für eine intelligente Erkennungsregel

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

## <a name="smart-detection-rule-names"></a>Name der intelligente Erkennungsregel

Im Folgenden finden Sie eine Tabelle mit den Namen der intelligenten Erkennungsregeln, wie sie im Portal angezeigt werden, zusammen mit ihren internen Namen, die in der Azure Resource Manager-Vorlage verwendet werden sollten.

> [!NOTE]
> Intelligente Erkennungsregeln, die als Vorschau markiert sind, unterstützen keine E-Mail-Benachrichtigungen. Aus diesem Grund können Sie nur die enabled-Eigenschaft für diese Regeln festlegen. 

| Name der Regel im Azure-Portal | Interner Name
|:---|:---|
| Langsame Seitenladezeit | slowpageloadtime |
| Langsame Serverantwortzeit | slowserverresponsetime |
| Lange Abhängigkeitsdauer | longdependencyduration |
| Beeinträchtigung der Serverantwortzeit | degradationinserverresponsetime |
| Leistungsminderung der Abhängigkeitsdauer | degradationindependencyduration |
| Verschlechterung des Schweregrads der Ablaufverfolgung (Vorschau) | extension_traceseveritydetector |
| Anormaler Anstieg in Ausnahmevolume (Vorschau) | extension_exceptionchangeextension |
| Möglicher Speicherverluste erkannt (Vorschau) | extension_memoryleakextension |
| Mögliches Sicherheitsproblem erkannt (Vorschau) | extension_securityextensionspackage |
| Ressourcenverwendungsproblem erkannt (Vorschau) | extension_resourceutilizationextensionspackage |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur automatischen Erkennung finden Sie hier:

- [Anomalien bei Fehlern](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Arbeitsspeicherverluste](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Leistungsanomalien](../../azure-monitor/app/proactive-performance-diagnostics.md)