---
title: Lizenzieren der Self-Service-Kennwortzurücksetzung – Azure Active Directory
description: Lizenzanforderungen für die Self-Service-Kennwortzurücksetzung in Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 6da0bddc3f6c90d0ecd3a554988f510e1063caac
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043038"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Lizenzanforderungen für Azure AD-Self-Service-Kennwortzurücksetzung

Azure Active Directory ist in vier Editionen erhältlich: Free, Basic, Premium P1 und Premium P2. Die Self-Service-Kennwortzurücksetzung setzt sich aus mehreren verschiedenen Features einschließlich Ändern, Zurücksetzen, Entsperren und Rückschreiben zusammen, die in den verschiedenen Editionen von Azure AD verfügbar sind. In diesem Artikel werden die Unterschiede erläutert. Weitere Informationen zu den in den einzelnen Editionen von Azure AD enthaltenen Features finden Sie auf der Seite [Azure Active Directory – Preise ](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Vergleich von Editionen und Features

Die Azure AD-Self-Service-Kennwortzurücksetzung wird pro Benutzer lizenziert. Aus Gründen der Kompatibilität müssen Organisationen ihren Benutzern die entsprechende Lizenz zuweisen.

* Self-Service-Kennwortänderung für Cloudbenutzer
   * Ich bin ein **reiner Cloudbenutzer** und kenne mein Kennwort.
      * Ich würde mein Kennwort gerne in etwas Neues **ändern**.
   * Diese Funktionalität ist in allen Editionen von Azure AD enthalten.

* Self-Service-Kennwortrücksetzung für Cloudbenutzer
   * Ich bin ein **reiner Cloudbenutzer** und habe mein Kennwort vergessen.
      * Ich würde mein Kennwort gerne auf etwas **zurücksetzen**, was ich kenne.
   * Diese Funktionalität ist in den Editionen Azure AD Basic, Premium P1 oder Premium P2 enthalten.

* Self-Service-Kennwortzurücksetzung/-änderung/-entsperrung **mit lokalem Rückschreiben**
   * Ich bin ein **Hybridbenutzer** – mein lokales Active Directory-Benutzerkonto ist mittels Azure AD Connect mit meinem Azure AD-Konto synchronisiert. Ich möchte mein Kennwort ändern, habe mein Kennwort vergessen oder wurde ausgesperrt.
      * Ich möchte mein Kennwort ändern oder auf etwas zurücksetzen, das mir bekannt ist, bzw. mein Konto entsperren, **und** habe diese Änderung wieder mit meinem lokalen Active Directory synchronisiert.
   * Diese Funktionalität ist in den Editionen Azure AD Premium P1 oder Premium P2 enthalten.

> [!WARNING]
> Eigenständige Office 365-Lizenzierungspläne *bieten keine Unterstützung für Self-Service-Kennwortzurücksetzung/-änderung/-entsperrung mit lokalem Rückschreiben* und erfordern einen Plan mit Azure AD Premium P1 oder Premium P2-Editionen, damit diese Funktion verwendbar ist.
>

Weitere Informationen zur Lizenzierung, einschließlich der Kosten, finden Sie auf den folgenden Seiten:

* [Website mit Preisen für Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory-Features und -Funktionen](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Aktivieren der gruppen- oder benutzerbasierten Lizenzierung

Azure AD unterstützt jetzt gruppenbasierte Lizenzierung. Administratoren können einer Gruppe von Benutzern Lizenzen in einem Massenvorgang zuweisen, anstatt sie einzeln zuzuweisen. Weitere Informationen hierzu finden Sie unter [Zuweisen, Prüfen und Beheben von Problemen mit Lizenzen](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Einige Microsoft-Dienste sind nicht an allen Standorten verfügbar. Bevor einem Benutzer eine Lizenz zugewiesen werden kann, muss der Administrator die Eigenschaft **Nutzungsspeicherort** für den Benutzer angeben. Die Zuweisung von Lizenzen erfolgt im Azure-Portal im Abschnitt **Benutzer** > **Profil** > **Einstellungen**. *Wenn Sie eine Gruppenlizenzzuweisung verwenden, erben alle Benutzer ohne Nutzungsspeicherort den Speicherort des Verzeichnisses.*

## <a name="next-steps"></a>Nächste Schritte

* [Erfolgreiches Rollout der Self-Service-Kennwortzurücksetzung](howto-sspr-deployment.md)
* [Zurücksetzen oder Ändern des Kennworts](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrieren für die Self-Service-Kennwortzurücksetzung](../user-help/active-directory-passwords-reset-register.md)
* [Bereitstellen der Kennwortzurücksetzung ohne erforderliche Endbenutzerregistrierung](howto-sspr-authenticationdata.md)
* [Authentifizierungsmethoden](concept-sspr-howitworks.md#authentication-methods)
* [Kennwortrichtlinien und -einschränkungen in Azure Active Directory](concept-sspr-policy.md)
* [Übersicht über die Kennwortrückschreibung](howto-sspr-writeback.md)
* [Berichterstellungsoptionen für die Kennwortverwaltung von Azure AD](howto-sspr-reporting.md)
* [Welche Optionen sind für SSPR verfügbar, und was bedeuten sie?](concept-sspr-howitworks.md)
* [Anscheinend ist ein Fehler aufgetreten. Wie behebe ich Probleme mit SSPR?](active-directory-passwords-troubleshoot.md)
* [Ich habe eine Frage, die nicht an einer anderen Stelle abgedeckt wurde.](active-directory-passwords-faq.md)
