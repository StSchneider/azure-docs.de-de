---
title: Ereignisschema des Azure-Aktivitätsprotokolls
description: Informieren Sie sich über das Ereignisschema für Daten, die in das Aktivitätsprotokoll ausgegeben werden
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 4/12/2018
ms.author: dukek
ms.component: logs
ms.openlocfilehash: 64b92a758d3d5f713b58a5e310a897ac1f11024d
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714830"
---
# <a name="azure-activity-log-event-schema"></a>Ereignisschema des Azure-Aktivitätsprotokolls
Das **Azure-Aktivitätsprotokoll** ist ein Protokoll, das einen Einblick in alle Ereignisse auf Abonnementebene ermöglicht, die in Azure aufgetreten sind. Dieser Artikel beschreibt das Ereignisschema pro Datenkategorie. Das Schema der Daten unterscheidet sich, je nachdem, ob Sie die Daten im Portal, in PowerShell, auf der Befehlszeilenschnittstelle oder direkt über die REST-API lesen, im Gegensatz zum [Streamen der Daten in den Speicher oder zu Event Hubs mithilfe eines Protokollprofils](./../../azure-monitor/platform/activity-logs-overview.md#export-the-activity-log-with-a-log-profile). Im Beispiel unten ist zu sehen, in welcher Weise das Schema über das Portal, PowerShell, die Befehlszeilenschnittstelle und REST-API zur Verfügung gestellt wird. Eine Zuordnung dieser Eigenschaften zum [Azure-Diagnoseprotokollschema](./tutorial-dashboards.md) findet sich am Ende dieses Artikels.

## <a name="administrative"></a>Administrative
Diese Kategorie enthält die Datensätze aller Erstellungs-, Aktualisierungs-, Lösch- und Aktionsvorgänge, die über Resource Manager ausgeführt wurden. Zu den Ereignissen in dieser Kategorie gehört das Erstellen eines virtuellen Computers und das Löschen einer Netzwerksicherheitsgruppe. Jede Aktion, die von einem Benutzer oder einer Anwendung mithilfe von Resource Manager ausgeführt wird, wird als Vorgang für einen bestimmten Ressourcentyp modelliert. Wenn der Vorgangstyp „Schreiben“, „Löschen“ oder „Aktion“ ist, werden die Datensätze zum Start und zum Erfolg oder Fehler dieses Vorgangs in der Kategorie „Administration“ aufgezeichnet. Die Kategorie „Administration“ enthält außerdem alle Änderungen an der rollenbasierten Zugriffssteuerung in einem Abonnement.

### <a name="sample-event"></a>Beispielereignis
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Beschreibungen der Eigenschaften
| Elementname | BESCHREIBUNG |
| --- | --- |
| authorization |Blob mit RBAC-Eigenschaften des Ereignisses. Enthält normalerweise die Eigenschaften „action“, „role“ und „scope“. |
| caller |E-Mail-Adresse des Benutzers, der den Vorgang, UPN-Anspruch oder SPN-Anspruch auf Grundlage der Verfügbarkeit ausgeführt hat. |
| channels |Einer der folgenden Werte: „Admin“, „Operation“ |
| claims |Das JWT-Token, das von Active Directory zum Authentifizieren des Benutzers oder der Anwendung zur Ausführung dieses Vorgangs in Resource Manager verwendet wird. |
| correlationId |Normalerweise eine GUID im Zeichenfolgenformat. Ereignisse, die über die gleiche correlationId verfügen, gehören zu derselben übergeordneten Aktion. |
| Beschreibung |Statische Beschreibung eines Ereignisses in Textform. |
| eventDataId |Eindeutiger Bezeichner eines Ereignisses. |
| httpRequest |Blob, das die HTTP-Anforderung beschreibt. Umfasst üblicherweise „clientRequestId“, „clientIpAddress“ und „method“ (HTTP-Methode, z.B. PUT). |
| level |Ebene des Ereignisses. Einer der folgenden Werte: „Critical“, „Error“, „Warning“ und „Informational“ |
| resourceGroupName |Name der Ressourcengruppe für die betroffene Ressource. |
| resourceProviderName |Name des Ressourcenanbieters für die betroffene Ressource. |
| Ressourcen-ID |Ressourcen-ID der betroffenen Ressource. |
| operationId |Eine GUID, die von den Ereignissen eines einzelnen Vorgangs gemeinsam genutzt wird. |
| operationName |Name des Vorgangs. |
| Eigenschaften |Satz mit `<Key, Value>`-Paaren (Wörterbuch), die Details des Ereignisses beschreiben. |
| status |Zeichenfolge, die den Status des Vorgangs beschreibt. Gängige Werte: „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“, „Resolved“. |
| subStatus |Üblicherweise der HTTP-Statuscode des entsprechenden REST-Aufrufs, kann aber auch weitere Zeichenfolgen zur Beschreibung eines untergeordneten Status enthalten, z. B. die folgenden gängigen Werte: OK (HTTP-Statuscode: 200), Erstellt (HTTP-Statuscode: 201), Akzeptiert (HTTP-Statuscode: 202), Kein Inhalt (HTTP-Statuscode: 204), Ungültige Anforderung (HTTP-Statuscode: 400), Nicht gefunden (HTTP-Statuscode: 404), Konflikt (HTTP-Statuscode: 409), Interner Serverfehler (HTTP-Statuscode: 500), Dienst nicht verfügbar (HTTP-Statuscode: 503), Gatewaytimeout (HTTP-Statuscode: 504) |
| eventTimestamp |Zeitstempel der Ereignisgenerierung durch den Azure-Dienst, der die zum Ereignis gehörende Anforderung verarbeitet hat. |
| submissionTimestamp |Zeitstempel des Zeitpunkts, ab dem das Ereignis für Abfragen verfügbar war. |
| subscriptionId |Die Azure-Abonnement-ID. |

## <a name="service-health"></a>Dienstintegrität
Diese Kategorie enthält Datensätze zu allen Incidents im Zusammenhang mit der Dienstintegrität, die in Azure aufgetreten sind. Ein Beispiel für ein Ereignis in dieser Kategorie ist „Ausfallzeiten bei SQL Azure in der Region ‚USA, Osten‘“. Es gibt fünf Typen von Ereignissen zur Dienstintegrität: „Action Required“, „Assisted Recovery“, „Incident“, „Maintenance“, „Information“ oder „Security“. Sie werden nur angezeigt, wenn eine Ressource in Ihrem Abonnement von dem Ereignis betroffen wäre.

### <a name="sample-event"></a>Beispielereignis
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Informationen zu den Werten in den Eigenschaften finden Sie im Artikel [Anzeigen von Dienstintegritätsbenachrichtigungen im Azure-Portal](./../../azure-monitor/platform/service-notifications.md).

## <a name="resource-health"></a>Ressourcenintegrität
Diese Kategorie enthält Datensätze zu allen Ereignissen im Zusammenhang mit der Ressourcenintegrität, die für Ihre Azure-Ressourcen aufgetreten sind. Ein Beispiel für die Art der Ereignisse, die in dieser Kategorie angezeigt werden, ist „Integritätsstatus des virtuellen Computers ist zu Nicht verfügbar gewechselt“. Ereignisse zur Ressourcenintegrität können über einen von vier Integritätsstatus verfügen: „Available“, „Unavailable“, „Degraded“ und „Unknown“. Darüber hinaus können Ereignisse zur Ressourcenintegrität kategorisiert werden. Dabei sind die Kategorien „Von der Plattform initiiert“ oder „Vom Benutzer initiiert“ verfügbar.

### <a name="sample-event"></a>Beispielereignis

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription Id>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>Beschreibungen der Eigenschaften
| Elementname | BESCHREIBUNG |
| --- | --- |
| channels | Immer „Admin, Operation“ |
| correlationId | Eine GUID im Zeichenfolgenformat. |
| Beschreibung |Statische Beschreibung des Warnungsereignisses. |
| eventDataId |Eindeutiger Bezeichner des Warnungsereignisses. |
| category | Immer „ResourceHealth“ |
| eventTimestamp |Zeitstempel der Ereignisgenerierung durch den Azure-Dienst, der die zum Ereignis gehörende Anforderung verarbeitet hat. |
| level |Ebene des Ereignisses. Einer der folgenden Werte: „Critical“, „Error“, „Warning“, „Informational“ oder „Verbose“ |
| operationId |Eine GUID, die von den Ereignissen eines einzelnen Vorgangs gemeinsam genutzt wird. |
| operationName |Name des Vorgangs. |
| resourceGroupName |Der Name der Ressourcengruppe, die die Ressource enthält. |
| resourceProviderName |Immer „Microsoft.Resourcehealth/healthevent/action“. |
| resourceType | Der Typ der Ressource, die von einem Resource Health-Ereignis betroffen war. |
| Ressourcen-ID | Der Name der Ressourcen-ID für die betroffene Ressource. |
| status |Zeichenfolge, die den Status des Integritätsereignisses beschreibt. Mögliche Werte: „Active“, „Resolved“, „InProgress“, „Updated“. |
| subStatus | In der Regel für Warnungen null. |
| submissionTimestamp |Zeitstempel des Zeitpunkts, ab dem das Ereignis für Abfragen verfügbar war. |
| subscriptionId |Azure-Abonnement-ID |
| Eigenschaften |Satz mit `<Key, Value>`-Paaren (Wörterbuch), die Details des Ereignisses beschreiben.|
| properties.title | Eine benutzerfreundliche Zeichenkette, die den Integritätsstatus der Ressource beschreibt. |
| properties.details | Eine benutzerfreundliche Zeichenkette, die weitere Details zum Ereignis aufführt. |
| properties.currentHealthStatus | Der aktuelle Integritätsstatus der Ressource. Einer der folgenden Werte: „Available“, „Unavailable“, „Degraded“ und „Unknown“. |
| properties.previousHealthStatus | Der vorherige Integritätsstatus der Ressource. Einer der folgenden Werte: „Available“, „Unavailable“, „Degraded“ und „Unknown“. |
| properties.type | Eine Beschreibung des Typs des Resource Health-Ereignisses. |
| properties.cause | Eine Beschreibung der Ursache des Resource Health-Ereignisses. Entweder „UserInitiated“ oder „PlatformInitiated“. |


## <a name="alert"></a>Warnung
Diese Kategorie enthält die Datensätze zu allen Aktivierungen von Azure-Warnungen. Ein Beispiel für ein Ereignis in dieser Kategorie ist „CPU-Auslastung auf „myVM“ lag in den letzten 5 Minuten über 80“. Eine Vielzahl von Azure-Systemen weist ein Konzept für Warnungen auf: Sie können eine Regel definieren und erhalten eine Benachrichtigung, wenn die Bedingungen mit der Regel übereinstimmen. Jedes Mal, wenn ein unterstützter Azure-Warnungstyp „aktiviert“ wird oder die Bedingungen erfüllt sind, sodass eine Benachrichtigung generiert wird, wird ein Datensatz der Aktivierung auch in dieser Kategorie des Aktivitätsprotokolls abgelegt.

### <a name="sample-event"></a>Beispielereignis

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Beschreibungen der Eigenschaften
| Elementname | BESCHREIBUNG |
| --- | --- |
| caller | Immer „Microsoft.Insights/alertRules“ |
| channels | Immer „Admin, Operation“ |
| claims | JSON-Blob mit dem Dienstprinzipalnamen (Service Principal Name, SPN) oder dem Ressourcentyp der Warnungs-Engine. |
| correlationId | Eine GUID im Zeichenfolgenformat. |
| Beschreibung |Statische Beschreibung des Warnungsereignisses. |
| eventDataId |Eindeutiger Bezeichner des Warnungsereignisses. |
| level |Ebene des Ereignisses. Einer der folgenden Werte: „Critical“, „Error“, „Warning“ und „Informational“ |
| resourceGroupName |Bei einer Metrikwarnung der Name der Ressourcengruppe für die betroffene Ressource. Bei anderen Warnungstypen ist dies der Name der Ressourcengruppe, die die Warnung selbst enthält. |
| resourceProviderName |Bei einer Metrikwarnung der Name des Ressourcenanbieters für die betroffene Ressource. Bei anderen Warnungstypen ist dies der Name des Ressourcenanbieters für die Warnung selbst. |
| Ressourcen-ID | Bei einer Metrikwarnung der Name der Ressourcen-ID für die betroffene Ressource. Bei anderen Warnungstypen ist dies die Ressourcen-ID der Warnungsressource selbst. |
| operationId |Eine GUID, die von den Ereignissen eines einzelnen Vorgangs gemeinsam genutzt wird. |
| operationName |Name des Vorgangs. |
| Eigenschaften |Satz mit `<Key, Value>`-Paaren (Wörterbuch), die Details des Ereignisses beschreiben. |
| status |Zeichenfolge, die den Status des Vorgangs beschreibt. Gängige Werte: „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“, „Resolved“. |
| subStatus | In der Regel für Warnungen null. |
| eventTimestamp |Zeitstempel der Ereignisgenerierung durch den Azure-Dienst, der die zum Ereignis gehörende Anforderung verarbeitet hat. |
| submissionTimestamp |Zeitstempel des Zeitpunkts, ab dem das Ereignis für Abfragen verfügbar war. |
| subscriptionId |Die Azure-Abonnement-ID. |

### <a name="properties-field-per-alert-type"></a>Feld „properties“ pro Warnungstyp
Das Feld „properties“ enthält abhängig von der Quelle des Warnungsereignisses unterschiedliche Werte. Zwei allgemeine Ereignisanbieter für Warnungen sind Aktivitätsprotokollwarnungen und Metrikwarnungen.

#### <a name="properties-for-activity-log-alerts"></a>Eigenschaften für Aktivitätsprotokollwarnungen
| Elementname | BESCHREIBUNG |
| --- | --- |
| properties.subscriptionId | Die Abonnement-ID aus dem Aktivitätsprotokollereignis, das verursacht hat, dass diese Warnungsregel des Aktivitätsprotokolls aktiviert wurde. |
| properties.eventDataId | Die Ereignisdaten-ID aus dem Aktivitätsprotokollereignis, das verursacht hat, dass diese Warnungsregel des Aktivitätsprotokolls aktiviert wurde. |
| properties.resourceGroup | Die Ressourcengruppe aus dem Aktivitätsprotokollereignis, das verursacht hat, dass diese Warnungsregel des Aktivitätsprotokolls aktiviert wurde. |
| properties.resourceId | Die Ressourcen-ID aus dem Aktivitätsprotokollereignis, das verursacht hat, dass diese Warnungsregel des Aktivitätsprotokolls aktiviert wurde. |
| properties.eventTimestamp | Der Ereigniszeitstempel aus dem Aktivitätsprotokollereignis, das verursacht hat, dass diese Warnungsregel des Aktivitätsprotokolls aktiviert wurde. |
| properties.operationName | Der Vorgangsname aus dem Aktivitätsprotokollereignis, das verursacht hat, dass diese Warnungsregel des Aktivitätsprotokolls aktiviert wurde. |
| properties.status | Der Status aus dem Aktivitätsprotokollereignis, das verursacht hat, dass diese Warnungsregel des Aktivitätsprotokolls aktiviert wurde.|

#### <a name="properties-for-metric-alerts"></a>Eigenschaften für Metrikwarnungen
| Elementname | BESCHREIBUNG |
| --- | --- |
| properties.RuleUri | Ressourcen-ID der Metrikwarnungsregel selbst. |
| properties.RuleName | Der Name der Metrikwarnungsregel. |
| properties.RuleDescription | Die Beschreibung der Metrikwarnungsregel (wie in der Warnungsregel definiert). |
| properties.Threshold | Der Schwellenwert, der bei der Auswertung der Metrikwarnungsregel verwendet wird. |
| properties.WindowSizeInMinutes | Die Fenstergröße, die bei der Auswertung der Metrikwarnungsregel verwendet wird. |
| properties.Aggregation | Der Aggregationstyp, der in der Metrikwarnungsregel definiert ist. |
| properties.Operator | Der bedingte Operator, der bei der Auswertung der Metrikwarnungsregel verwendet wird. |
| properties.MetricName | Der Metrikname der Metrik, die bei der Auswertung der Metrikwarnungsregel verwendet wird. |
| properties.MetricUnit | Die Metrikeinheit für die Metrik, die bei der Auswertung der Metrikwarnungsregel verwendet wird. |

## <a name="autoscale"></a>Autoscale
Diese Kategorie enthält Datensätze zu Ereignissen im Zusammenhang mit der Engine für die automatische Skalierung – basierend auf den Einstellungen für die automatische Skalierung, die Sie in Ihrem Abonnement definiert haben. Ein Beispiel für Ereignisse in dieser Kategorie ist „Fehler beim automatischen zentralen Hochskalieren“. Mit der automatischen Skalierung können Sie die Anzahl der Instanzen eines unterstützten Ressourcentyps basierend auf der Tageszeit und/oder Lastdaten (Metrik) mithilfe einer Einstellung für die automatische Skalierung automatisch horizontal hoch- oder herunterskalieren. Wenn die Bedingungen zum zentralen Hoch- oder Herunterskalieren erfüllt sind, werden Ereignisse zum Start und zum Erfolg oder Fehler in dieser Kategorie aufgezeichnet.

### <a name="sample-event"></a>Beispielereignis
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Beschreibungen der Eigenschaften
| Elementname | BESCHREIBUNG |
| --- | --- |
| caller | Immer „Microsoft.Insights/autoscaleSettings“ |
| channels | Immer „Admin, Operation“ |
| claims | JSON-Blob mit dem Dienstprinzipalnamen (Service Principal Name, SPN) oder dem Ressourcentyp der Engine für die automatische Skalierung. |
| correlationId | Eine GUID im Zeichenfolgenformat. |
| Beschreibung |Statische Beschreibung des Ereignisses der automatischen Skalierung. |
| eventDataId |Eindeutiger Bezeichner des Ereignisses der automatischen Skalierung. |
| level |Ebene des Ereignisses. Einer der folgenden Werte: „Critical“, „Error“, „Warning“ und „Informational“ |
| resourceGroupName |Name der Ressourcengruppe der Einstellung für die automatische Skalierung. |
| resourceProviderName |Name des Ressourcenanbieters der Einstellung für die automatische Skalierung. |
| Ressourcen-ID |Ressourcen-ID der Einstellung für die automatische Skalierung. |
| operationId |Eine GUID, die von den Ereignissen eines einzelnen Vorgangs gemeinsam genutzt wird. |
| operationName |Name des Vorgangs. |
| Eigenschaften |Satz mit `<Key, Value>`-Paaren (Wörterbuch), die Details des Ereignisses beschreiben. |
| properties.Description | Detaillierte Beschreibung des Vorgangs der Engine für die automatische Skalierung. |
| properties.ResourceName | Ressourcen-ID der betroffenen Ressource (die Ressource, für die die Skalierungsaktion ausgeführt wurde) |
| properties.OldInstancesCount | Die Anzahl von Instanzen, bevor die automatische Skalierung wirksam war. |
| properties.NewInstancesCount | Die Anzahl von Instanzen, nachdem die automatische Skalierung wirksam war. |
| properties.LastScaleActionTime | Der Zeitstempel des Zeitpunkts, zu dem die automatische Skalierung aufgetreten ist. |
| status |Zeichenfolge, die den Status des Vorgangs beschreibt. Gängige Werte: „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“, „Resolved“. |
| subStatus | In der Regel für die automatische Skalierung null. |
| eventTimestamp |Zeitstempel der Ereignisgenerierung durch den Azure-Dienst, der die zum Ereignis gehörende Anforderung verarbeitet hat. |
| submissionTimestamp |Zeitstempel des Zeitpunkts, ab dem das Ereignis für Abfragen verfügbar war. |
| subscriptionId |Die Azure-Abonnement-ID. |

## <a name="security"></a>Sicherheit
Diese Kategorie enthält den Datensatz, der von Warnungen in Azure Security Center generiert wurde. Ein Beispiel für den Typ der Ereignisse, die in dieser Kategorie angezeigt werden, ist „Verdächtige Datei mit doppelter Erweiterung ausgeführt“.

### <a name="sample-event"></a>Beispielereignis
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Beschreibungen der Eigenschaften
| Elementname | BESCHREIBUNG |
| --- | --- |
| channels | Immer „Vorgang“ |
| correlationId | Eine GUID im Zeichenfolgenformat. |
| Beschreibung |Statische Beschreibung des Sicherheitsereignisses. |
| eventDataId |Eindeutiger Bezeichner des Sicherheitsereignisses. |
| eventName |Anzeigename des Sicherheitsereignisses. |
| id |Eindeutiger Ressourcenbezeichner des Sicherheitsereignisses. |
| level |Ebene des Ereignisses. Einer der folgenden Werte: „Critical“, „Error“, „Warning“ oder „Informational“ |
| resourceGroupName |Name der Ressourcengruppe für die Ressource. |
| resourceProviderName |Name des Ressourcenanbieters für die Azure Security Center. Immer „Microsoft.Security“. |
| resourceType |Der Typ der Ressource, die das Sicherheitsereignis generiert hat, z. B. „Microsoft.Security/locations/alerts“. |
| Ressourcen-ID |Ressourcen-ID der Sicherheitswarnung. |
| operationId |Eine GUID, die von den Ereignissen eines einzelnen Vorgangs gemeinsam genutzt wird. |
| operationName |Name des Vorgangs. |
| Eigenschaften |Satz mit `<Key, Value>`-Paaren (Wörterbuch), die Details des Ereignisses beschreiben. Diese Eigenschaften variieren je nach Typ der Sicherheitswarnung. Eine Beschreibung der Warnungstypen, die aus Security Center stammen, finden Sie [auf dieser Seite](../../security-center/security-center-alerts-type.md). |
| properties.Severity |Der Schweregrad. Mögliche Werte sind „Hoch“, „Mittel“ oder „Niedrig“. |
| status |Zeichenfolge, die den Status des Vorgangs beschreibt. Gängige Werte: „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“, „Resolved“. |
| subStatus | Für Sicherheitsereignisse in der Regel NULL. |
| eventTimestamp |Zeitstempel der Ereignisgenerierung durch den Azure-Dienst, der die zum Ereignis gehörende Anforderung verarbeitet hat. |
| submissionTimestamp |Zeitstempel des Zeitpunkts, ab dem das Ereignis für Abfragen verfügbar war. |
| subscriptionId |Die Azure-Abonnement-ID. |

## <a name="recommendation"></a>Empfehlung
Diese Kategorie enthält den Datensatz mit den neuen Empfehlungen, die für Ihre Dienste generiert werden. Ein Beispiel für eine Empfehlung wäre „Verwenden Sie für eine verbesserte Fehlertoleranz Verfügbarkeitsgruppen“. Es gibt vier Typen von „Recommendation“-Ereignissen, die generiert werden können: „High Availability“, „Performance“, „Security“ und „Cost Optimization“. 

### <a name="sample-event"></a>Beispielereignis
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Beschreibungen der Eigenschaften
| Elementname | BESCHREIBUNG |
| --- | --- |
| channels | Immer „Vorgang“ |
| correlationId | Eine GUID im Zeichenfolgenformat. |
| Beschreibung |Statische Beschreibung des Empfehlungsereignisses. |
| eventDataId | Eindeutiger Bezeichner des Empfehlungsereignisses. |
| category | Immer „Empfehlung“ |
| id |Eindeutiger Ressourcenbezeichner des Empfehlungsereignisses. |
| level |Ebene des Ereignisses. Einer der folgenden Werte: „Critical“, „Error“, „Warning“ oder „Informational“ |
| operationName |Name des Vorgangs.  Immer „Microsoft.Advisor/generateRecommendations/action“|
| resourceGroupName |Name der Ressourcengruppe für die Ressource. |
| resourceProviderName |Name des Ressourcenanbieters für die Ressource, für die diese Empfehlung gilt, z.B. „MICROSOFT.COMPUTE“. |
| resourceType |Name des Ressourcentyps für die Ressource, für die diese Empfehlung gilt, z.B. „MICROSOFT.COMPUTE/virtualmachines“. |
| Ressourcen-ID |Ressourcen-ID der Ressource, für die die Empfehlung gilt. |
| status | Immer „Aktiv“ |
| submissionTimestamp |Zeitstempel des Zeitpunkts, ab dem das Ereignis für Abfragen verfügbar war. |
| subscriptionId |Die Azure-Abonnement-ID. |
| Eigenschaften |Satz mit `<Key, Value>`-Paaren (also ein Wörterbuch), die die Details der Empfehlung beschreiben.|
| properties.recommendationSchemaVersion| Schemaversion der Empfehlungseigenschaften, die im Aktivitätsprotokolleintrag veröffentlicht werden. |
| properties.recommendationCategory | Kategorie der Empfehlung. Mögliche Werte sind „Hochverfügbarkeit“, „Leistung“, „Sicherheit“ und „Kosten“. |
| properties.recommendationImpact| Auswirkung der Empfehlung. Mögliche Werte sind „Hoch“, „Mittel“ oder „Niedrig“. |
| properties.recommendationRisk| Risiko der Empfehlung. Mögliche Werte sind „Fehler“, „Warnung“, „Kein“. |

## <a name="mapping-to-diagnostic-logs-schema"></a>Zuordnung zum Diagnoseprotokollschema

Beim Streamen des Azure-Aktivitätsprotokolls an ein Speicherkonto oder den Event Hubs-Namespace entsprechen die Daten dem [Azure-Diagnoseprotokollschema](./tutorial-dashboards.md). Hier finden Sie die Zuordnung der Eigenschaften aus dem oben genannten Schema zum Diagnoseprotokollschema:

| Eigenschaft im Diagnoseprotokollschema | Eigenschaft im REST-API-Schema des Aktivitätsprotokolls | Notizen |
| --- | --- | --- |
| time | eventTimestamp |  |
| Ressourcen-ID | Ressourcen-ID | „subscriptionId“, „resourceType“ und „resourceGroupName“ werden alle aus der „resourceId“ abgeleitet. |
| operationName | operationName.value |  |
| category | Teil des Vorgangsnamens | Entnahme des Vorgangstyps – „Write“/„Delete“/„Action“ |
| resultType | status.value | |
| resultSignature | substatus.value | |
| resultDescription | Beschreibung |  |
| durationMs | N/V | Immer 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | Ansprüche und Autorisierungseigenschaften |  |
| Ebene | Ebene |  |
| location | N/V | Ort, an dem das Ereignis verarbeitet wurde. *Dies ist nicht der Speicherort der Ressource, sondern der Ort, an dem das Ereignis verarbeitet wurde. Diese Eigenschaft wird in einem kommenden Update entfernt.* |
| Eigenschaften | properties.eventProperties |  |
| properties.eventCategory | category | Wenn „properties.eventCategory“ nicht vorhanden ist, ist die Kategorie „Administrative“ |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | Eigenschaften |  |


## <a name="next-steps"></a>Nächste Schritte
* [Weitere Informationen zum Aktivitätsprotokoll (bisher „Überwachungsprotokolle“)](../../azure-monitor/platform/activity-logs-overview.md)
* [Stream the Azure Activity Log to Event Hubs (Streamen des Azure-Aktivitätsprotokolls auf Event Hubs)](../../azure-monitor/platform/activity-logs-stream-event-hubs.md)
