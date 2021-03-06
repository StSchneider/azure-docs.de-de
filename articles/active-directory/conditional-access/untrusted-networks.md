---
title: Vorschreiben der mehrstufigen Authentifizierung (MFA) für den Zugriff von nicht vertrauenswürdigen Netzwerken mit bedingtem Zugriff von Azure Active Directory (Azure AD) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Richtlinie für bedingten Zugriff in Azure Active Directory (Azure AD) für Zugriffsversuche von nicht vertrauenswürdigen Netzwerken konfigurieren.
services: active-directory
keywords: bedingter Zugriff auf Apps, bedingter Zugriff mit Azure AD, sicherer Zugriff auf Unternehmensressourcen, Richtlinien für bedingten Zugriff
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: c40db6c253899d7aab21d277e93b23dd0c6feb97
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314005"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Gewusst wie: Vorschreiben der Verwendung der MFA für den Zugriff von nicht vertrauenswürdigen Netzwerken mit bedingtem Zugriff   

Azure Active Directory (Azure AD) ermöglicht das einmalige Anmelden bei Geräten, Apps und Diensten an jedem Ort. Benutzer können nicht nur über das Netzwerk Ihres Unternehmens, sondern auch von jeder nicht vertrauenswürdigen Internetadresse aus auf Ihre Cloud-Apps zugreifen. Eine allgemeine bewährte Methode für den Zugriff von nicht vertrauenswürdigen Netzwerken ist die Anforderung der Verwendung der mehrstufigen Authentifizierung (MFA).

Dieser Artikel enthält Informationen, die zum Konfigurieren einer Richtlinie für bedingten Zugriff erforderlich sind, die MFA für den Zugriff von nicht vertrauenswürdigen Netzwerken erfordert. 

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie mit Folgendem vertraut sind: 

- Mit den [grundlegenden Konzepten](overview.md) des bedingten Azure AD-Zugriffs 
- Mit den [Best Practices](best-practices.md) für das Konfigurieren von Richtlinien für bedingten Zugriff im Azure-Portal



## <a name="scenario-description"></a>Beschreibung des Szenarios

Um das Gleichgewicht zwischen Sicherheit und Produktivität zu gewährleisten, kann es ausreichen, dass für die Anmeldung über das Netzwerk Ihrer Organisation lediglich ein Kennwort erforderlich ist. Beim Zugriff über eine nicht vertrauenswürdige Netzwerkadresse besteht jedoch ein erhöhtes Risiko, dass die Anmeldung nicht von berechtigten Benutzern durchgeführt wird. Um dieses Risiko zu vermeiden, können Sie den Zugriff von nicht vertrauenswürdigen Netzwerken blockieren. Alternativ können Sie auch eine mehrstufige Authentifizierung (MFA) verlangen, um sicher zu sein, dass ein Versuch vom legitimen Besitzer des Kontos unternommen wurde. 

Mit dem bedingten Zugriff in Azure AD können Sie diese Anforderung mit einer einzigen Richtlinie erfüllen, die den Zugriff gewährt: 

- Für ausgewählte Cloud-Apps

- Für ausgewählte Benutzer und Gruppen  

- Mit erforderlicher mehrstufiger Authentifizierung 

- Beim Zugriff von: 

    - Einer nicht als vertrauenswürdig eingestuften Adresse


## <a name="implementation"></a>Implementierung

Die Herausforderung dieses Szenarios besteht darin, *den Zugriff von einem nicht vertrauenswürdigen Netzwerk* in eine Bedingung für den bedingten Zugriff umzuwandeln. In einer Richtlinie für bedingten Zugriff können Sie die [Standortbedingung so konfigurieren](location-condition.md), dass sie Szenarien adressiert, die sich auf Netzwerkadressen beziehen. Die Standortbedingung ermöglicht es Ihnen, benannte Standorte auszuwählen, die logische Gruppierungen von IP-Adressbereichen, Ländern und Regionen sind.  

Typischerweise besitzt Ihre Organisation einen oder mehrere Adressbereiche, z.B. 199.30.16.0 bis 199.30.16.24.
Sie können einen benannten Speicherort wie folgt konfigurieren:

- Festlegen dieses Bereichs (199.30.16.0/24) 

- Zuweisen eines beschreibenden Namens wie **Unternehmensnetzwerk** 


Anstatt zu versuchen, alle Standorte zu definieren, die nicht vertrauenswürdig sind, können Sie:

- Alle Standorte einschließen 

    ![Bedingter Zugriff](./media/untrusted-networks/02.png)

- Alle vertrauenswürdigen Standorte ausschließen 

    ![Bedingter Zugriff](./media/untrusted-networks/01.png)



## <a name="policy-deployment"></a>Richtlinienbereitstellung

Mit dem in diesem Artikel beschriebenen Ansatz können Sie nun eine Richtlinie für den bedingten Zugriff für nicht vertrauenswürdige Standorte konfigurieren. Um die erwartete Funktionsweise der Richtlinie sicherzustellen, empfiehlt es sich, sie zu testen, bevor Sie sie in der Produktionsumgebung verwenden. Idealerweise sollten Sie in einem Testmandanten überprüfen, ob die neue Richtlinie wie erwartet funktioniert. Weitere Informationen finden Sie unter [Wie stellen Sie eine neue Richtlinie bereit?](best-practices.md#how-should-you-deploy-a-new-policy) 



## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum bedingten Zugriff finden Sie unter [Was ist bedingter Zugriff in Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)