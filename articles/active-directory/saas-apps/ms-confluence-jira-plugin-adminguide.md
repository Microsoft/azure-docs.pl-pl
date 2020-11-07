---
title: Podręcznik administratora Atlassian JIRA/Confluence-Azure Active Directory | Microsoft Docs
description: Przewodnik administratora do korzystania z Atlassian JIRA i Confluence z usługą Azure Active Directory (Azure AD).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 8e73ea3650e631bed277ab95092b714eef7596d4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359161"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Atlassian JIRA i Podręcznik administratora Confluence dla Azure Active Directory

## <a name="overview"></a>Omówienie

Wtyczka Logowanie jednokrotne w usłudze Azure Active Directory (Azure AD) umożliwia Microsoft Azure AD klientom korzystanie z konta służbowego do logowania się do produktów Atlassian JIRA i Confluence na serwerach. Implementuje Logowanie jednokrotne w oparciu o protokół SAML 2,0.

## <a name="how-it-works"></a>Jak to działa

Gdy użytkownicy chcą zalogować się do aplikacji Atlassian JIRA lub Confluence, zobaczysz przycisk **Zaloguj przy użyciu usługi Azure AD** na stronie logowania. Po ich wybraniu wymagane jest zalogowanie się przy użyciu strony logowania organizacji usługi Azure AD (czyli konta służbowego).

Po uwierzytelnieniu użytkownicy powinni mieć możliwość zalogowania się do aplikacji. Jeśli są już uwierzytelniane przy użyciu identyfikatora i hasła dla konta służbowego, wówczas logują się oni bezpośrednio do aplikacji. 

Logowanie działa w JIRA i Confluence. Jeśli użytkownicy są zalogowani do aplikacji JIRA, a Confluence jest otwarty w tym samym oknie przeglądarki, nie muszą podawać poświadczeń dla innej aplikacji. 

Użytkownicy mogą również uzyskać dostęp do produktu Atlassian za pomocą aplikacji Moje aplikacje w ramach konta służbowego. Należy zalogować się bez monitowania o poświadczenia.

> [!NOTE]
> Inicjowanie obsługi użytkowników nie odbywa się za pomocą wtyczki.

## <a name="audience"></a>Grupy odbiorców

Administratorzy JIRA i Confluence mogą używać wtyczki, aby włączyć logowanie jednokrotne za pomocą usługi Azure AD.

## <a name="assumptions"></a>Założenia

* Wystąpienia JIRA i Confluence są włączone przy użyciu protokołu HTTPS.
* Użytkownicy są już utworzeni w JIRA lub Confluence.
* Użytkownicy mają role przypisane w JIRA lub Confluence.
* Administratorzy mają dostęp do informacji wymaganych do skonfigurowania wtyczki.
* JIRA lub Confluence jest również dostępna poza siecią firmową.
* Wtyczka współpracuje tylko z lokalną wersją programu JIRA i Confluence.

## <a name="prerequisites"></a>Wymagania wstępne

Przed zainstalowaniem wtyczki należy zwrócić uwagę na następujące informacje:

* JIRA i Confluence są zainstalowane w wersji systemu Windows 64-bitowej.
* Wersje JIRA i Confluence są włączone przy użyciu protokołu HTTPS.
* JIRA i Confluence są dostępne w Internecie.
* Poświadczenia administratora są stosowane dla JIRA i Confluence.
* Poświadczenia administratora dla usługi Azure AD.
* WebSudo jest wyłączona w JIRA i Confluence.

## <a name="supported-versions-of-jira-and-confluence"></a>Obsługiwane wersje programu JIRA i Confluence

Wtyczka obsługuje następujące wersje JIRA i Confluence:

* JIRA rdzeń i oprogramowanie: od 6,0 do 7,12
* JIRA Service Desk: 3.0.0 do 3.5.0
* Usługa JIRA obsługuje także wersję 5.2. Aby uzyskać więcej szczegółowych informacji, kliknij następujący link: [logowanie jednokrotne usługi Microsoft Azure Active Directory dla usługi JIRA 5.2](./jira52microsoft-tutorial.md)
* Confluence: 5,0 do 5,10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

## <a name="installation"></a>Instalacja

Aby zainstalować wtyczkę, wykonaj następujące kroki:

1. Zaloguj się do swojego wystąpienia JIRA lub Confluence jako administrator.

2. Przejdź do konsoli administracyjnej JIRA/Confluence i wybierz pozycję **Dodatki**.

3. W centrum pobierania Microsoft Pobierz [wtyczkę usługi Microsoft SAML SSO dla JIRA](https://www.microsoft.com/download/details.aspx?id=56506) /  [Microsoft SAML SSO plugin for Confluence](https://www.microsoft.com/download/details.aspx?id=56503).

   W wynikach wyszukiwania zostanie wyświetlona odpowiednia wersja wtyczki.

4. Wybierz wtyczkę, a Menedżer uniwersalnej wtyczki (UPM) zainstaluje ją.

Gdy wtyczka jest zainstalowana, zostanie wyświetlona w sekcji **dodatków zainstalowanych użytkowników** w temacie **Zarządzanie dodatkami**.

## <a name="plug-in-configuration"></a>Konfiguracja wtyczki

Przed rozpoczęciem korzystania z wtyczki należy ją skonfigurować. Wybierz wtyczkę, wybierz przycisk **Konfiguruj** i podaj szczegóły konfiguracji.

Na poniższej ilustracji przedstawiono ekran konfiguracji zarówno w JIRA, jak i w Confluence:

![Ekran konfiguracji wtyczki](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **Adres URL metadanych** : adres URL umożliwiający uzyskiwanie metadanych Federacji z usługi Azure AD.

* **Identyfikatory** : adres URL, którego usługa Azure AD używa do weryfikacji źródła żądania. Mapuje do elementu **identyfikatora** w usłudze Azure AD. Wtyczka automatycznie dziedziczy ten adres URL jako https:// *\<domain:port>* /.

* **Adres URL odpowiedzi** : adres URL odpowiedzi w dostawcy tożsamości (dostawcy tożsamości), który inicjuje Logowanie przy użyciu protokołu SAML. Jest on mapowany na element **adresu URL odpowiedzi** w usłudze Azure AD. Wtyczka automatycznie dziedziczy ten adres URL jako https:// *\<domain:port>* /plugins/servlet/SAML/auth.

* **Adres URL logowania** : adres URL logowania w dostawcy tożsamości, który inicjuje Logowanie przy użyciu protokołu SAML. Jest on mapowany na element **Sign on** w usłudze Azure AD. Wtyczka automatycznie dziedziczy ten adres URL jako https:// *\<domain:port>* /plugins/servlet/SAML/auth.

* **Identyfikator jednostki dostawcy tożsamości** : identyfikator jednostki używany przez dostawcy tożsamości. To pole jest wypełniane, gdy adres URL metadanych zostanie rozwiązany.

* **Adres URL logowania** : adres URL logowania z dostawcy tożsamości. To pole jest wypełniane w usłudze Azure AD, gdy adres URL metadanych zostanie rozwiązany.

* **Adres URL wylogowywania** : adres URL wylogowania z dostawcy tożsamości. To pole jest wypełniane w usłudze Azure AD, gdy adres URL metadanych zostanie rozwiązany.

* **Certyfikat x. 509** : certyfikat x. 509 dostawcy tożsamości. To pole jest wypełniane w usłudze Azure AD, gdy adres URL metadanych zostanie rozwiązany.

* **Nazwa przycisku logowania** : nazwa przycisku logowania, który organizacja chce, aby użytkownicy mogli zobaczyć na stronie logowania.

* **Lokalizacje identyfikatora użytkownika SAML** : lokalizacja, w której jest oczekiwany identyfikator użytkownika JIRA lub Confluence w odpowiedzi SAML. Może być w **NameID** lub w nazwie atrybutu niestandardowego.

* **Nazwa atrybutu** : nazwa atrybutu, w którym Oczekiwano identyfikatora użytkownika.

* **Włącz odnajdowanie obszaru macierzystego** : wybór, który ma być używany, jeśli firma korzysta z logowania opartego na Active Directory Federation Services (AD FS).

* **Nazwa domeny** : nazwa domeny, jeśli logowanie odbywa się na podstawie AD FS.

* **Włącz pojedynczy wylogowaniu** : wybór do wylogowania z usługi Azure AD, gdy użytkownik wyloguje się z JIRA lub Confluence.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* **Otrzymujesz wiele błędów certyfikatów** : Zaloguj się do usługi Azure AD i Usuń wiele certyfikatów, które są dostępne dla aplikacji. Upewnij się, że istnieje tylko jeden certyfikat.

* **Certyfikat niedługo wygaśnie w usłudze Azure AD** : Dodatki zadbają o automatyczne Przerzucanie certyfikatu. Gdy certyfikat wkrótce wygaśnie, należy oznaczyć nowy certyfikat jako aktywny i nieużywane certyfikaty powinny zostać usunięte. Gdy użytkownik próbuje zalogować się do JIRA w tym scenariuszu, wtyczka pobiera i zapisuje nowy certyfikat.

* **Chcesz wyłączyć WebSudo (Wyłącz sesję administratora zabezpieczeń)** :

  * W przypadku usługi JIRA są domyślnie włączone bezpieczne sesje administratora (czyli potwierdzenie hasła przed uzyskaniem dostępu do funkcji administracyjnych). Jeśli chcesz usunąć tę możliwość w wystąpieniu programu JIRA, określ następujący wiersz w pliku JIRA-config. Properties: `jira.websudo.is.disabled = true`

  * W przypadku Confluence postępuj zgodnie z instrukcjami w [witrynie obsługi Confluence](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Pola, które mają zostać wypełnione przez adres URL metadanych, nie są wypełniane** :

  * Sprawdź, czy adres URL jest poprawny. Sprawdź, czy zamapowano prawidłowy identyfikator dzierżawy i aplikacji.

  * Wprowadź adres URL w przeglądarce i sprawdź, czy jest wyświetlany plik XML metadanych Federacji.

* **Wystąpił wewnętrzny błąd serwera** : Zapoznaj się z dziennikami w katalogu dziennika instalacji. Jeśli wystąpi błąd podczas próby zalogowania się przy użyciu logowania jednokrotnego w usłudze Azure AD, możesz udostępnić dzienniki zespołowi pomocy technicznej.

* Wystąpił **błąd "nie znaleziono identyfikatora użytkownika", gdy użytkownik próbuje się zalogować** : Utwórz identyfikator użytkownika w JIRA lub Confluence.

* Wystąpił **błąd "nie znaleziono aplikacji" w usłudze Azure AD** : Sprawdź, czy odpowiedni adres URL jest mapowany do aplikacji w usłudze Azure AD.

* **Potrzebujesz pomocy technicznej** : skontaktuj się z [zespołem integracji rejestracji jednokrotnej usługi Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Zespół odpowiada za 24-48 godzin pracy.

  Możesz również zgłosić bilet pomocy technicznej w firmie Microsoft za pośrednictwem kanału Azure Portal.

## <a name="plug-in-faq"></a>Wtyczka często zadawane pytania

Zapoznaj się z poniższymi pytaniami, jeśli masz jakieś zapytania dotyczące tej wtyczki.

### <a name="what-does-the-plug-in-do"></a>Co to jest wtyczka?

Wtyczka udostępnia funkcję logowania jednokrotnego (SSO, Single Sign-on) dla Atlassian JIRA (w tym JIRA Core, JIRA Software, JIRA Service Desk) i Confluence oprogramowanie lokalne. Wtyczka współpracuje z usługą Azure Active Directory (Azure AD) jako dostawcą tożsamości (dostawcy tożsamości).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Które produkty Atlassianą współpracuje z wtyczką?

Wtyczka współpracuje z lokalnymi wersjami JIRA i Confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Czy wtyczka współpracuje z wersjami w chmurze?

Nie. Wtyczka obsługuje tylko lokalne wersje systemów JIRA i Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Które wersje programu JIRA i Confluence obsługują wtyczkę?

Wtyczka obsługuje następujące wersje:

* JIRA rdzeń i oprogramowanie: od 6,0 do 7,12
* JIRA Service Desk: 3.0.0 do 3.5.0
* Usługa JIRA obsługuje także wersję 5.2. Aby uzyskać więcej szczegółowych informacji, kliknij następujący link: [logowanie jednokrotne usługi Microsoft Azure Active Directory dla usługi JIRA 5.2](./jira52microsoft-tutorial.md)
* Confluence: 5,0 do 5,10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>Czy wtyczka jest bezpłatna czy płatna?

Jest to bezpłatny dodatek.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Czy muszę ponownie uruchomić JIRA lub Confluence po wdrożeniu wtyczki?

Nie jest wymagane ponowne uruchomienie komputera. Możesz zacząć od razu korzystać z wtyczki.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Jak mogę uzyskać pomoc techniczną dla wtyczki?

Aby uzyskać pomoc techniczną dotyczącą tej wtyczki, możesz skontaktować się z [zespołem integracji usługi Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) . Zespół odpowiada za 24-48 godzin pracy.

Możesz również zgłosić bilet pomocy technicznej w firmie Microsoft za pośrednictwem kanału Azure Portal.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Czy wtyczka będzie działała na komputerach Mac lub Ubuntu instalacji JIRA i Confluence?

Wtyczka została przetestowana tylko na 64-bitowych instalacjach systemu Windows Server z JIRA i Confluence.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Czy wtyczka współpracuje z usługą dostawców tożsamości inną niż usługa Azure AD?

Nie. Działa tylko z usługą Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>W jakiej wersji protokołu SAML działa wtyczka?

Działa z użyciem protokołu SAML 2,0.

### <a name="does-the-plug-in-do-user-provisioning"></a>Czy wtyczka wykonuje aprowizacji użytkowników?

Nie. Wtyczka zapewnia tylko Logowanie jednokrotne oparte na protokole SAML 2,0. Użytkownik musi być zainicjowany w aplikacji przed logowaniem jednokrotnym.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Czy wtyczka obsługuje klastry wersji JIRA i Confluence?

Nie. Wtyczka współpracuje z lokalnymi wersjami JIRA i Confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Czy wtyczka współpracuje z wersjami HTTP JIRA i Confluence?

Nie. Wtyczka współpracuje tylko z instalacjami z obsługą protokołu HTTPS.