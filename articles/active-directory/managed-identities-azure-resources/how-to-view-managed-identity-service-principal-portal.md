---
title: Anzeigen des Dienstprinzipals einer verwalteten Identität im Azure-Portal
description: Schrittanleitungen zur Anzeige des Dienstprinzipals einer verwalteten Identität im Azure-Portal.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: c61220242a5a2ff84204c84b11aa132d00279e49
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726594"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Anzeigen des Dienstprinzipals einer verwalteten Identität im Azure-Portal

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. 

In diesem Artikel erhalten Sie Informationen zur Anzeige des Dienstprinzipals einer verwalteten Identität im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter.
- Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/free/) registrieren.
- Aktivieren Sie eine [systemseitig zugewiesene Identität auf einem virtuellen Computer](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) oder in einer [Anwendung](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Anzeigen des Dienstprinzipals

Dieses Verfahren zeigt, wie der Dienstprinzipal einer VM mit aktivierter systemseitig zugewiesener Identität angezeigt wird. (Dieselben Schritte gelten für eine Anwendung.)

1. Klicken Sie auf **Azure Active Directory** und dann auf **Unternehmensanwendungen**.
2. Wählen Sie unter **Anwendungstyp** die Option **Alle Anwendungen** aus.
3. Geben Sie im Suchfilterfeld den Namen des virtuellen Computers oder der Anwendung mit aktivierter verwalteter Identität ein, oder wählen Sie den Namen in der angezeigten Liste aus.

   ![Anzeigen eines Dienstprinzipals für verwaltete Identitäten im Portal](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Nächste Schritte

[Verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview)

