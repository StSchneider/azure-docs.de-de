---
title: 'Tutorial: Azure Active Directory-Integration mit Kantega SSO for Bitbucket | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Kantega SSO for Bitbucket konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 6dee106d688d9f9a6ebc6dc26caa6a46db3f6850
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51823893"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Tutorial: Azure Active Directory-Integration mit Kantega SSO for Bitbucket

In diesem Tutorial erfahren Sie, wie Sie Kantega SSO for Bitbucket in Azure Active Directory (Azure AD) integrieren.

Die Integration von Kantega SSO for Bitbucket in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Kantega SSO for Bitbucket hat.
- Sie können Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Kantega SSO for Bitbucket anzumelden (Sigle Sign-On, SSO; einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren der Azure AD-Integration mit Kantega SSO for Bitbucket benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Kantega SSO for Bitbucket-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/)eine einmonatige Testversion anfordern.

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von Kantega SSO for Bitbucket aus dem Katalog
1. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Hinzufügen von Kantega SSO for Bitbucket aus dem Katalog
Zum Konfigurieren der Integration von Kantega SSO for Bitbucket in Azure AD müssen Sie Kantega SSO for Bitbucket aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Kantega SSO for Bitbucket aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

1. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![ANWENDUNGEN][2]
    
1. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![ANWENDUNGEN][3]

1. Geben Sie in das Suchfeld den Suchbegriff **Kantega SSO for Bitbucket** ein.

    ![Erstellen eines Azure AD-Testbenutzers](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_search.png)

1. Wählen Sie im Ergebnisbereich **Kantega SSO for Bitbucket** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Erstellen eines Azure AD-Testbenutzers](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Kantega SSO for Bitbucket basierend auf einer Testbenutzerin mit dem Namen „Britta Simon“.

Damit das einmalige Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Kantega SSO for Bitbucket als Entsprechung zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Kantega SSO for Bitbucket muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in Kantega SSO for Bitbucket den Wert für **Benutzername** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Kantega SSO for Bitbucket müssen Sie die folgenden Schritte ausführen:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
1. **[Erstellen eines Kantega SSO for Bitbucket-Testbenutzers](#creating-a-kantega-sso-for-bitbucket-test-user)**, um eine Entsprechung von Britta Simon in Kantega SSO for Bitbucket zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Testing Single Sign-On](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer Kantega SSO for Bitbucket-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Kantega SSO for Bitbucket die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Kantega SSO for Bitbucket** auf **Einmaliges Anmelden**.

    ![Configure single sign-on][4]

1. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_samlbase.png)

1. Führen Sie im **IDP**-initiierten Modus im Abschnitt **Domäne und URLs für Kantega SSO for Bitbucket** den folgenden Schritt aus:

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url1.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL nach folgendem Muster ein: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

1. Aktivieren Sie im **SP**-initiierten Modus die Option **Erweiterte URL-Einstellungen anzeigen**, und führen Sie den folgenden Schritt aus:

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url2.png)
    
    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`.

    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner, die Antwort-URL und die Anmelde-URL. Diese Werte werden während der Konfiguration des Bitbucket-Plug-Ins empfangen, die später im Tutorial beschrieben wird.

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Metadaten-XML**, und speichern Sie die Metadatendatei dann auf Ihrem Computer.

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_certificate.png) 

1. Klicken Sie auf die Schaltfläche **Save** .

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/tutorial_general_400.png)

1. Melden Sie sich in einem anderen Webbrowserfenster im Bitbucket-Verwaltungsportal als Administrator an.

1. Klicken Sie auf das Zahnrad und dann auf **Find new add-ons** (Nach neuen Add-Ons suchen).

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. Suchen Sie nach **Kantega SSO for Bitbucket SAML & Kerberos**, und klicken Sie auf die Schaltfläche **Installieren**, um das neue SAML-Plug-In zu installieren.

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. Die Installation des Plug-Ins wird gestartet.

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. Gehen Sie nach Abschluss der Installation wie folgt vor: Klicken Sie auf **Schließen**.

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon33.png)

1.  Klicken Sie auf **Manage**.

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon34.png)
    
1. Klicken Sie auf **Konfigurieren**, um das neue Plug-In zu konfigurieren. 

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. Im Abschnitt **SAML**: Wählen Sie in der Dropdownliste **Identitätsanbieter hinzufügen** die Option **Azure Active Directory (Azure AD)**.

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. Wählen Sie als Abonnementebene die Option **Basic**.

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. Führen Sie im Abschnitt **App-Eigenschaften** die folgenden Schritte aus:

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Kopieren Sie den Wert für den **App-ID-URI**, und verwenden Sie ihn als **Bezeichner, Antwort-URL und Anmelde-URL** im Abschnitt **Domäne und URLs für Kantega SSO for Bitbucket** des Azure-Portals.

    b. Klicken Sie auf **Weiter**.

1. Führen Sie im Abschnitt **Metadata import** (Metadatenimport) die folgenden Schritte aus:

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Wählen Sie **Metadata file on my computer** (Metadatendatei auf meinem Computer), und laden Sie die Metadatendatei hoch, die Sie aus dem Azure-Portal heruntergeladen haben.

    b. Klicken Sie auf **Weiter**.

1. Führen Sie im Abschnitt **Name and SSO location** (Name und SSO-Standort) die folgenden Schritte aus:

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. Fügen Sie im Textfeld **Name des Identitätsanbieters** den Namen des Identitätsanbieters hinzu (z.B. Azure AD).

    b. Klicken Sie auf **Weiter**.

1. Überprüfen Sie das Signaturzertifikat, und klicken Sie auf **Weiter**.   

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. Führen Sie im Abschnitt **Bitbucket user accounts** (Bitbucket-Benutzerkonten) die folgenden Schritte aus:

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. Wählen Sie **Create users in Bitbucket's internal Directory if needed** (Benutzer im internen Bitbucket-Verzeichnis erstellen, falls erforderlich), und geben Sie den entsprechenden Namen der Gruppe für Benutzer ein (können mehrere durch Kommas getrennte Gruppen sein).

    b. Klicken Sie auf **Weiter**.

1. Klicken Sie auf **Fertig stellen**.

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. Führen Sie im Abschnitt **Known domains for Azure AD** (Bekannte Domänen für Azure AD) die folgenden Schritte aus:  

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. Wählen Sie im linken Bereich der Seite die Option **Known domains** (Bekannte Domänen).

    b. Geben Sie den Domänennamen im Textfeld **Known domains** (Bekannte Domänen) ein.

    c. Klicken Sie auf **Speichern**.  

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Eingebettete Azure AD-Dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Portals** auf das Symbol für **Azure Active Directory**.

    ![Erstellen eines Azure AD-Testbenutzers](./media/kantegassoforbitbucket-tutorial/create_aaduser_01.png) 

1. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Erstellen eines Azure AD-Testbenutzers](./media/kantegassoforbitbucket-tutorial/create_aaduser_02.png) 

1. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/kantegassoforbitbucket-tutorial/create_aaduser_03.png) 

1. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/kantegassoforbitbucket-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Create**.
 
### <a name="creating-a-kantega-sso-for-bitbucket-test-user"></a>Erstellen eines Kantega SSO for Bitbucket-Testbenutzers

Damit sich Azure AD-Benutzer bei Bitbucket anmelden können, müssen sie in Bitbucket bereitgestellt werden. In Kantega SSO for Bitbucket ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der Bitbucket-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf das Symbol „Einstellungen“.

    ![Mitarbeiter hinzufügen](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. Klicken Sie im Registerkartenabschnitt **Verwaltung** auf **Benutzer**.

    ![Mitarbeiter hinzufügen](./media/kantegassoforbitbucket-tutorial/user2.png)

1. Klicken Sie auf **Benutzer erstellen**.

    ![Mitarbeiter hinzufügen](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. Führen Sie auf der Dialogfeldseite **Benutzer erstellen** die folgenden Schritte aus:

    ![Mitarbeiter hinzufügen](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. Geben Sie im Textfeld **Benutzername** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.
    
    b. Geben Sie im Textfeld **Vollständiger Name** den vollständigen Namen des Benutzers, z.B. „Britta Simon“, ein.
    
    c. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.

    d. Geben Sie im Textfeld **Kennwort** das Kennwort des Benutzers ein.  

    e. Geben Sie im Textfeld **Kennwort bestätigen** das Kennwort des Benutzers erneut ein.

    f. Klicken Sie auf **Benutzer erstellen**.   

### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Kantega SSO for Bitbucket gewähren.

![Benutzer zuweisen][200] 

**Führen Sie die folgenden Schritte aus, um Britta Simon Kantega SSO for Bitbucket zuzuweisen:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

1. Wählen Sie in der Anwendungsliste **Kantega SSO for Bitbucket** aus.

    ![Configure single sign-on](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_app.png) 

1. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Benutzer zuweisen][202] 

1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer zuweisen][203]

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

1. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Kantega SSO for Bitbucket“ klicken, sollten Sie automatisch bei Ihrer Kantega SSO for Bitbucket-Anwendung angemeldet werden.
Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_01.png
[2]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_02.png
[3]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_03.png
[4]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_04.png

[100]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_100.png

[200]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_200.png
[201]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_201.png
[202]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_202.png
[203]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_203.png

