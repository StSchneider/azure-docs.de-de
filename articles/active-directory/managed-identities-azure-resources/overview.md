---
title: Verwaltete Identitäten für Azure-Ressourcen
description: Eine Übersicht über verwaltete Identitäten für Azure-Ressourcen.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 10/23/2018
ms.author: daveba
ms.openlocfilehash: 3b9c8ea3dba5fdbd3e25293873ab1ae6854b3b83
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728107"
---
# <a name="what-is-managed-identities-for-azure-resources"></a>Was sind verwaltete Identitäten für Azure-Ressourcen?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Eine gängige Herausforderung beim Erstellen von Cloudanwendungen ist die Verwaltung der Anmeldeinformationen im Code, die für die Authentifizierung bei Clouddiensten erforderlich sind. Der Schutz dieser Anmeldeinformationen ist eine wichtige Aufgabe. Im Idealfall werden die Anmeldeinformationen nie auf Entwicklerarbeitsstationen angezeigt und auch nicht in die Quellcodeverwaltung eingecheckt. Azure Key Vault bietet eine Möglichkeit zum sicheren Speichern von Anmeldeinformationen, Geheimnissen und anderen Schlüsseln. Um diese abrufen zu können, muss sich Ihr Code jedoch bei Key Vault authentifizieren. 

Die Funktion für verwaltete Identitäten für Azure-Ressourcen in Azure Active Directory (Azure AD) löst dieses Problem. Dieses Feature stellt für Azure-Dienste eine automatisch verwaltete Identität in Azure AD bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

Die Funktion für verwaltete Identitäten für Azure-Ressourcen ist bei Azure AD mit einem Azure-Abonnement kostenlos. Es fallen keine zusätzlichen Kosten an.

> [!NOTE]
> „Verwaltete Identitäten für Azure-Ressourcen“ ist der neue Name für den Dienst, der früher als „Verwaltete Dienstidentität“ (Managed Service Identity, MSI) bezeichnet wurde.

## <a name="terminology"></a>Begriff

Die folgenden Begriffe werden in den verwalteten Identitäten für den Azure-Ressourcendokumentationssatz verwendet:

- **Client-ID**: Ein eindeutiger, vom Azure AD generierter Bezeichner, der während der ersten Bereitstellung an eine Anwendung und einen Dienstprinzipal gebunden ist.
- **Prinzipal-ID**: die Objekt-ID des Dienstprinzipalobjekts für Ihre verwaltete Identität, die zum Gewähren des rollenbasierten Zugriffs auf eine Azure-Ressource verwendet wird.
- Der **Azure Instance Metadata Service (IMDS)** ist ein REST-Endpunkt, der für alle virtuellen IaaS-Computer verfügbar ist, die mit Azure Resource Manager erstellt wurden. Der Endpunkt steht unter einer bekannten, nicht routingfähigen IP-Adresse zur Verfügung (169.254.169.254), auf die nur von innerhalb der VM zugegriffen werden kann.

## Wie funktionieren die verwalteten Identitäten für Azure-Ressourcen?<a name="how-does-it-work"></a>

Es gibt zwei Arten von verwalteten Identitäten:

- Eine **vom System zugewiesene verwaltete Identität** wird direkt für eine Azure-Dienstinstanz aktiviert. Wenn die Identität aktiviert ist, erstellt Azure eine Identität für die Instanz in dem Azure AD-Mandanten, der vom Abonnement der Instanz als vertrauenswürdig eingestuft wird. Nach dem Erstellen der Identität werden die Anmeldeinformationen in der Instanz bereitgestellt. Der Lebenszyklus einer vom System zugewiesenen Identität ist direkt an die Azure-Dienstinstanz gebunden, für die sie aktiviert wurde. Wenn die Instanz gelöscht wird, bereinigt Azure automatisch die Anmeldeinformationen und die Identität in Azure AD.
- Eine **vom Benutzer zugewiesene verwaltete Identität** wird als eigenständige Azure-Ressource erstellt. Azure erstellt eine Identität in dem Azure AD-Mandanten, der vom verwendeten Abonnement als vertrauenswürdig eingestuft wird. Nachdem die Identität erstellt wurde, kann sie einer oder mehreren Azure-Dienstinstanzen zugewiesen werden. Der Lebenszyklus einer vom Benutzer zugewiesenen Identität wird getrennt vom Lebenszyklus der Azure-Dienstinstanzen verwaltet, denen sie zugewiesen ist.

Ihr Code kann eine verwaltete Identität zum Anfordern von Zugriffstokens für Dienste verwenden, die die Azure AD-Authentifizierung unterstützen. Azure übernimmt die Weitergabe der von der Dienstinstanz verwendeten Anmeldeinformationen.

Das folgende Diagramm zeigt, wie verwaltete Dienstidentitäten mit virtuellen Azure-Computern funktionieren:

![Verwaltete Dienstidentitäten und Azure-VMs](media/overview/msi-vm-vmextension-imds-example.png)

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>Funktionsweise einer vom System zugewiesenen verwalteten Identität mit einer Azure-VM

1. Azure Resource Manager empfängt eine Anforderung zur Aktivierung der vom System zugewiesenen verwalteten Identität auf einer VM.
2. Azure Resource Manager erstellt einen Dienstprinzipal in Azure AD für die Identität des virtuellen Computers. Der Dienstprinzipal wird in dem Azure AD-Mandanten erstellt, der von diesem Abonnement als vertrauenswürdig eingestuft wird.
3. Azure Resource Manager konfiguriert die Identität auf dem virtuellen Computer:
    1. Aktualisiert den Azure Instance Metadata Service-Identitätsendpunkt mit der Client-ID und dem Zertifikat des Dienstprinzipals.
    1. Stellt die VM-Erweiterung bereit (geplante Einstufung als veraltet im Januar 2019) und fügt die Client-ID und das Zertifikat des Dienstprinzipals hinzu. (Wird in Kürze als veraltet eingestuft.)
4. Wenn der virtuelle Computer über eine Identität verfügt, verwenden wir die Dienstprinzipalinformationen, um ihm Zugriff auf Azure-Ressourcen zu gewähren. Verwenden Sie zum Aufrufen von Azure Resource Manager die rollenbasierte Zugriffssteuerung (RBAC) in Azure AD, um dem VM-Dienstprinzipal die entsprechende Rolle zuzuweisen. Gewähren Sie Ihrem Code zum Aufrufen von Key Vault Zugriff auf das spezifische Geheimnis oder den spezifischen Schlüssel in Key Vault.
5. Der auf dem virtuellen Computer ausgeführte Code kann ein Token von zwei Endpunkten anfordern, auf die nur von dem virtuellen Computer aus zugegriffen werden kann:

    - Identitätsendpunkt von Azure Instance Metadata Service (empfohlen): `http://169.254.169.254/metadata/identity/oauth2/token`
        - Der Ressourcenparameter gibt den Dienst an, an den das Token gesendet wird. Verwenden Sie `resource=https://management.azure.com/` für die Authentifizierung bei Azure Resource Manager.
        - Der API-Versionsparameter gibt die IMDS-Version an. Verwenden Sie mindestens „api-version=2018-02-01“.
    - VM-Erweiterungsendpunkt (geplante Einstufung als veraltet im Januar 2019): `http://localhost:50342/oauth2/token` 
        - Der Ressourcenparameter gibt den Dienst an, an den das Token gesendet wird. Verwenden Sie `resource=https://management.azure.com/` für die Authentifizierung bei Azure Resource Manager.

6. Mit einem an Azure AD gerichteten Aufruf wird ein Zugriffstoken angefordert (siehe Schritt 5). Dabei werden die Client-ID und das Zertifikat verwendet, die in Schritt 3 konfiguriert wurden. Azure AD gibt ein JWT-Zugriffstoken (JSON Web Token) zurück.
7. Der Code sendet das Zugriffstoken in einem Aufruf eines Diensts, der die Azure AD-Authentifizierung unterstützt.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>Funktionsweise einer vom Benutzer zugewiesenen verwalteten Identität mit einer Azure-VM

1. Azure Resource Manager empfängt eine Anforderung zur Erstellung einer vom Benutzer zugewiesenen verwalteten Identität.
2. Azure Resource Manager erstellt einen Dienstprinzipal in Azure AD für die vom Benutzer zugewiesene verwaltete Identität. Der Dienstprinzipal wird in dem Azure AD-Mandanten erstellt, der von diesem Abonnement als vertrauenswürdig eingestuft wird.
3. Azure Resource Manager empfängt eine Anforderung zur Konfiguration der vom Benutzer zugewiesenen verwalteten Identität auf einer VM:
    1. Er aktualisiert den Azure Instance Metadata Service-Identitätsendpunkt mit der Client-ID und dem Zertifikat des Dienstprinzipals der vom Benutzer zugewiesenen verwalteten Identität.
    1. Er stellt die VM-Erweiterung bereit und fügt die Client-ID und das Zertifikat des Dienstprinzipals der vom Benutzer zugewiesenen verwalteten Identität hinzu. (Wird in Kürze als veraltet eingestuft.)
4. Nachdem die vom Benutzer zugewiesene verwaltete Identität erstellt wurde, werden die Dienstprinzipalinformationen verwendet, um der Identität Zugriff auf Azure-Ressourcen zu gewähren. Verwenden Sie zum Aufrufen von Azure Resource Manager die RBAC in Azure AD, um dem Dienstprinzipal der vom Benutzer zugewiesenen Identität die entsprechende Rolle zuzuweisen. Gewähren Sie Ihrem Code zum Aufrufen von Key Vault Zugriff auf das spezifische Geheimnis oder den spezifischen Schlüssel in Key Vault.

   > [!Note]
   > Sie können diesen Schritt auch vor Schritt 3 ausführen.

5. Der auf dem virtuellen Computer ausgeführte Code kann ein Token von zwei Endpunkten anfordern, auf die nur von dem virtuellen Computer aus zugegriffen werden kann:

    - Identitätsendpunkt von Azure Instance Metadata Service (empfohlen): `http://169.254.169.254/metadata/identity/oauth2/token`
        - Der Ressourcenparameter gibt den Dienst an, an den das Token gesendet wird. Verwenden Sie `resource=https://management.azure.com/` für die Authentifizierung bei Azure Resource Manager.
        - Der Client-ID-Parameter gibt die Identität an, für die das Token angefordert wird. Dieser Wert ist erforderlich, um Mehrdeutigkeiten zu vermeiden, wenn auf einer einzelnen VM mehrere vom Benutzer zugewiesene Identitäten vorhanden sind.
        - Der API-Versionsparameter gibt die Azure Instance Metadata Service-Version an. Verwenden Sie `api-version=2018-02-01` oder höher.

    - VM-Erweiterungsendpunkt (geplante Einstufung als veraltet im Januar 2019): `http://localhost:50342/oauth2/token`
        - Der Ressourcenparameter gibt den Dienst an, an den das Token gesendet wird. Verwenden Sie `resource=https://management.azure.com/` für die Authentifizierung bei Azure Resource Manager.
        - Der Client-ID-Parameter gibt die Identität an, für die das Token angefordert wird. Dieser Wert ist erforderlich, um Mehrdeutigkeiten zu vermeiden, wenn auf einer einzelnen VM mehrere vom Benutzer zugewiesene Identitäten vorhanden sind.
6. Mit einem an Azure AD gerichteten Aufruf wird ein Zugriffstoken angefordert (siehe Schritt 5). Dabei werden die Client-ID und das Zertifikat verwendet, die in Schritt 3 konfiguriert wurden. Azure AD gibt ein JWT-Zugriffstoken (JSON Web Token) zurück.
7. Der Code sendet das Zugriffstoken in einem Aufruf eines Diensts, der die Azure AD-Authentifizierung unterstützt.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Wie kann ich verwaltete Identitäten für Azure-Ressourcen verwenden?

In den folgenden Tutorials erfahren Sie mehr darüber, wie Sie verwaltete Identitäten für den Zugriff auf verschiedene Azure-Ressourcen verwenden können.

Erfahren Sie, wie Sie eine verwaltete Identität mit einer Windows-VM verwenden:

* [Zugreifen auf Azure Data Lake Store](tutorial-windows-vm-access-datalake.md)
* [Zugreifen auf Azure Resource Manager](tutorial-windows-vm-access-arm.md)
* [Zugreifen auf Azure SQL](tutorial-windows-vm-access-sql.md)
* [Zugreifen auf Azure Storage mithilfe eines Zugriffsschlüssels](tutorial-windows-vm-access-storage.md)
* [Zugreifen auf Azure Storage mithilfe von SAS-Anmeldeinformationen](tutorial-windows-vm-access-storage-sas.md)
* [Zugreifen auf eine Nicht-Azure AD-Ressource mithilfe von Azure Key Vault](tutorial-windows-vm-access-nonaad.md)

Erfahren Sie, wie Sie eine verwaltete Identität mit einer Linux-VM verwenden:

* [Zugreifen auf Azure Data Lake Store](tutorial-linux-vm-access-datalake.md)
* [Zugreifen auf Azure Resource Manager](tutorial-linux-vm-access-arm.md)
* [Zugreifen auf Azure Storage mithilfe eines Zugriffsschlüssels](tutorial-linux-vm-access-storage.md)
* [Zugreifen auf Azure Storage mithilfe von SAS-Anmeldeinformationen](tutorial-linux-vm-access-storage-sas.md)
* [Zugreifen auf eine Nicht-Azure AD-Ressource mithilfe von Azure Key Vault](tutorial-linux-vm-access-nonaad.md)

Erfahren Sie, wie Sie eine verwaltete Identität mit anderen Azure-Diensten verwenden:

* [Azure App Service](/azure/app-service/overview-managed-identity)
* [Azure-Funktionen](/azure/app-service/overview-managed-identity)
* [Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md)
* [Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Azure Container Instances](../../container-instances/container-instances-managed-identity.md)

## Welche Azure-Dienste unterstützen das Feature?<a name="which-azure-services-support-managed-identity"></a>

Verwaltete Identitäten für Azure-Ressourcen können zur Authentifizierung bei Diensten verwendet werden, die die Azure AD-Authentifizierung unterstützen. Eine Liste der Azure-Dienste, die die Funktion für verwaltete Identitäten für Azure-Ressourcen unterstützen, finden Sie unter [Services that support managed identities for Azure resources](services-support-msi.md) (Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen).

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit der Funktion für verwaltete Identitäten für Azure-Ressourcen finden Sie in den folgenden Schnellstartanleitungen:

* [Use a Windows VM system-assigned managed identity to access Resource Manager](tutorial-windows-vm-access-arm.md) (Verwenden der systemseitig zugewiesenen verwalteten Identität einer Windows-VM für den Zugriff auf Resource Manager)
* [Use a Linux VM system-assigned managed identity to access Resource Manager](tutorial-linux-vm-access-arm.md) (Verwenden der systemseitig zugewiesenen verwalteten Identität einer Linux-VM für den Zugriff auf Resource Manager)
