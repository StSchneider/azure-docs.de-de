---
title: 'Tutorial: Konfigurieren von Workday für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Workday konfigurieren.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/18/2018
ms.author: chmutali
ms.openlocfilehash: 754c3278cb01e010718fa4d3cb257acf6ffe99c9
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849852"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning-preview"></a>Tutorial: Konfigurieren von Workday für die automatische Benutzerbereitstellung (Vorschau)

In diesem Tutorial werden die Schritte vorgestellt, die Sie zum Importieren von Mitarbeiterprofilen aus Workday in sowohl Active Directory als auch Azure Active Directory ausführen müssen, wobei einige E-Mail-Adressen optional in Workday zurückgeschrieben werden.

## <a name="overview"></a>Übersicht

Der [Azure Active Directory-Benutzerbereitstellungsdienst](../manage-apps/user-provisioning.md) ist zum Bereitstellen von Benutzerkonten mit der [Workday Human Resources-API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) integriert. Azure AD nutzt diese Verbindung zum Ermöglichen der folgenden Workflows für die Benutzerbereitstellung:

* **Bereitstellung von Benutzern in Active Directory:** Sie können ausgewählte Gruppen von Benutzern aus Workday in eine oder mehrere Active Directory-Domänen synchronisieren.

* **Bereitstellung reiner Cloudbenutzer in Azure Active Directory**: In Szenarien, in denen kein lokales Active Directory verwendet wird, können Benutzer mithilfe des Azure AD-Benutzerbereitstellungsdiensts direkt aus Workday in Azure Active Directory bereitgestellt werden. 

* **Zurückschreiben von E-Mail-Adressen in Workday**: Der Azure AD-Benutzerbereitstellungsdienst kann die E-Mail-Adressen von Azure AD-Benutzern in Workday zurückschreiben. 

### <a name="what-human-resources-scenarios-does-it-cover"></a>Welche Szenarien im Personalwesen werden unterstützt?

Die vom Azure AD-Benutzerbereitstellungsdienst unterstützten Workday-Workflows zur Benutzerbereitstellung ermöglichen die Automatisierung der folgenden Szenarien im Personalwesen und bei der Verwaltung des Lebenszyklus von Identitäten:

* **Einstellung neuer Mitarbeiter**: Wenn Workday ein neuer Mitarbeiter hinzugefügt wird, wird in Active Directory, Azure Active Directory und optional Office 365 sowie [anderen von Azure AD unterstützten SaaS-Anwendungen](../manage-apps/user-provisioning.md) automatisch ein Benutzerkonto erstellt. Die E-Mail-Adresse wird dabei in Workday zurückgeschrieben.

* **Aktualisierungen von Mitarbeiterattributen und -profil**: Wenn ein Mitarbeiterdatensatz in Workday aktualisiert wird (wie z.B. der Name, Titel oder Vorgesetzte), wird das entsprechende Benutzerkonto in Active Directory, Azure Active Directory und optional Office 365 und [anderen von Azure AD unterstützten SaaS-Anwendungen](../manage-apps/user-provisioning.md) automatisch aktualisiert.

* **Kündigungen von Mitarbeitern** : Wenn einem Mitarbeiter in Workday gekündigt wird, wird das entsprechende Benutzerkonto in Active Directory, Azure Active Directory und optional Office 365 und [anderen von Azure AD unterstützten SaaS-Anwendungen](../manage-apps/user-provisioning.md) automatisch deaktiviert.

* **Erneute Einstellung von Mitarbeitern**: Wenn ein Mitarbeiter in Workday erneut eingestellt wird, kann sein altes Konto in Active Directory, Azure Active Directory und optional Office 365 und [anderen von Azure AD unterstützten SaaS-Anwendungen](../manage-apps/user-provisioning.md) automatisch reaktiviert oder erneut bereitgestellt werden (je nachdem, was Sie bevorzugen).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Für wen ist diese Benutzerbereitstellungslösung am besten geeignet?

Diese Workday-Benutzerbereitstellungslösung ist derzeit als öffentliche Vorschauversion verfügbar und eignet sich ideal für:

* Organisationen, die eine vorgefertigte, cloudbasierte Lösung für die Workday-Benutzerbereitstellung verwenden möchten

* Organisationen, bei denen Benutzer direkt aus Workday in Active Directory oder Azure Active Directory bereitgestellt werden müssen

* Organisationen, bei denen Benutzer mithilfe von Daten bereitgestellt werden müssen, die aus dem HCM-Modul von Workday abgerufen werden (siehe [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisationen, bei denen Benutzer beim Beitreten, Verschieben und Verlassen nur auf Grundlage von Änderungsinformationen, die im HCM-Modul von Workday erkannt werden, mit einer oder mehreren Active Directory-Gesamtstrukturen, -Domänen und -Organisationseinheiten synchronisiert werden müssen (siehe [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisationen, die Office 365 für E-Mail verwenden

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="solution-architecture"></a>Lösungsarchitektur

In diesem Abschnitt wird die Lösungsarchitektur der End-to-End-Benutzerbereitstellung für häufige Hybridumgebungen beschrieben. Es gibt zwei zugehörige Flows:

* **Autoritativer Personaldatenfluss – von Workday in ein lokales Active Directory-Verzeichnis**: In diesem Flow treten mitarbeiterbezogene Ereignisse (z.B. Neueinstellungen, Wechsel, Kündigungen) zuerst im Cloud-HR-Mandanten von Workday auf und werden dann über Azure AD und den Bereitstellungs-Agent in ein lokales Active Directory-Verzeichnis übertragen. Abhängig vom Ereignis kann dies dann in Active Directory zu Erstellungs-, Aktualisierungs-, Aktivierungs- oder Deaktivierungsvorgängen führen.
* **E-Mail-Rückschreibedatenfluss – vom lokalen Active Directory-Verzeichnis zu Workday**: Nach Abschluss der Kontoerstellung in Active Directory wird dieses über Azure AD Connect mit Azure AD synchronisiert. Anschließend kann das E-Mail-Attribut aus Active Directory zurück in Workday geschrieben werden.

![Übersicht](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>End-to-End-Benutzerdatenfluss

1. Das Team der Personalabteilung führt Mitarbeitertransaktionen (Einstellungen/Wechsel/Kündigungen) in Workday HCM aus.
2. Der Azure AD-Bereitstellungsdienst führt geplante Synchronisierungen von Identitäten aus Workday HR aus und ermittelt Änderungen, die für eine Synchronisierung mit dem lokalen Active Directory verarbeitet werden müssen.
3. Der Azure AD-Bereitstellungsdienst ruft den lokalen AAD Connect-Bereitstellungs-Agent mit einer Anforderungsnutzlast auf, die die Erstellungs-, Aktualisierungs-, Aktivierungs- oder Deaktivierungsvorgänge für das AD-Konto enthält.
4. Der Azure AD Connect-Bereitstellungs-Agent verwendet ein Dienstkonto zum Hinzufügen/Aktualisieren von AD-Kontodaten.
5. Die Azure AD Connect-/AAD Sync-Engine führt eine Deltasynchronisierung aus, um Updates in Active Directory zu pullen.
6. Die Active Directory-Updates werden mit Azure Active Directory synchronisiert.
7. Wenn der Workday Writeback-Connector konfiguriert ist, schreibt er das E-Mail-Attribut zurück in Workday, wenn das verwendete Attribut übereinstimmt.


## <a name="planning-your-deployment"></a>Planen der Bereitstellung

Überprüfen Sie vor Beginn der Workday-Integration die folgenden Voraussetzungen, und lesen Sie die folgende Anleitung zum Erfüllen Ihrer aktuellen Anforderungen an die Active Directory-Architektur und -Benutzerbereitstellung mithilfe der von Azure Active Directory gebotenen Lösungen.

### <a name="prerequisites"></a>Voraussetzungen

Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Gültiges Azure AD Premium P1-Abonnement mit globalem Administratorzugriff
* Workday-Implementierungsmandant für Test- und Integrationszwecke
* Administratorberechtigungen in Workday zum Erstellen eines Systemintegrationsbenutzers für Testzwecke und Vornehmen von Änderungen zum Testen von Mitarbeiterdaten
* Für die Benutzerbereitstellung in Active Directory einen Server mit mindestens Windows Server 2012 und der .NET-Runtime 4.7 zum Hosten des [lokalen Bereitstellungs-Agents](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) für die Synchronisierung zwischen Active Directory und Azure AD

### <a name="planning-considerations"></a>Überlegungen zur Planung

Azure AD bietet einen umfangreichen Satz von Bereitstellungsconnectors, die Sie bei der Bereitstellung und der Verwaltung des Identitätslebenszyklus zwischen Workday und Active Directory, Azure AD, SaaS-Apps usw. unterstützen. Die zu verwendenden Features und die Einrichtung der Lösung variieren abhängig von der Umgebung und den Anforderungen Ihrer Organisation. Führen Sie als ersten Schritt eine Bestandsaufnahme durch, welche der folgenden Elemente in Ihrer Organisation vorhanden und bereitgestellt sind:

* Wie viele Active Directory-Gesamtstrukturen werden verwendet?
* Wie viele Active Directory-Domänen werden verwendet?
* Wie viele Active Directory-Organisationseinheiten (OUs) werden verwendet?
* Wie viele Azure Active Directory-Mandanten werden verwendet?
* Gibt es Benutzer, die sowohl in Active Directory als auch in Azure Active Directory bereitgestellt werden müssen (z.B. als „hybride“ Benutzer)?
* Gibt es Benutzer, die zwar in Azure Active Directory, aber nicht in Active Directory bereitgestellt werden müssen (z.B. „reine Cloudbenutzer“)?
* Müssen E-Mail-Adressen von Benutzern in Workday zurückgeschrieben werden?

Sobald Sie die Antworten auf diese Fragen kennen, können Sie Ihre Workday-Bereitstellung gemäß der folgenden Anleitung planen.

#### <a name="planning-deployment-of-aad-connect-provisioning-agent"></a>Planen der Bereitstellung des AAD Connect-Bereitstellungs-Agents

Die Lösung für die Benutzerbereitstellung von Workday zu AD erfordert das Bereitstellen mindestens eines Bereitstellungs-Agents auf Servern unter Windows 2012 R2 oder höher mit mindestens 4 GB RAM und der .NET-Runtime 4.7 oder höher. Die folgenden Aspekte müssen vor der Installation des Bereitstellungs-Agents berücksichtigt werden:

* Stellen Sie sicher, dass der Hostserver, auf dem der Bereitstellungs-Agent ausgeführt wird, Netzwerkzugriff auf die AD-Zieldomäne hat.
* Der Konfigurations-Assistent für den Bereitstellungs-Agent registriert den Agent bei Ihrem Azure AD-Mandanten. Für den Registrierungsprozess ist Zugriff auf *.msappproxy.net über Port 8082 notwendig. Stellen Sie sicher, dass die Firewallregeln für ausgehenden Datenverkehr diese Kommunikation erlauben.
* Der Bereitstellungs-Agent verwendet ein Dienstkonto für die Kommunikation mit den lokalen AD-Domänen. Vor der Installation des Agents empfiehlt es sich, ein Dienstkonto mit Lese-/Schreibberechtigungen für die Benutzereigenschaften und einem Kennwort, das nicht abläuft, zu erstellen.  
* Sie können während der Konfiguration des Bereitstellungs-Agents Domänencontroller auswählen, die Bereitstellungsanforderungen verarbeiten sollen. Wenn Sie über mehrere geografisch verteilte Domänencontroller verfügen, Installieren Sie den Bereitstellungs-Agent am selben Standort wie Ihre bevorzugten Domänencontroller. Damit steigern Sie die Zuverlässigkeit und Leistung der End-to-End-Lösung.
* Für Hochverfügbarkeit können Sie auch mehrere Bereitstellungs-Agents bereitstellen und registrieren, die dann den gleichen Satz von lokalen AD-Domänen behandeln.

> [!IMPORTANT]
> Für Hochverfügbarkeit in Produktionsumgebungen empfiehlt Microsoft mindestens drei Bereitstellungs-Agents, die mit Ihrem Azure AD-Mandanten konfiguriert sind.

#### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Auswählen von Bereitstellungsconnector-Apps für die Bereitstellung

Beim Integrieren von Workday und Active Directory müssen mehrere Quell- und Zielsysteme berücksichtigt werden:

| Quellsystem | Zielsystem | Notizen |
| ---------- | ---------- | ---------- |
| Workday | Active Directory-Domäne | Jede Domäne wird als eigenes Zielsystem behandelt. |
| Workday | Azure AD-Mandant | Entsprechend dem Bedarf reiner Cloudbenutzer. |
| Active Directory-Gesamtstruktur | Azure AD-Mandant | Dieser Flow erfolgt derzeit über AAD Connect. |
| Azure AD-Mandant | Workday | Für das Zurückschreiben von E-Mail-Adressen |

Um Bereitstellungsworkflows für Workday und Active Directory zu ermöglichen, bietet Azure AD mehrere Bereitstellungsconnector-Apps, die über den Azure AD-App-Katalog hinzugefügt werden können:

![AAD-App-Katalog](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday to Active Directory Provisioning:** Diese App vereinfacht die Bereitstellung von Benutzerkonten aus Workday in eine einzelne Active Directory-Domäne. Wenn Sie über mehrere Domänen verfügen, können Sie eine Instanz dieser App aus dem Azure AD-App-Katalog für jede Active Directory-Domäne hinzufügen, in der die Bereitstellung erfolgen soll.

* **Workday to Azure AD Provisioning**: Wenngleich AAD Connect das Tool ist, das Sie zum Synchronisieren von Active Directory-Benutzern mit Azure Active Directory verwenden sollten, können Sie auch diese App nutzen, um die Bereitstellung reiner Cloudbenutzer aus Workday in einem einzelnen Azure Active Directory-Mandanten zu erleichtern.

* **Workday Writeback:** Diese App vereinfacht das Zurückschreiben von E-Mail-Adressen von Benutzern aus Azure Active Directory in Workday.

> [!TIP]
> Die reguläre „Workday“-App dient zum Einrichten des einmaligen Anmeldens zwischen Workday und Azure Active Directory. 

#### <a name="determine-workday-to-ad-user-attribute-mapping-and-transformations"></a>Bestimmen der Benutzerattributzuordnung und Transformationen zwischen Workday und AD

Beantworten Sie die folgenden Fragen, bevor Sie die Benutzerbereitstellung in einer Active Directory-Domäne konfigurieren. Die Antworten auf diese Fragen bestimmen, wie Ihre Bereichsfilter und Attributzuordnungen festgelegt werden müssen.

* **Welche Benutzer in Workday müssen in dieser Active Directory-Gesamtstruktur bereitgestellt werden?**

   * *Beispiel: Benutzer, bei denen das Workday-Attribut „Company“ den Wert „Contoso“ und das Attribut „Worker_Type“ den Wert „Regular“ enthält*

* **Wie werden Benutzer in verschiedene Organisationseinheiten (OEs) weitergeleitet?**

   * *Beispiel: Benutzer werden wie in den Workday-Attributen „Municipality“ und „Country_Region_Reference“ definiert zu Organisationseinheiten weitergeleitet, die einem Bürostandort entsprechen*

* **Wie müssen die folgenden Attribute in Active Directory aufgefüllt werden?**

   * Allgemeiner Name (cn)
      * *Beispiel: Verwenden Sie den von der Personalabteilung festgelegten Workday-Wert „User_ID“.*
      
   * Mitarbeiter-ID (employeeId)
      * *Beispiel: Verwenden Sie den Workday-Wert „Worker_ID“.*
      
   * SAM-Kontoname (sAMAccountName)
      * *Beispiel: Verwenden Sie den Workday-Wert „User_ID“, gefiltert mit einem Azure AD-Bereitstellungsausdruck, um unzulässige Zeichen zu entfernen.*
      
   * Benutzerprinzipalname (userPrincipalName)
      * *Beispiel: Verwenden Sie den Workday-Wert „User_ID“ mit einem Azure AD-Bereitstellungsausdruck, um einen Domänennamen anzufügen.*

* **Wie müssen Benutzer zwischen Workday und Active Directory zugeordnet werden?**

  * *Beispiel: Benutzer mit einem bestimmten Workday-Wert „Worker_ID“ werden Active Directory-Benutzern zugeordnet, deren „employeeID“ den gleichen Wert enthält. Wenn der Wert von „Worker_ID“ nicht in Active Directory gefunden wird, erstellen Sie einen neuen Benutzer.*
  
* **Enthält die Active Directory-Gesamtstruktur bereits die Benutzer-IDs, die notwendig sind, damit die Zuordnungslogik funktioniert?**

  * *Beispiel: Im Fall einer neuen Workday-Bereitstellung wird dringend empfohlen, Active Directory vorab mit den korrekten Worker_ID-Werten aus Workday (oder einem eindeutigen ID-Wert Ihrer Wahl) aufzufüllen, um die Zuordnungslogik so einfach wie möglich zu halten.*



Das Einrichten und Konfigurieren dieser spezielle Bereitstellungsconnector-Apps ist das Thema in den verbleibenden Abschnitten dieses Tutorials. Welche Apps Sie konfigurieren, hängt davon ab, in welchen Systemen die Bereitstellung erfolgen soll und wie viele Active Directory-Domänen und Azure AD-Mandanten in Ihrer Umgebung vorhanden sind.



## <a name="configure-integration-system-user-in-workday"></a>Konfigurieren eines Integrationssystembenutzers in Workday

Eine gängige Anforderung an alle Workday-Bereitstellungsconnectors sind Anmeldeinformationen für ein Workday-Systemintegrationskonto, mit dem eine Verbindung mit der Workday Human Resources-API hergestellt wird. In diesem Abschnitt wird das Erstellen eines Integrationssystembenutzers in Workday beschrieben.

> [!NOTE]
> Sie können diesen Schritt auslassen und stattdessen ein globales Workday-Administratorkonto als Systemintegrationskonto nutzen. Diese Vorgehensweise ist für Demos einwandfrei, wird jedoch für Produktionsbereitstellungen nicht empfohlen.

### <a name="create-an-integration-system-user"></a>Erstellen eines Integrationssystembenutzers

**So erstellen Sie einen Integrationssystembenutzer**

1. Melden Sie sich mithilfe eines Administratorkontos bei Ihrem Workday-Mandanten an. Geben Sie in der **Workday-Anwendung** die Suchzeichenfolge „Benutzer erstellen“ in das Suchfeld ein, und klicken Sie dann auf den Link **Create Integration System User** (Integrationssystembenutzer erstellen).

    ![Benutzer erstellen](./media/workday-inbound-tutorial/wd_isu_01.png "Benutzer erstellen")
2. Führen Sie die Aufgabe **Integrationssystembenutzer erstellen** aus, indem Sie einen Benutzernamen und ein Kennwort für einen neuen Integrationssystembenutzer angeben.  
 * Lassen Sie das Kontrollkästchen **Bei der nächsten Anmeldung neues Kennwort anfordern** deaktiviert. Dieser Benutzer meldet sich programmgesteuert an.
 * Übernehmen Sie für **Sitzungstimeout in Minuten** den Standardwert 0. Diese Einstellung verhindert, dass Sitzungen des Benutzers vorzeitig beendet werden.
 * Wählen Sie die Option **Do Not Allow UI Sessions** (Keine Sitzungen mit Benutzeroberfläche zulassen) aus. Sie bietet zusätzliche Sicherheit, da sie verhindert, dass sich ein Benutzer mit dem Kennwort für das Integrationssystem bei Workday anmeldet. 

    ![Integrationssystembenutzer erstellen](./media/workday-inbound-tutorial/wd_isu_02.png "Integrationssystembenutzer erstellen")

### <a name="create-a-security-group"></a>Erstellen einer Sicherheitsgruppe
In diesem Schritt erstellen Sie eine uneingeschränkte Sicherheitsgruppe für das Integrationssystem in Workday und ordnen den Integrationssystembenutzer, den Sie im vorherigen Schritt erstellt haben, dieser Gruppe zu.

**So erstellen Sie eine Sicherheitsgruppe**

1. Geben Sie „Sicherheitsgruppe erstellen“ in das Suchfeld ein, und klicken Sie dann auf **Sicherheitsgruppe erstellen**.

    ![Sicherheitsgruppe erstellen](./media/workday-inbound-tutorial/wd_isu_03.png "Sicherheitsgruppe erstellen")
2. Führen Sie die Aufgabe **Sicherheitsgruppe erstellen** aus.  
   * Wählen Sie in der Dropdownliste **Type of Tenanted Security Group** (Typ der Mandantensicherheitsgruppe) die Option **Integration System Security Group (Unconstrained)** (Integrationssystem-Sicherheitsgruppe – uneingeschränkt) aus.

    ![Sicherheitsgruppe erstellen](./media/workday-inbound-tutorial/wd_isu_04.png "Sicherheitsgruppe erstellen")

3. Nach dem Erstellen der Sicherheitsgruppe wird eine Seite angezeigt, auf der Sie der Sicherheitsgruppe Mitglieder zuweisen können. Fügen Sie den neuen Integrationssystembenutzer dieser Sicherheitsgruppe hinzu, und wählen Sie einen geeigneten Organisationsbereich aus.
![Sicherheitsgruppe bearbeiten](./media/workday-inbound-tutorial/wd_isu_05.png "Sicherheitsgruppe bearbeiten")
 
### <a name="configure-domain-security-policy-permissions"></a>Konfigurieren der Berechtigungen der Domänensicherheitsrichtlinie
In diesem Schritt gewähren Sie der Sicherheitsgruppe die Berechtigungen der Domänensicherheitsrichtlinie für die Mitarbeiterdaten.

**So konfigurieren Sie die Berechtigungen der Domänensicherheitsrichtlinie**

1. Geben Sie **Domain Security Configuration** (Domänensicherheitskonfiguration) in das Suchfeld ein, und klicken Sie dann auf den Link **Domain Security Configuration Report** (Bericht zur Domänensicherheitskonfiguration).  

    ![Domänensicherheitsrichtlinien](./media/workday-inbound-tutorial/wd_isu_06.png "Domänensicherheitsrichtlinien")  
2. Suchen Sie im Textfeld **Domäne** nach den folgenden Domänen, und fügen Sie sie einzeln dem Filter hinzu.  
   * *External Account Provisioning* (Externe Kontobereitstellung)
   * *Worker Data: Public Worker Reports* (Mitarbeiterdaten: öffentliche Mitarbeiterberichte)
   * *Person Data: Work Contact Information* (Personendaten: Kontaktinformationen von Mitarbeitern)
   * *Worker Data: All Positions* (Mitarbeiterdaten: alle Positionen)
   * *Worker Data: Current Staffing Information* (Mitarbeiterdaten: aktuelle Personalinformationen)
   * *Worker Data: Business Title on Worker Profile* (Mitarbeiterdaten: Berufsbezeichnung in Mitarbeiterprofil)
 
    ![Domänensicherheitsrichtlinien](./media/workday-inbound-tutorial/wd_isu_07.png "Domänensicherheitsrichtlinien")  

    ![Domänensicherheitsrichtlinien](./media/workday-inbound-tutorial/wd_isu_08.png "Domänensicherheitsrichtlinien") 

    Klicken Sie auf **OK**.

3. Wählen Sie im angezeigten Bericht die Auslassungspunkte (...) neben **External Account Provisioning** (Externe Kontobereitstellung) aus, und klicken Sie auf die Menüoption **Domain -> Edit Security Policy Permissions** (Domäne > Berechtigungen für Sicherheitsrichtlinie bearbeiten).

    ![Domänensicherheitsrichtlinien](./media/workday-inbound-tutorial/wd_isu_09.png "Domänensicherheitsrichtlinien")  

4. Scrollen Sie auf der Seite **Edit Domain Security Policy Permissions** (Berechtigungen für Domänensicherheitsrichtlinie bearbeiten) nach unten zum Abschnitt **Integration Permissions** (Integrationsberechtigungen). Klicken Sie auf das Zeichen „+“, um die Integrationssystemgruppe der Liste der Sicherheitsgruppen mit den Integrationsberechtigungen **Get** und **Put** hinzuzufügen.

    ![Berechtigung bearbeiten](./media/workday-inbound-tutorial/wd_isu_10.png "Berechtigung bearbeiten")  

5. Klicken Sie auf das Zeichen „+“, um die Integrationssystemgruppe der Liste der Sicherheitsgruppen mit den Integrationsberechtigungen **Get** und **Put** hinzuzufügen.

    ![Berechtigung bearbeiten](./media/workday-inbound-tutorial/wd_isu_11.png "Berechtigung bearbeiten")  

6. Wiederholen Sie die obigen Schritte 3 bis 5 für jede der folgenden verbleibenden Sicherheitsrichtlinien:

   | Vorgang | Domänensicherheitsrichtlinie |
   | ---------- | ---------- | 
   | Get und Put | Mitarbeiterdaten: öffentliche Mitarbeiterberichte |
   | Get und Put | Personendaten: Kontaktinformationen von Mitarbeitern |
   | Get | Mitarbeiterdaten: alle Positionen |
   | Get | Mitarbeiterdaten: aktuelle Personalinformationen |
   | Get | Mitarbeiterdaten: Berufsbezeichnung in Mitarbeiterprofil |

### <a name="configure-business-process-security-policy-permissions"></a>Konfigurieren von Sicherheitsrichtlinienberechtigungen für Geschäftsprozesse
In diesem Schritt gewähren Sie der Sicherheitsgruppe Berechtigungen der Sicherheitsrichtlinien für Geschäftsprozesse für die Mitarbeiterdaten. Diese sind für das Einrichten des Workday Writeback-App-Connectors erforderlich. 

**So konfigurieren Sie Sicherheitsrichtlinienberechtigungen für Geschäftsprozesse**

1. Geben Sie **Business Process Policy** (Geschäftsprozessrichtlinie) in das Suchfeld ein, und klicken Sie dann auf den Link für die Aufgabe **Edit Business Process Security Policy** (Sicherheitsrichtlinie für Geschäftsprozesse bearbeiten).  

    ![Sicherheitsrichtlinien für Geschäftsprozesse](./media/workday-inbound-tutorial/wd_isu_12.png "Sicherheitsrichtlinien für Geschäftsprozesse")  

2. Suchen Sie im Textfeld **Business Process Type** (Geschäftsprozesstyp) nach *Contact* (Kontakt), wählen Sie den Geschäftsprozess **Contact Change** (Kontakt ändern) aus, und klicken Sie auf **OK**.

    ![Sicherheitsrichtlinien für Geschäftsprozesse](./media/workday-inbound-tutorial/wd_isu_13.png "Sicherheitsrichtlinien für Geschäftsprozesse")  

3. Scrollen Sie auf der Seite **Edit Business Process Security Policy** (Sicherheitsrichtlinien für Geschäftsprozesse bearbeiten) zum Abschnitt **Maintain Contact Information (Web Service)** (Kontaktinformationen verwalten (Webdienst)).

    ![Sicherheitsrichtlinien für Geschäftsprozesse](./media/workday-inbound-tutorial/wd_isu_14.png "Sicherheitsrichtlinien für Geschäftsprozesse")  

4. Wählen Sie die neue Sicherheitsgruppe des Integrationssystems aus, und fügen Sie sie der Liste der Sicherheitsgruppen hinzu, die Anforderungen an Webdienste initiieren können. Klicken Sie auf **Done** (Fertig). 

    ![Sicherheitsrichtlinien für Geschäftsprozesse](./media/workday-inbound-tutorial/wd_isu_15.png "Sicherheitsrichtlinien für Geschäftsprozesse")  

 
### <a name="activate-security-policy-changes"></a>Aktivieren von Sicherheitsrichtlinienänderungen

**So aktivieren Sie Sicherheitsrichtlinienänderungen**

1. Geben Sie „aktivieren“ in das Suchfeld ein, und klicken Sie dann auf den Link **Ausstehende Sicherheitsrichtlinienänderungen aktivieren**.

    ![Aktivieren](./media/workday-inbound-tutorial/wd_isu_16.png "Aktivieren") 
2. Geben Sie zum Ausführen der Aufgabe „Ausstehende Sicherheitsrichtlinienänderungen aktivieren“ zunächst einen Kommentar für Überwachungszwecke ein, und klicken Sie dann auf die Schaltfläche **OK**. 

    ![Ausstehende Sicherheitsrichtlinienänderungen aktivieren](./media/workday-inbound-tutorial/wd_isu_17.png "Ausstehende Sicherheitsrichtlinienänderungen aktivieren")  
1. Führen Sie die Aufgabe auf dem nächsten Bildschirm aus, indem Sie das Kontrollkästchen **Bestätigen** aktivieren und auf **OK** klicken.

    ![Ausstehende Sicherheitsrichtlinienänderungen aktivieren](./media/workday-inbound-tutorial/wd_isu_18.png "Ausstehende Sicherheitsrichtlinienänderungen aktivieren")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfiguration der Benutzerbereitstellung aus Workday in Active Directory

Befolgen Sie diese Anweisungen zum Konfigurieren der Bereitstellung von Benutzerkonten aus Workday in jeder Active Directory-Domäne im Geltungsbereich Ihrer Integration.

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>Teil 1: Installieren und Konfigurieren der lokalen Bereitstellungs-Agents

Um Active Directory lokal bereitzustellen, muss ein Agent auf einem Server installiert mit .NET Framework 4.7 oder höher und Netzwerkzugriff auf die gewünschten Active Directory-Domänen installiert werden.

> [!TIP]
> Sie können die Version von .NET Framework auf dem Server mithilfe der Anweisungen [hier](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) überprüfen.
> Wenn auf dem Server nicht .NET 4.7 oder höher installiert ist, können Sie es [hier](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows) herunterladen.  

Nachdem Sie .NET 4.7 oder höher bereitgestellt haben, können Sie den **[lokalen Bereitstellungs-Agent hier](https://go.microsoft.com/fwlink/?linkid=847801)** herunterladen. Führen Sie die folgenden Schritte aus, um die Agent-Konfiguration abzuschließen.

1. Melden Sie sich auf dem Windows-Server an, auf dem Sie den neuen Agent installieren möchten.
2. Starten Sie den Installer für den Bereitstellungs-Agent, akzeptieren Sie die Bedingungen, und klicken Sie auf die Schaltfläche **Installieren**.
![Installationsbildschirm](./media/workday-inbound-tutorial/pa_install_screen_1.png "Installationsbildschirm")

3. Nachdem die Installation abgeschlossen ist, wird der Assistent gestartet, und der Bildschirm **Azure AD verbinden** wird angezeigt. Klicken Sie auf die Schaltfläche **Authentifizieren**, um eine Verbindung mit Ihrer Azure AD-Instanz herzustellen.
![Azure AD verbinden](./media/workday-inbound-tutorial/pa_install_screen_2.png "Azure AD verbinden")

4. Authentifizieren Sie Ihre Azure AD-Instanz mit den Anmeldeinformationen eines globalen Administrators. 
![Administratorauthentifizierung](./media/workday-inbound-tutorial/pa_install_screen_3.png "Administratorauthentifizierung")

5. Nach der erfolgreichen Authentifizierung mit Azure AD wird der Bildschirm **Active Directory verbinden** angezeigt. In diesem Schritt geben Sie Ihren AD-Domänennamen an und klicken auf die Schaltfläche **Verzeichnis hinzufügen**.
![Verzeichnis hinzufügen](./media/workday-inbound-tutorial/pa_install_screen_4.png "Verzeichnis hinzufügen")

6. Sie werden daraufhin aufgefordert, die Anmeldeinformationen für die Verbindung mit der AD-Domäne einzugeben. Auf dem gleichen Bildschirm können Sie die **Domänencontrollerpriorität auswählen**, um Domänencontroller anzugeben, die der Agent zum Senden von Bereitstellungsanforderungen verwenden soll.
![Domänenanmeldeinformationen](./media/workday-inbound-tutorial/pa_install_screen_5.png "Domänenanmeldeinformationen")

7. Nach dem Konfigurieren der Domäne zeigt der Installer eine Liste der konfigurierten Domänen an. Auf diesem Bildschirm können Sie Schritt 5 und 6 wiederholen, um weitere Domänen hinzuzufügen, oder auf **Weiter** klicken, um mit der Registrierung des Agents fortzufahren. 
![Konfigurierte Domänen](./media/workday-inbound-tutorial/pa_install_screen_6.png "Konfigurierte Domänen")

   > [!NOTE]
   > Wenn Sie über mehrere AD-Domänen verfügen (z.B. na.contoso.com, emea.contoso.com), fügen Sie jede Domäne einzeln der Liste hinzu. Das alleinige Hinzufügen der übergeordneten Domäne (z.B. contoso.com) ist nicht ausreichend, und es wird empfohlen, jede untergeordnete Domäne beim Agent zu registrieren. 

8. Überprüfen Sie die Konfigurationsdetails, und klicken Sie auf **Bestätigen**, um den Agent zu registrieren. 
![Bestätigungsbildschirm](./media/workday-inbound-tutorial/pa_install_screen_7.png "Bestätigungsbildschirm")

9. Der Konfigurations-Assistent zeigt den Fortschritt der Agent-Registrierung an.
![Agent-Registrierung](./media/workday-inbound-tutorial/pa_install_screen_8.png "Agent-Registrierung")

10. Nach der erfolgreichen Agent-Registrierung können Sie auf **Beenden** klicken, um den Assistenten zu beenden. 
![Abschlussbildschirm](./media/workday-inbound-tutorial/pa_install_screen_9.png "Abschlussbildschirm")

11. Überprüfen Sie die Installation des Agents, und stellen Sie sicher, dass er ausgeführt wird, indem Sie das Snap-In „Dienste“ öffnen und nach dem Dienst mit der Bezeichnung „Microsoft Azure AD Connect Provisioning Agent“ suchen. ![Dienste](./media/workday-inbound-tutorial/services.png)  


**Behandeln von Problemen mit dem Agent**

Das [Windows-Ereignisprotokoll](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) auf dem Windows Server-Computer, der als Host für den Agent fungiert, enthält Ereignisse für alle Vorgänge, die der Agent ausgeführt hat. So zeigen Sie diese Ereignisse an:
    
1. Öffnen Sie **Eventvwr.msc**.
2. Klicken Sie auf **Windows-Protokolle > Anwendung**.
3. Sehen Sie sich alle Ereignisse an, die unter der Quelle **AAD.Connect.ProvisioningAgent** protokolliert wurden. 
4. Suchen Sie nach Fehlern und Warnungen.

    
### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Teil 2: Hinzufügen der Bereitstellungsconnector-App und Herstellen der Verbindung mit Workday

**So konfigurieren Sie die Bereitstellung aus Workday in Active Directory**

1.  Besuchen Sie <https://portal.azure.com>.

2.  Wählen Sie auf der linken Navigationsleiste **Azure Active Directory** aus.

3.  Klicken Sie auf **Unternehmensanwendungen** und dann auf **Alle Anwendungen**.

4.  Klicken Sie auf **Anwendung hinzufügen**, und wählen Sie die Kategorie **Alle** aus.

5.  Suchen Sie nach **Workday Provisioning to Active Directory**, und fügen Sie die App aus dem Katalog hinzu.

6.  Sobald die App hinzugefügt wurde und der Bildschirm mit den App-Details angezeigt wird, wählen Sie **Bereitstellung** aus.

7.  Legen Sie **Bereitstellungsmodus** **auf** **Automatisch** fest.

8.  Vervollständigen Sie den Abschnitt **Administratoranmeldeinformationen** wie folgt:

   * **Administratorbenutzername**: Geben Sie den Benutzernamen des Workday-Systemintegrationskontos mit angefügtem Mandantendomänennamen ein. **Dies sollte ungefähr wie folgt aussehen: username@contoso4**

   * **Administratorkennwort**: Geben Sie das Kennwort des Workday-Systemintegrationskontos ein.

   * **Mandanten-URL**: Geben Sie die URL des Workday-Webdienstendpunkts für Ihren Mandanten ein. Diese sollte wie folgt lauten: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources. Dabei wird „contoso4“ durch den Namen Ihres Mandanten und „wd3-impl“ durch die ordnungsgemäße Umgebungszeichenfolge ersetzt.

   * **Active Directory-Gesamtstruktur:** Der „Name“ Ihrer Active Directory-Domäne, mit dem diese beim Agent registriert wurde. Dieser meist eine Zeichenfolge wie: *contoso.com*

   * **Active Directory-Container:** Geben Sie den DN des Containers an, in dem der Agent Benutzerkonten standardmäßig erstellen soll. 
        Beispiel: *OU=Standard Users,OU=Users,DC=contoso,DC=test*
> [!NOTE]
> Diese Einstellung wird nur für die Benutzerkontoerstellung verwendet, wenn das Attribut *parentDistinguishedName* nicht in den Attributzuordnungen konfiguriert ist. Diese Einstellung wird nicht zum Suchen von Benutzern oder für Updatevorgänge verwendet. Der Suchvorgang schließt die gesamte Domänenteilstruktur ein.
   * **Benachrichtigungs-E-Mail**: Geben Sie Ihre E-Mail-Adresse ein, und aktivieren Sie das Kontrollkästchen „E-Mail senden, wenn Fehler auftritt“.
> [!NOTE]
> Der Azure AD-Bereitstellungsdienst sendet eine E-Mail-Benachrichtigung, wenn der Bereitstellungsauftrag in den Zustand [Quarantäne](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) wechselt.

   * Klicken Sie auf die Schaltfläche **Verbindung testen**. Wenn der Verbindungstest erfolgreich ist, klicken Sie oben auf die Schaltfläche **Speichern**. Überprüfen Sie bei einem Fehler, ob die Workday-Anmeldeinformationen und die AD-Anmeldeinformationen, die beim Einrichten des Agents angegeben wurden, gültig sind.

![Azure-Portal](./media/workday-inbound-tutorial/wd_1.png)

### <a name="part-2-configure-attribute-mappings"></a>Teil 2: Konfigurieren von Attributzuordnungen 

In diesem Abschnitt konfigurieren Sie den Fluss von Benutzerdaten aus Workday in Active Directory.

1.  Klicken Sie auf der Registerkarte „Bereitstellung“ unter **Zuordnungen** auf **Workday-Mitarbeiter in lokale Umgebung synchronisieren**.

2.  Im Feld **Quellobjektbereich** können Sie die Benutzergruppen in Workday für die Bereitstellung in Active Directory auswählen, indem Sie verschiedene attributbasierte Filter definieren. Die Standardoption ist „Alle Benutzer in Workday“. Beispielfilter:

   * Beispiel: Auswählen der Benutzer mit Mitarbeiter-IDs von 1000000 bis 2000000

      * Attribut: WorkerID

      * Operator: REGEX Match

      * Wert: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Beispiel: Nur Mitarbeiter und keine vorübergehend Beschäftigten 

      * Attribut: EmployeeID

      * Operator: IS NOT NULL

3.  Im Feld **Zielobjektaktionen** können Sie global filtern, welche Aktionen auf Active Directory angewendet werden können. **Erstellen** und **Aktualisieren** erfolgen am häufigsten.

4.  Im Abschnitt **Attributzuordnungen** können Sie definieren, wie einzelne Workday-Attribute Active Directory-Attributen zugeordnet werden.

5. Klicken Sie auf eine vorhandene Attributzuordnung, um sie zu aktualisieren. Oder klicken Sie am unteren Bildschirmrand auf **Neue Zuordnung hinzufügen**, um neue Zuordnungen hinzuzufügen. Eine einzelne Attributzuordnung unterstützt die folgenden Eigenschaften:

      * **Zuordnungstyp**

         * **Direkt**: Schreibt den Wert des Workday-Attributs unverändert in das AD-Attribut.

         * **Konstante**: Schreibt einen statischen, konstanten Zeichenfolgenwert in das AD-Attribut.

         * **Ausdruck**: Ermöglicht das Schreiben eines benutzerdefinierten Werts basierend auf einem oder mehreren Workday-Attributen in das AD-Attribut. [Weitere Informationen finden Sie im Artikel zu Ausdrücken](../manage-apps/functions-for-customizing-application-data.md).

      * **Quellattribut**: Das Benutzerattribut aus Workday. Wenn das gesuchte Attribut nicht vorhanden ist, finden Sie weitere Informationen unter [Anpassen der Liste der Workday-Benutzerattribute](#customizing-the-list-of-workday-user-attributes).

      * **Standardwert**: Optional. Wenn das Quellattribut einen leeren Wert aufweist, wird von der Zuordnung stattdessen dieser Wert geschrieben.
            Die meisten Konfigurationen sehen vor, dieses Feld leer zu lassen.

      * **Zielattribut**: Das Benutzerattribut in Active Directory.

      * **Objekte mit diesem Attribut abgleichen**: Gibt an, ob diese Zuordnung zum eindeutigen Bestimmen von Benutzern zwischen Workday und Active Directory verwendet werden soll. Diese Einstellung wird meist auf das Feld „Mitarbeiter-ID“ für Workday festgelegt, das in der Regel den „Mitarbeiter-ID“-Attributen in Active Directory zugeordnet wird.

      * **Rangfolge für Abgleich**: Es können mehrere Attribute für den Abgleich festgelegt werden. Falls mehrere vorhanden sind, werden sie entsprechend der in diesem Feld festgelegten Reihenfolge ausgewertet. Sobald eine Übereinstimmung gefunden wird, werden keine weiteren Attribute für den Abgleich mehr ausgewertet.

      * **Diese Zuordnung anwenden**
       
         * **Immer**: Wenden Sie diese Zuordnung sowohl bei der Aktion zum Erstellen eines Benutzers als auch bei der zum Aktualisieren eines Benutzers an.

         * **Nur während der Erstellung**: Wenden Sie diese Zuordnung nur bei der Aktion zum Erstellen eines Benutzers an.

6. Klicken Sie oben im Abschnitt „Attributzuordnung“ auf **Speichern**, um Ihre Zuordnungen zu speichern.

![Azure-Portal](./media/workday-inbound-tutorial/WD_2.PNG)

**Nachstehend finden Sie einige Beispiele für Attributzuordnungen zwischen Workday und Active Directory sowie einige häufig verwendete Ausdrücke**

-   Der Ausdruck für die Zuordnung zum Attribut „parentDistinguishedName“ wird verwendet, um Benutzer basierend auf mindestens einem Workday-Quellattribut in anderen Organisationseinheiten bereitzustellen. In diesem Beispiel werden Benutzer basierend auf dem Ort in verschiedenen Organisationseinheiten platziert.

-   Das Attribut „userPrincipalName“ in Active Directory wird durch Verketten der Workday-Benutzer-ID mit einem Domänensuffix generiert.

-   [Hier finden Sie Dokumentation zum Schreiben von Ausdrücken](../manage-apps/functions-for-customizing-application-data.md). Die Dokumentation enthält auch Beispiele zum Entfernen von Sonderzeichen.

  
| WORKDAY-ATTRIBUT | ACTIVE DIRECTORY-ATTRIBUT |  ÜBEREINSTIMMENDE ID? | ERSTELLEN/AKTUALISIEREN |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Ja** | Wird nur bei der Erstellung geschrieben |
| **UserID**    |  cn    |   |   Wird nur bei der Erstellung geschrieben |
| **Join („@“, [Benutzer-ID] „contoso.com“)**   | userPrincipalName     |     | Wird nur bei der Erstellung geschrieben 
| **Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Wird nur bei der Erstellung geschrieben |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | Erstellen und aktualisieren |
| **Vorname**   | givenName       |     |    Erstellen und aktualisieren |
| **Nachname**   |   sn   |     |  Erstellen und aktualisieren |
| **PreferredNameData**  |  displayName |     |   Erstellen und aktualisieren |
| **Company**         | company   |     |  Erstellen und aktualisieren |
| **SupervisoryOrganization**  | department  |     |  Erstellen und aktualisieren |
| **ManagerReference**   | manager  |     |  Erstellen und aktualisieren |
| **BusinessTitle**   |  title     |     |  Erstellen und aktualisieren | 
| **AddressLineData**    |  streetAddress  |     |   Erstellen und aktualisieren |
| **Municipality**   |   l   |     | Erstellen und aktualisieren |
| **CountryReferenceTwoLetter**      |   co |     |   Erstellen und aktualisieren |
| **CountryReferenceTwoLetter**    |  c  |     |         Erstellen und aktualisieren |
| **CountryRegionReference** |  st     |     | Erstellen und aktualisieren |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Erstellen und aktualisieren |
| **PostalCode**  |   postalCode  |     | Erstellen und aktualisieren |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Erstellen und aktualisieren |
| **Fax**      | facsimileTelephoneNumber     |     |    Erstellen und aktualisieren |
| **Mobile**  |    mobile       |     |       Erstellen und aktualisieren |
| **LocalReference** |  preferredLanguage  |     |  Erstellen und aktualisieren |                                               
| **Switch(\[Municipality\], "OU=Standard Users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", “London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  Erstellen und aktualisieren |
  


### <a name="part-4-start-the-service"></a>Teil 4: Starten des Dienstes
Nach Abschluss der Teile 1 bis 3 können Sie im Azure-Portal den Bereitstellungsdienst starten.

1.  Legen Sie auf der Registerkarte **Bereitstellung** die Einstellung **Bereitstellungsstatus** auf **Ein** fest.

2. Klicken Sie auf **Speichern**.

3. Dadurch wird die erste Synchronisierung gestartet, die abhängig von der Anzahl der Benutzer in Workday eine variable Anzahl von Stunden dauern kann.

4. Im Azure-Portal können Sie sich auf der Registerkarte **Überwachungsprotokolle** jederzeit ansehen, welche Aktionen der Bereitstellungsdienst ausgeführt hat. Die Überwachungsprotokolle enthalten alle einzelnen Synchronisierungsereignisse des Bereitstellungsdiensts – beispielsweise, welche Benutzer in Workday gelesen und anschließend Active Directory hinzugefügt oder dort aktualisiert wurden. **[Ausführliche Anweisungen zum Lesen der Überwachungsprotokolle finden Sie in der Anleitung zur Erstellung von Bereitstellungsberichten.](../manage-apps/check-status-user-account-provisioning.md)**

1.  Prüfen Sie im [Windows-Ereignisprotokoll](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) auf dem Windows Server-Computer, der als Host für den Agent fungiert, ob neue Fehler oder Warnungen vorliegen. Starten Sie zum Anzeigen dieser Ereignisse **Eventvwr.msc** auf dem Server, und klicken Sie auf **Windows-Protokolle > Anwendung**. Alle bereitstellungsbezogenen Meldungen werden unter der Quelle **AADSyncAgent** protokolliert.

6. Anschließend wird auf der Registerkarte **Bereitstellung** ein Überwachungszusammenfassungsbericht ausgegeben:

![Azure-Portal](./media/workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Konfiguration der Benutzerbereitstellung in Azure Active Directory
Die Konfiguration der Bereitstellung in Azure Active Directory hängt von Ihren Bereitstellungsanforderungen ab (siehe die folgende Tabelle).

| Szenario | Lösung |
| -------- | -------- |
| **Benutzer müssen in Active Directory und Azure AD bereitgestellt werden** | **[AAD Connect](../hybrid/whatis-hybrid-identity.md)** verwenden |
| **Benutzer müssen nur in Active Directory bereitgestellt werden** | **[AAD Connect](../hybrid/whatis-hybrid-identity.md)** verwenden |
| **Benutzer müssen nur in Azure AD (als reine Cloudbenutzer) bereitgestellt werden** | Die App **Workday to Azure Active Directory provisioning** im App-Katalog verwenden |

Anleitungen zum Einrichten von Azure AD Connect finden Sie in der [Dokumentation zu Azure AD Connect](../hybrid/whatis-hybrid-identity.md).

In den folgenden Abschnitten wird das Einrichten einer Verbindung zwischen Workday und Azure AD beschrieben, um reine Cloudbenutzer bereitzustellen.

> [!IMPORTANT]
> Führen Sie diese Schritte nur aus, wenn es reine Cloudbenutzer gibt, die in Azure AD und nicht in lokalem Active Directory bereitgestellt werden müssen.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Teil 1: Hinzufügen der Bereitstellungsconnector-App für Azure AD und Herstellen der Verbindung mit Workday

**So konfigurieren Sie die Workday-zu-Azure Active Directory-Bereitstellung für reine Cloudbenutzer**

1.  Wechseln Sie zur Adresse <https://portal.azure.com>.

2.  Wählen Sie auf der linken Navigationsleiste **Azure Active Directory** aus.

3.  Klicken Sie auf **Unternehmensanwendungen** und dann auf **Alle Anwendungen**.

4.  Klicken Sie auf **Anwendung hinzufügen**, und wählen Sie dann die Kategorie **Alle** aus.

5.  Suchen Sie nach **Workday to Azure AD provisioning**, und fügen Sie die App aus dem Katalog hinzu.

6.  Sobald die App hinzugefügt wurde und der Bildschirm mit den App-Details angezeigt wird, wählen Sie **Bereitstellung** aus.

7.  Legen Sie **Bereitstellungsmodus** **auf** **Automatisch** fest.

8.  Vervollständigen Sie den Abschnitt **Administratoranmeldeinformationen** wie folgt:

   * **Administratorbenutzername**: Geben Sie den Benutzernamen des Workday-Systemintegrationskontos mit angefügtem Mandantendomänennamen ein. Dies sollte ungefähr wie folgt aussehen: username@contoso4

   * **Administratorkennwort**: Geben Sie das Kennwort des Workday-Systemintegrationskontos ein.

   * **Mandanten-URL**: Geben Sie die URL des Workday-Webdienstendpunkts für Ihren Mandanten ein. Diese sollte wie folgt lauten: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources. Dabei wird „contoso4“ durch den Namen Ihres Mandanten und „wd3-impl“ durch die ordnungsgemäße Umgebungszeichenfolge ersetzt. Ist diese URL nicht bekannt, erkundigen Sie sich bei Ihrem Workday-Integrationspartner oder bei einem Supportmitarbeiter nach der korrekten URL.

   * **Benachrichtigungs-E-Mail**: Geben Sie Ihre E-Mail-Adresse ein, und aktivieren Sie das Kontrollkästchen „E-Mail senden, wenn Fehler auftritt“.

   * Klicken Sie auf die Schaltfläche **Verbindung testen**.

   * Wenn der Verbindungstest erfolgreich ist, klicken Sie oben auf die Schaltfläche **Speichern**. Falls nicht, überprüfen Sie, ob die Workday-URL und -Anmeldeinformationen in Workday gültig sind.

### <a name="part-2-configure-attribute-mappings"></a>Teil 2: Konfigurieren von Attributzuordnungen 

In diesem Abschnitt konfigurieren Sie den Fluss von Benutzerdaten aus Workday in Azure Active Directory für reine Cloudbenutzer.

1. Klicken Sie auf der Registerkarte „Bereitstellung“ unter **Zuordnungen** auf **Mitarbeiter in Azure AD synchronisieren**.

2. Im Feld **Quellobjektbereich** können Sie die Benutzergruppen in Workday für die Bereitstellung in Azure Active Directory auswählen, indem Sie verschiedene attributbasierte Filter definieren. Die Standardoption ist „Alle Benutzer in Workday“. Beispielfilter:

   * Beispiel: Auswählen der Benutzer mit Mitarbeiter-IDs von 1000000 bis 2000000

      * Attribut: WorkerID

      * Operator: REGEX Match

      * Wert: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Beispiel: Nur vorübergehend Beschäftigte und keine regulären Mitarbeiter

      * Attribut: ContingentID

      * Operator: IS NOT NULL

3. Im Feld **Zielobjektaktionen** können Sie global filtern, welche Aktionen auf Azure AD angewendet werden können. **Erstellen** und **Aktualisieren** erfolgen am häufigsten.

4. Im Abschnitt **Attributzuordnungen** können Sie definieren, wie einzelne Workday-Attribute Active Directory-Attributen zugeordnet werden.

5. Klicken Sie auf eine vorhandene Attributzuordnung, um sie zu aktualisieren. Oder klicken Sie am unteren Bildschirmrand auf **Neue Zuordnung hinzufügen**, um neue Zuordnungen hinzuzufügen. Eine einzelne Attributzuordnung unterstützt die folgenden Eigenschaften:

   * **Zuordnungstyp**

      * **Direkt**: Schreibt den Wert des Workday-Attributs unverändert in das AD-Attribut.

      * **Konstante**: Schreibt einen statischen, konstanten Zeichenfolgenwert in das AD-Attribut.

      * **Ausdruck**: Ermöglicht das Schreiben eines benutzerdefinierten Werts basierend auf einem oder mehreren Workday-Attributen in das AD-Attribut. [Weitere Informationen finden Sie im Artikel zu Ausdrücken](../manage-apps/functions-for-customizing-application-data.md).

   * **Quellattribut**: Das Benutzerattribut aus Workday. Wenn das gesuchte Attribut nicht vorhanden ist, finden Sie weitere Informationen unter [Anpassen der Liste der Workday-Benutzerattribute](#customizing-the-list-of-workday-user-attributes).

   * **Standardwert**: Optional. Wenn das Quellattribut einen leeren Wert aufweist, wird von der Zuordnung stattdessen dieser Wert geschrieben.
            Die meisten Konfigurationen sehen vor, dieses Feld leer zu lassen.

   * **Zielattribut**: Das Benutzerattribut in Azure AD.

   * **Objekte mit diesem Attribut abgleichen**: Gibt an, ob diese Zuordnung zum eindeutigen Bestimmen von Benutzern zwischen Workday und Azure AD verwendet werden soll. Diese Einstellung wird meist auf das Feld „Mitarbeiter-ID“ für Workday festgelegt, das in der Regel dem „Mitarbeiter-ID“-Attribut (neu) oder einem Erweiterungsattribut in Azure AD zugeordnet wird.

   * **Rangfolge für Abgleich**: Es können mehrere Attribute für den Abgleich festgelegt werden. Falls mehrere vorhanden sind, werden sie entsprechend der in diesem Feld festgelegten Reihenfolge ausgewertet. Sobald eine Übereinstimmung gefunden wird, werden keine weiteren Attribute für den Abgleich mehr ausgewertet.

   * **Diese Zuordnung anwenden**

     * **Immer**: Wenden Sie diese Zuordnung sowohl bei der Aktion zum Erstellen eines Benutzers als auch bei der zum Aktualisieren eines Benutzers an.

     * **Nur während der Erstellung**: Wenden Sie diese Zuordnung nur bei der Aktion zum Erstellen eines Benutzers an.

6. Klicken Sie oben im Abschnitt „Attributzuordnung“ auf **Speichern**, um Ihre Zuordnungen zu speichern.

### <a name="part-3-start-the-service"></a>Teil 3: Starten des Dienstes
Sobald Sie die Teile 1 und 2 abgeschlossen haben, können Sie den Bereitstellungsdienst starten.

1. Legen Sie auf der Registerkarte **Bereitstellung** die Einstellung **Bereitstellungsstatus** auf **Ein** fest.

2. Klicken Sie auf **Speichern**.

3. Dadurch wird die erste Synchronisierung gestartet, die abhängig von der Anzahl der Benutzer in Workday eine variable Anzahl von Stunden dauern kann.

4. Einzelne Synchronisierungsereignisse können auf der Registerkarte **Überwachungsprotokolle** angezeigt werden. **[Ausführliche Anweisungen zum Lesen der Überwachungsprotokolle finden Sie in der Anleitung zur Erstellung von Bereitstellungsberichten.](../manage-apps/check-status-user-account-provisioning.md)**

5. Anschließend wird auf der Registerkarte **Bereitstellung** ein Überwachungszusammenfassungsbericht ausgegeben:

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Konfigurieren des Zurückschreibens von E-Mail-Adressen in Workday
Befolgen Sie diese Anweisungen zum Konfigurieren des Zurückschreibens von E-Mail-Adressen der Benutzer aus Azure Active Directory in Workday.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Teil 1: Hinzufügen der Bereitstellungsconnector-App und Herstellen der Verbindung mit Workday

**So konfigurieren Sie den Workday Writeback-Connector**

1. Besuchen Sie <https://portal.azure.com>.

2. Wählen Sie auf der linken Navigationsleiste **Azure Active Directory** aus.

3. Klicken Sie auf **Unternehmensanwendungen** und dann auf **Alle Anwendungen**.

4. Klicken Sie auf **Anwendung hinzufügen**, und wählen Sie dann die Kategorie **Alle** aus.

5. Suchen Sie nach **Workday Writeback**, und fügen Sie die App aus dem Katalog hinzu.

6. Sobald die App hinzugefügt wurde und der Bildschirm mit den App-Details angezeigt wird, wählen Sie **Bereitstellung** aus.

7. Legen Sie **Bereitstellungsmodus** **auf** **Automatisch** fest.

8. Vervollständigen Sie den Abschnitt **Administratoranmeldeinformationen** wie folgt:

   * **Administratorbenutzername**: Geben Sie den Benutzernamen des Workday-Systemintegrationskontos mit angefügtem Mandantendomänennamen ein. Dies sollte ungefähr wie folgt aussehen: username@contoso4

   * **Administratorkennwort**: Geben Sie das Kennwort des Workday-Systemintegrationskontos ein.

   * **Mandanten-URL**: Geben Sie die URL des Workday-Webdienstendpunkts für Ihren Mandanten ein. Diese sollte wie folgt lauten: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources. Dabei wird „contoso4“ durch den Namen Ihres Mandanten und „wd3-impl“ (bei Bedarf) durch die ordnungsgemäße Umgebungszeichenfolge ersetzt.

   * **Benachrichtigungs-E-Mail**: Geben Sie Ihre E-Mail-Adresse ein, und aktivieren Sie das Kontrollkästchen „E-Mail senden, wenn Fehler auftritt“.

   * Klicken Sie auf die Schaltfläche **Verbindung testen**. Wenn der Verbindungstest erfolgreich ist, klicken Sie oben auf die Schaltfläche **Speichern**. Falls nicht, überprüfen Sie, ob die Workday-URL und -Anmeldeinformationen in Workday gültig sind.

### <a name="part-2-configure-attribute-mappings"></a>Teil 2: Konfigurieren von Attributzuordnungen 

In diesem Abschnitt konfigurieren Sie den Fluss von Benutzerdaten aus Workday in Active Directory.

1. Klicken Sie auf der Registerkarte „Bereitstellung“ unter **Zuordnungen** auf **Azure AD-Benutzer in Workday synchronisieren**.

2. Im Feld **Quellobjektbereich** können Sie optional filtern, für welche Gruppen von Benutzern in Azure Active Directory E-Mail-Adressen in Workday zurückgeschrieben werden sollen. Die Standardoption ist „Alle Benutzer in Azure AD“. 

3. Aktualisieren Sie im Abschnitt **Attributzuordnungen** die entsprechende ID, um das Attribut in Azure Active Directory anzugeben, in dem die Mitarbeiter-ID von Workday gespeichert ist. Eine gängige Methode für den Abgleich ist das Synchronisieren der Mitarbeiter-ID von Workday mit „extensionAttribute“ 1-15 in Azure AD und das anschließende Verwenden dieses Attributs in Azure AD, um die Benutzer wieder mit Workday abzugleichen. 

4. Klicken Sie oben im Abschnitt „Attributzuordnung“ auf **Speichern**, um Ihre Zuordnungen zu speichern.

### <a name="part-3-start-the-service"></a>Teil 3: Starten des Dienstes
Sobald Sie die Teile 1 und 2 abgeschlossen haben, können Sie den Bereitstellungsdienst starten.

1. Legen Sie auf der Registerkarte **Bereitstellung** die Einstellung **Bereitstellungsstatus** auf **Ein** fest.

2. Klicken Sie auf **Speichern**.

3. Dadurch wird die erste Synchronisierung gestartet, die abhängig von der Anzahl der Benutzer in Workday eine variable Anzahl von Stunden dauern kann.

4. Einzelne Synchronisierungsereignisse können auf der Registerkarte **Überwachungsprotokolle** angezeigt werden. **[Ausführliche Anweisungen zum Lesen der Überwachungsprotokolle finden Sie in der Anleitung zur Erstellung von Bereitstellungsberichten.](../manage-apps/check-status-user-account-provisioning.md)**

5. Anschließend wird auf der Registerkarte **Bereitstellung** ein Überwachungszusammenfassungsbericht ausgegeben:

## <a name="customizing-the-list-of-workday-user-attributes"></a>Anpassen der Liste der Workday-Benutzerattribute
Die Workday-Bereitstellungs-Apps für Active Directory und Azure AD umfassen jeweils eine Liste mit Workday-Benutzerattributen, aus denen Sie auswählen können. Diese Listen sind jedoch nicht sehr umfangreich. Workday unterstützt mehrere Hundert möglicher Benutzerattribute, die entweder als Standardattribute oder als eindeutige Attribute für Ihren Workday-Mandanten eingerichtet werden können. 

Der Azure AD-Bereitstellungsdienst unterstützt die Möglichkeit, Ihre Liste der Workday-Attribute so anzupassen, dass alle im [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)-Vorgang der Human Resources-API verfügbar gemachten Attribute enthalten sind.

Zu diesem Zweck müssen Sie [Workday Studio](https://community.workday.com/studio-download) verwenden, um die XPath-Ausdrücke zu extrahieren, die die gewünschten Attribute repräsentieren. Anschließend verwenden Sie den erweiterten Attribut-Editor im Azure-Portal, um sie zu Ihrer Bereitstellungskonfiguration hinzuzufügen.

**So rufen Sie einen XPath-Ausdruck für ein Workday-Benutzerattribut ab:**

1. Laden Sie [Workday Studio](https://community.workday.com/studio-download), herunter, und installieren Sie es. Sie benötigen ein Workday-Communitykonto, um auf das Installationsprogramm zuzugreifen.

2. Laden Sie die WDSL-Datei „Workday Human_Resources“ über diese URL herunter: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Starten Sie Workday Studio.

4. Wählen Sie in der Befehlsleiste die Option **Workday > Test Web Service in Tester** (Workday > Webdienst im Tester testen) aus.

5. Wählen Sie **External** und dann die WSDL-Datei „Human_Resources“ aus, die Sie in Schritt 2 heruntergeladen haben.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Legen Sie das Feld **Location** auf `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources` fest, ersetzen Sie jedoch „IMPL-CC“ durch den tatsächlichen Typ Ihrer Instanz und „TENANT“ durch den echten Namen Ihres Mandanten.

7. Legen Sie **Operation** auf **Get_Workers** fest.

8.  Klicken Sie auf den **configure**-Link unterhalb der Bereiche „Request“ (Anforderung) und „Response“ (Antwort), um Ihre Workday-Anmeldeinformationen festzulegen. Aktivieren Sie das Kontrollkästchen **Authentifizierung**, und geben Sie den Benutzernamen und das Kennwort für Ihr Systemkonto für die Workday-Integration ein. Stellen Sie sicher, dass der Benutzername das Format name@tenant aufweist, und behalten Sie die Auswahl der Option **WS-Security UsernameToken** bei.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Klicken Sie auf **OK**.

10. Fügen Sie im Bereich **Request** den unten stehenden XML-Code ein, und legen Sie **Employee_ID** auf die Mitarbeiter-ID eines realen Benutzers in Ihrem Workday-Mandanten fest. Wählen Sie einen Benutzer aus, für den das Attribut aufgefüllt ist, das Sie extrahieren möchten.

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
            <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```
 
11. Klicken Sie auf **Send Request** (Anforderung senden, grüner Pfeil), um den Befehl auszuführen. Bei erfolgreicher Ausführung wird die Antwort im Bereich **Response** angezeigt. Überprüfen Sie die Antwort, um sicherzustellen, dass sie die Daten des eingegebenen Benutzers enthält und fehlerfrei ist.

12. Wenn alles richtig ist, kopieren Sie den XML-Code aus dem Bereich **Response**, und speichern Sie ihn als XML-Datei.

13. Wählen Sie in der Befehlsleiste von Workday Studio **File > Open File...** (Datei > Datei öffnen...) aus, und öffnen Sie die gespeicherte XML-Datei. Die Datei wird im XML-Editor von Workday Studio geöffnet.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. Navigieren Sie in der Dateistruktur durch **/env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker**, um die Daten zu Ihrem Benutzer zu finden. 

15. Suchen Sie unter **wd: Worker** das Attribut, das Sie hinzufügen möchten, und wählen Sie es aus.

16. Kopieren Sie den XPath-Ausdruck für das ausgewählte Attribut aus dem Feld **Document Path** (Dokumentpfad).

1. Entfernen Sie das Präfix **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** aus dem kopierten Ausdruck.

18. Wenn es sich beim letzten Element im kopierten Ausdruck um einen Knoten handelt (z.B. „/wd: Birth_Date“), dann fügen Sie **/text()** an das Ende des Ausdrucks an. Dies ist nicht erforderlich, wenn das letzte Element ein Attribut ist (Beispiel: „/@wd: type“).

19. Das Ergebnis sollte in etwa wie folgt aussehen: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Diese Zeile kopieren Sie in das Azure-Portal.


**So fügen Sie Ihr benutzerdefiniertes Workday-Benutzerattribut zu Ihrer Bereitstellungskonfiguration hinzu:**

1. Starten Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zum Bereitstellungsbereich Ihrer Workday-Bereitstellungsanwendung, wie oben in diesem Tutorial beschrieben.

2. Legen Sie den **Bereitstellungsstatus** auf **Aus** fest, und klicken Sie dann auf **Speichern**. So können Sie sicherstellen, dass Ihre Änderungen erst dann wirksam werden, wenn Sie bereit sind.

3. Wählen Sie unter **Zuordnungen** die Option **Worker mit lokalem System synchronisieren** (oder **Worker mit Azure AD synchronisieren**).

4. Scrollen Sie im nächsten Bildschirm nach unten, und wählen Sie **Erweiterte Optionen anzeigen** aus.

5. Wählen Sie **Attributliste für Workday bearbeiten** aus.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Scrollen Sie zu den Eingabefeldern am Ende der Attributliste.

7. Geben Sie unter **Name** einen Anzeigenamen für Ihr Attribut ein.

8. Wählen Sie einen **Typ** aus, der zu Ihrem Attribut passt (**Zeichenfolge** ist der am häufigsten verwendete Typ).

9. Geben Sie unter **API-Ausdruck** den XPath-Ausdruck ein, den Sie aus Workday Studio kopiert haben. Beispiel: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Wählen Sie **Attribut hinzufügen** aus.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Wählen Sie oben **Speichern** aus, und klicken Sie im angezeigten Dialogfeld auf **Ja**. Schließen Sie den Bildschirm „Attributzuordnung“, falls dieser noch geöffnet ist.

12. Wählen Sie auf der Registerkarte **Bereitstellung** erneut die Option **Worker mit lokalem System synchronisieren** (oder **Worker mit Azure AD synchronisieren**).

13. Wählen Sie **Neue Zuordnung hinzufügen** aus.

14. Das neue Attribut sollte jetzt in der Liste **Quellattribut** angezeigt werden.

15. Fügen Sie wie gewünscht eine Zuordnung für Ihr neues Attribut hinzu.

16. Denken Sie nach Abschluss der Konfiguration daran, den **Bereitstellungsstatus** wieder auf **Ein** festzulegen und zu speichern.

## <a name="known-issues"></a>Bekannte Probleme

* Das Schreiben von Daten in das Benutzerattribut „thumbnailPhoto“ im lokalen Active Directory wird derzeit nicht unterstützt.

* Der Connector „Workday to Azure AD“ wird auf Azure AD-Mandanten, auf denen AAD Connect aktiviert ist, derzeit nicht unterstützt.  

## <a name="managing-personal-data"></a>Verwalten von personenbezogenen Daten

Die Workday-Bereitstellungslösung für Active Directory benötigt einen Synchronisierungs-Agent, der auf einem in die Domäne eingebundenen Server installiert ist. Dieser Agent erstellt Protokolle im Windows-Ereignisprotokoll, die personenbezogene Daten enthalten können.

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../manage-apps/check-status-user-account-provisioning.md)
* [Lesen Sie, wie Sie das einmalige Anmelden zwischen Workday und Azure Active Directory konfigurieren.](workday-tutorial.md)
* [Erfahren Sie, wie Sie andere SaaS-Anwendungen in Azure Active Directory integrieren.](tutorial-list.md)

