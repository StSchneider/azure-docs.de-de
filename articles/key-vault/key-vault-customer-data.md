---
title: 'Azure Key Vault-Funktionen für Kundendaten: Azure Key Vault | Microsoft-Dokumentation'
description: Informationen zu Kundendaten in Key Vault
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: reference
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: bcfa2c9202d99dba83db57bfe286271da0e64e43
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077826"
---
# <a name="azure-key-vault-customer-data-features"></a>Azure Key Vault-Funktionen für Kundendaten

Azure Key Vault empfängt Kundendaten während der Erstellung oder Aktualisierung von Tresoren, Schlüsseln, Geheimnissen, Zertifikaten und verwalteten Speicherkonten. Diese Kundendaten werden im Azure-Portal und über die REST-API direkt angezeigt. Kundendaten können durch Aktualisieren oder Löschen des jeweiligen Objekts, das die Daten enthält, bearbeitet oder gelöscht werden.

Wenn ein Benutzer oder eine Anwendung auf Key Vault zugreift, werden Systemzugriffsprotokolle generiert. Detaillierte Zugriffsprotokolle stehen den Kunden über Azure Insights zur Verfügung.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifizieren von Kundendaten

Die folgenden Informationen identifizieren Kundendaten in Azure Key Vault:

- Zugriffsrichtlinien für Azure Key Vault enthalten Objekt-IDs, die Benutzer, Gruppen oder Anwendungen darstellen.
- Zertifikatantragsteller können E-Mail-Adressen oder andere Benutzer- oder organisatorische IDs enthalten.
- Zertifikatkontakte können E-Mail-Adressen, Namen oder Telefonnummern von Benutzern enthalten.
- Zertifikataussteller können E-Mail-Adressen, Namen, Telefonnummern, Kontoanmeldeinformationen und organisatorische Details enthalten.
- Auf Objekte in Azure Key Vault können beliebige Tags angewendet werden. Zu diesen Objekten gehören Tresore, Schlüssel, Geheimnisse, Zertifikate und Speicherkonten. Die verwendeten Tags können persönliche Daten enthalten.
- Azure Key Vault-Zugriffsprotokolle enthalten Objekt-IDs, [UPNs](../active-directory/hybrid/plan-connect-userprincipalname.md) und IP-Adressen für jeden REST-API-Aufruf.
- Azure Key Vault-Diagnoseprotokolle können Objekt-IDs und IP-Adressen für REST-API-Aufrufe enthalten.

## <a name="deleting-customer-data"></a>Löschen von Kundendaten

Mit den REST-APIs, Portal-Oberflächenelementen und SDKs, die zum Erstellen von Tresoren, Schlüsseln, Geheimnissen, Zertifikaten und verwalteten Speicherkonten verwendet werden, können diese Objekte auch aktualisiert und gelöscht werden.

Durch vorläufiges Löschen haben Sie die Möglichkeit, gelöschte Daten bis zu 90 Tage nach dem Löschen wiederherzustellen. Beim vorläufigen Löschen können die Daten vor Ablauf der Beibehaltungsdauer von 90 Tagen durch Ausführen eines Bereinigungsvorgangs dauerhaft gelöscht werden. Wenn der Tresor oder das Abonnement so konfiguriert ist, dass Bereinigungsvorgänge blockiert werden, können die Daten erst nach Ablauf der geplanten Beibehaltungsdauer dauerhaft gelöscht werden.

## <a name="exporting-customer-data"></a>Exportieren von Kundendaten

Mit den REST-APIs, Portal-Oberflächenelementen und SDKs, die zum Erstellen von Tresoren, Schlüsseln, Geheimnissen, Zertifikaten und verwalteten Speicherkonten verwendet werden, können Sie diese Objekte auch anzeigen und exportieren.

Die Azure Key Vault-Zugriffsprotokollierung ist eine optionale Funktion, die aktiviert werden kann, um Protokolle für jeden REST-API-Aufruf zu generieren. Diese Protokolle werden in ein Speicherkonto in Ihrem Abonnement übertragen, auf das Sie die Aufbewahrungsrichtlinie anwenden, die den Anforderungen Ihrer Organisation entspricht.

Azure Key Vault-Diagnoseprotokolle, die persönliche Daten enthalten, können durch Senden einer Exportanforderung im Datenschutzportal abgerufen werden. Diese Anforderung muss vom Mandantenadministrator vorgenommen werden.

## <a name="next-steps"></a>Nächste Schritte

- [Azure-Schlüsseltresor-Protokollierung](key-vault-logging.md)

- [Übersicht über die Azure Key Vault-Funktion für vorläufiges Löschen](key-vault-soft-delete-cli.md)

- [Azure Key Vault-Vorgänge für Schlüssel](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Azure Key Vault-Vorgänge für Geheimnisse](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Azure Key Vault-Zertifikate und -Richtlinien](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Azure Key Vault-Vorgänge für Speicherkonten](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
