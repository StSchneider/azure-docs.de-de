---
title: Anfordern eines Tokens mit einer Android-Anwendung in Azure Active Directory B2C | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie eine Android-App erstellen, die Appauth mit Azure Active Directory B2C zum Verwalten von Benutzeridentitäten und Authentifizieren von Benutzern verwendet.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a5bf15289e91cc568524e8110702b5608118bc2d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52833923"
---
# <a name="azure-ad-b2c-sign-in-using-an-android-application"></a>Azure AD B2C: Anmelden mit einer Android-Anwendung

Die Microsoft Identity-Plattform nutzt offene Standards, z.B. OAuth2 und OpenID Connect. Diese Standards ermöglichen die Nutzung jeder beliebigen Bibliothek, die Sie in Azure Active Directory B2C integrieren möchten. Zur Vereinfachung der Verwendung anderer Bibliotheken können Sie eine exemplarische Vorgehensweise wie die vorliegende verwenden. Darin wird veranschaulicht, wie Drittanbieterbibliotheken konfiguriert werden, um eine Verbindung mit der Microsoft-Identitätsplattform herzustellen. Die meisten Bibliotheken, die die [RFC6749 OAuth2-Spezifikation](https://tools.ietf.org/html/rfc6749) implementieren, können eine Verbindung mit der Identitätsplattform herstellen.

> [!WARNING]
> Microsoft stellt keine Fehlerbehebungen für diese Drittanbieterbibliotheken bereit und hat diese Bibliotheken keinerlei Prüfungen unterzogen. In diesem Beispiel wird eine Drittanbieterbibliothek namens AppAuth verwendet, deren Kompatibilität in grundlegenden Szenarien mit Azure AD B2C getestet wurde. Probleme und Funktionsanforderungen müssen direkt an das Open Source-Projekt der Bibliothek weitergeleitet werden. Weitere Informationen hierzu finden Sie in [diesem Artikel](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).  
>
>

Wenn OAuth2 oder OpenID Connect neu für Sie ist, ergibt diese Beispielkonfiguration für Sie vielleicht noch nicht viel Sinn. Wir empfehlen Ihnen, sich eine kurze [Übersicht über das Protokoll](active-directory-b2c-reference-protocols.md)anzusehen.

## <a name="get-an-azure-ad-b2c-directory"></a>Erstellen eines Azure AD B2C-Verzeichnisses

Bevor Sie Azure AD B2C verwenden können, müssen Sie ein Verzeichnis oder einen Mandanten erstellen. Ein Verzeichnis ist ein Container für alle Ihre Benutzer, Apps, Gruppen usw. Erstellen Sie, sofern noch nicht geschehen, [ein B2C-Verzeichnis](active-directory-b2c-get-started.md) , bevor Sie fortfahren.

## <a name="create-an-application"></a>Erstellen einer Anwendung

Als Nächstes müssen Sie in Ihrem B2C-Verzeichnis eine App erstellen. Dadurch werden Azure AD die Informationen bereitgestellt, die für die sichere Kommunikation mit Ihrer App erforderlich sind. Befolgen Sie zum Erstellen einer Mobile App [diese Anweisungen](active-directory-b2c-app-registration.md). Führen Sie folgende Schritte aus:

* Fügen Sie einen **nativen Client** in die Anwendung ein.
* Kopieren Sie die **Anwendungs-ID** , die Ihrer App zugewiesen ist. Sie benötigen sie später.
* Richten Sie eine **Umleitungs-URI** mit einem benutzerdefinierten Schema ein (z.B. com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect) Diese benötigen sie später ebenfalls.

## <a name="create-your-user-flows"></a>Erstellen Ihrer Benutzerflows

In Azure AD B2C wird jede Benutzeroberfläche durch einen [Benutzerflow](active-directory-b2c-reference-policies.md) definiert. Hierbei handelt es sich um eine Reihe von Richtlinien, die das Verhalten von Azure AD steuern. Diese App enthält eine Benutzeroberfläche für die Identität: einen kombinierten Benutzerflow für Anmeldung und Registrierung. Sie müssen diesen Benutzerflow wie im [Referenzartikel für Benutzerflows](active-directory-b2c-reference-policies.md#create-a-sign-up-user-flow) beschrieben erstellen. Achten Sie beim Erstellen Ihres Benutzerflows auf Folgendes:

* Wählen Sie den **Anzeigenamen** als Registrierungsattribut in Ihrem Benutzerflow aus.
* Wählen Sie die Anwendungsansprüche **Anzeigenamen** und die **Objekt-ID** in jedem Benutzerflow aus. Sie können auch andere Ansprüche auswählen.
* Notieren Sie sich nach der Erstellung den **Namen** jedes Benutzerflows. Dieser muss das Präfix `b2c_1_` aufweisen.  Der Name des Benutzerflows wird später noch benötigt.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Nachdem Sie Ihre Benutzerflows erstellt haben, können Sie Ihre App erstellen.

## <a name="download-the-sample-code"></a>Herunterladen des Beispielcodes

Wir haben ein funktionsfähiges Beispiel bereitgestellt, das AppAuth mit Azure AD B2C [auf GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c) verwendet. Sie können den Code herunterladen und ausführen. Folgen Sie den Anweisungen in der Datei [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md), um bei den ersten Schritten mit Ihrer eigenen App Ihre eigene Azure AD B2C-Konfiguration zu verwenden.

Das Beispiel ist eine geänderte Version des durch [AppAuth](https://openid.github.io/AppAuth-Android/) bereitgestellten Beispiels. Auf der Seite von AppAuth finden Sie weitere Informationen zu AppAuth und dessen Funktionen.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Ändern Ihrer Anwendung zur Verwendung von Azure AD B2C mit AppAuth

> [!NOTE]
> AppAuth unterstützt Android API 16 (Jellybean) und höher. Wir empfehlen API 23 und höher.
>

### <a name="configuration"></a>Konfiguration

Sie können die Kommunikation mit Azure AD B2C konfigurieren, indem Sie die Ermittlungs-URI oder die Autorisierungsendpunkt- und die Token-Endpunkt-URIs angeben. In beiden Fällen benötigen Sie die folgenden Informationen:

* Mandanten-ID (z.B. contoso.onmicrosoft.com)
* Name des Benutzerflows (z.B. B2C\_1\_SignUpIn)

Falls Sie die Autorisierungs- und Token-Endpunkt-URIs automatisch ermitteln möchten, müssen Sie Informationen aus der Ermittlungs-URI abrufen. Sie können die Ermittlungs-URI generieren, indem Sie Tenant\_ID (Mandanten-ID) und Policy\_Name (Richtlinienname) in der folgenden URL ersetzen:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Sie können dann die Autorisierungs- und Token-Endpunkt-URIs abrufen und ein AuthorizationServiceConfiguration-Objekt erstellen, indem Sie Folgendes ausführen:

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

Anstatt die Autorisierungs- und Token-Endpunkt-URIs per Ermittlung abzurufen, können Sie diese auch explizit durch Ersetzen von Tenant\_ID (Mandanten-ID) und Policy\_Name (Richtlinienname) in der folgenden URL festlegen:

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Führen Sie den folgenden Code aus, um Ihr AuthorizationServiceConfiguration-Objekt zu erstellen:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Autorisieren

Nach dem Konfigurieren oder Abrufen einer Autorisierungsdienstkonfiguration kann eine Autorisierungsanforderung erstellt werden. Zum Erstellen dieser Anforderung benötigen Sie die folgenden Informationen:

* CLIENT-ID (z.B. 00000000-0000-0000-0000-000000000000)
* Umleitungs-URI mit einem benutzerdefinierten Schema (z.B. com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect)

Sie sollten beide Elemente beim [Registrieren Ihrer App](#create-an-application) gespeichert haben.

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Informationen zum restlichen Prozess finden Sie im [AppAuth Handbuch](https://openid.github.io/AppAuth-Android/). Sehen Sie sich [unser Beispiel](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c) für die ersten Schritte mit einer funktionsfähigen App an. Folgen Sie den Anweisungen in der Datei [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) aus, um Ihre eigene Azure AD B2C-Konfiguration einzugeben.

Wir sind stets offen für Feedback und Vorschläge. Wenn Sie Probleme mit diesem Artikel oder Vorschläge zur Verbesserung dieses Inhalts haben, würden wir uns über Ihr Feedback unten auf der Seite freuen. Anforderungen neuer Features geben Sie bitte unter [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) ein.

