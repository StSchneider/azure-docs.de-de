---
title: Installieren von Visual Studio und Herstellen einer Verbindung mit Azure Stack | Microsoft-Dokumentation
description: Erfahren Sie, welche Schritte zum Installieren von Visual Studio und zum Herstellen einer Verbindung mit Azure Stack erforderlich sind.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 6bb3215e63e2fc672cde5746e6a9867df899ee88
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016697"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Installieren von Visual Studio und Herstellen einer Verbindung mit Azure Stack

*Anwendungsbereich: Integrierte Azure Stack-Systeme und Azure Stack Development Kit*

Sie können Visual Studio zum Schreiben und Bereitstellen von [Azure Resource Manager-Vorlagen](azure-stack-arm-templates.md) für Azure Stack verwenden. Die Schritte in diesem Artikel beschreiben die Installation von Visual Studio in [Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) oder auf einem externen Computer, wenn Sie eine Verbindung mit Azure Stack per [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) herstellen möchten.

## <a name="install-visual-studio"></a>Installieren von Visual Studio

1. Laden Sie den [Webplattform-Installer](https://www.microsoft.com/web/downloads/platform.aspx) herunter, und führen Sie ihn aus.  

2. Öffnen Sie den **Microsoft-Webplattform-Installer**.

3. Suchen Sie nach **Visual Studio Community 2015 with Microsoft Azure SDK – 2.9.6**. Klicken Sie auf **Hinzufügen** und anschließend auf **Installieren**.

4. Deinstallieren Sie das **Microsoft Azure PowerShell**, das als Teil des Azure SDKs installiert ist.

    ![Screenshot der WebPI-Installationsschritte](./media/azure-stack-install-visual-studio/image1.png) 

5. [Installieren von PowerShell für Azure Stack](azure-stack-powershell-install.md)

6. Starten Sie nach Abschluss der Installation das Betriebssystem neu.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Herstellen einer Verbindung mit Azure Stack über Azure AD

1. Starten Sie Visual Studio.

2. Wählen Sie im Menü **Ansicht** die Option **Cloud-Explorer** aus.

3. Wählen Sie im neuen Bereich die Option **Konto hinzufügen** aus, und melden Sie sich mit Ihren Azure Active Directory-Anmeldeinformationen (Azure AD) an.  

    ![Screenshot von Cloud-Explorer nach dem Anmelden und dem Herstellen einer Verbindung mit Azure Stack](./media/azure-stack-install-visual-studio/image2.png)

Nach der Anmeldung können Sie [Vorlagen bereitstellen](azure-stack-deploy-template-visual-studio.md) oder die verfügbaren Ressourcentypen und Ressourcengruppen durchsuchen, um Ihre eigenen Vorlagen zu erstellen.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>Herstellen einer Verbindung mit Azure Stack über AD FS

1. Starten Sie Visual Studio.

2. Wählen Sie unter **Tools** den Eintrag **Optionen** aus.

3. Erweitern Sie **Umgebung** im **Navigationsbereich**, und wählen Sie **Konten** aus.

4. Wählen Sie **Hinzufügen** aus, und geben Sie den Azure Resource Manager-Endpunkt des Benutzers ein.  
  Für das Azure Stack Development Kit lautet die URL `https://management.local.azurestack/external`.  
  Für integrierte Azure Stack-Systeme lautet die URL `https://management.[Region}.[External FQDN]`.

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. Wählen Sie **Hinzufügen**.  

    Visual Studio ruft Azure Resource Manager auf und ermittelt die Endpunkte, einschließlich des Authentifizierungsendpunkts für Active Directory-Verbunddienste (AD FS).

    ![Screenshot von Cloud-Explorer nach dem Anmelden und dem Herstellen einer Verbindung mit Azure Stack](./media/azure-stack-install-visual-studio/image6.png)

6. Wählen Sie im Menü **Ansicht** die Option **Cloud-Explorer** aus.

1. Wählen Sie **Konto hinzufügen** aus, und melden Sie sich mit Ihren AD FS-Anmeldeinformationen an.  

    ![Cloud-Explorer](./media/azure-stack-install-visual-studio/image7.png)

    Cloud-Explorer fragt die verfügbaren Abonnements ab. Sie können ein für die Verwaltung verfügbares Abonnement auswählen.

    ![Cloud-Explorer](./media/azure-stack-install-visual-studio/image8.png)

8. Durchsuchen Sie Ihre vorhandenen Ressourcen und Ressourcengruppen, oder stellen Sie Vorlagen bereit.

## <a name="next-steps"></a>Nächste Schritte

 - Erfahren Sie mehr über die [parallele Installation mehrerer Visual Studio-Versionen](https://msdn.microsoft.com/library/ms246609.aspx).
 - [Entwickeln Sie Vorlagen für Azure Stack.](azure-stack-develop-templates.md)