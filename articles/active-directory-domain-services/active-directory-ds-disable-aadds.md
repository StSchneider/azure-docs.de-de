---
title: Deaktivieren von Azure Active Directory Domain Services | Microsoft-Dokumentation
description: Deaktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: ergreenl
ms.openlocfilehash: 60108032e10b3281ddfc3fd468d1f0f48cf7defc
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50154877"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Deaktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals
In diesem Artikel wird gezeigt, wie Sie mit dem Azure-Portal Azure Active Directory Domain Services (AD) für Ihr Azure AD-Verzeichnis deaktivieren.

> [!WARNING]
> **Verzeichnisse werden dauerhaft gelöscht, und dieser Vorgang kann nicht rückgängig gemacht werden.**
> Lassen Sie daher Vorsicht walten. Beim Löschen der verwalteten Domäne geschieht Folgendes:
  * Die Bereitstellung der Domänencontroller für die verwaltete Domäne wird aufgehoben, und die Domänencontroller werden aus dem virtuellen Netzwerk entfernt.
  * Die Daten in der verwalteten Domäne werden endgültig gelöscht. Dazu gehören u.a. benutzerdefinierte Organisationseinheiten, GPOs, benutzerdefinierte DNS-Einträge, Dienstprinzipale und gMSAs, die Sie in der verwalteten Domäne erstellt haben.
  * Computer, die in die verwaltete Domäne eingebunden sind, verlieren ihre Vertrauensstellung mit der Domäne und müssen aus der Domäne entfernt werden.
  * Bei diesen Computern können Sie sich nicht mit Unternehmens-AD-Anmeldeinformationen anmelden. Verwenden Sie stattdessen die Anmeldeinformationen für den lokalen Administrator des Computers.
Durch das Löschen der verwalteten Domäne wird Ihr Azure AD-Verzeichnis weder gelöscht noch anderweitig beeinträchtigt.
>

Führen Sie zum Löschen der verwalteten Azure AD Domain Services-Domäne die folgenden Schritte aus:
1. Navigieren Sie im Azure-Portal zur [Azure AD Domain Services-Erweiterung](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices).
2. Klicken Sie auf den Namen der verwalteten Domäne.

    ![Auswählen der zu löschenden Domäne](./media/getting-started/domain-services-delete-select-domain.png)

3. Klicken Sie auf der Seite **Übersicht** auf die Schaltfläche **Löschen**.

    ![Löschen der Domäne](./media/getting-started/domain-services-delete-domain.png)

4. Geben Sie zum Bestätigen des Löschvorgangs den DNS-Domänennamen der verwalteten Domäne ein. Klicken Sie anschließend auf die Schaltfläche **Löschen**.

    ![Bestätigung zum Löschen der Domäne](./media/getting-started/domain-services-delete-domain-confirm.png)

Die verwaltete Domäne wird in etwa 15 bis 20 Minuten gelöscht.

Wir würden uns über [Ihr Feedback](active-directory-ds-contact-us.md) freuen, damit wir besser verstehen, welche Features Sie benötigen, um sich in Zukunft für die Azure AD Domain Services zu entscheiden. Dieses Feedback hilft uns dabei, den Dienst weiter zu verbessern und an die Anforderungen Ihrer Bereitstellungen und Anwendungsfälle anzupassen.
