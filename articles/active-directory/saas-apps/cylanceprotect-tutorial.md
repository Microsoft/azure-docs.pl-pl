---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą CylancePROTECT | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją CylancePROTECT.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: a5daf83346db901288d5bc614f341c354d0c008f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481348"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cylanceprotect"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą CylancePROTECT

Z tego samouczka dowiesz się, jak zintegrować program CylancePROTECT z Azure Active Directory (Azure AD). Po zintegrowaniu aplikacji CylancePROTECT z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do aplikacji CylancePROTECT.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji CylancePROTECT przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji CylancePROTECT z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* CylancePROTECT obsługuje logowanie **jednokrotne inicjowane** przez dostawcy tożsamości.

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc w jednej dzierżawie można skonfigurować tylko jedno wystąpienie.

## <a name="add-cylanceprotect-from-the-gallery"></a>Dodawanie aplikacji CylancePROTECT z galerii

Aby skonfigurować integrację aplikacji CylancePROTECT z usługą Azure AD, musisz dodać aplikację CylancePROTECT z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub osobistego konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W **sekcji Dodaj z galerii** wpisz **CylancePROTECT** w polu wyszukiwania.
1. Wybierz **pozycję CylancePROTECT** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-cylanceprotect"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji CylancePROTECT

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z użyciem aplikacji CylancePROTECT, korzystając z użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji CylancePROTECT.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z ochroną CylancePROTECT, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedynczej usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD —](#assign-the-azure-ad-test-user)** aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego w aplikacji CylancePROTECT](#configure-cylanceprotect-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji CylancePROTECT](#create-cylanceprotect-test-user)** — aby mieć w aplikacji CylancePROTECT odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W aplikacji Azure Portal na **stronie integracji aplikacji CylancePROTECT** znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie pojedynczej.**
1. Na stronie **Select a single sign-on method (Wybieranie** metody logowania pojedynczego) wybierz pozycję **SAML**.
1. Na stronie Konfigurowanie logowania pojedynczego za pomocą **saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **stronie Konfigurowanie logowania pojedynczego za pomocą saml** wprowadź wartości następujących pól:

    a. W polu **tekstowym** Identyfikator wpisz jeden z następujących adresów URL:
    
    | Region | Wartość adresu URL |
    |----------|---------|
    | Azja i Pacyfik północno-wschodnie (APNE1)| `https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Azja i Pacyfik południowo-wschodnie (AU) | `https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml` |
    | Europa Środkowa (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Ameryka Północna|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Ameryka Południowa (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

    b. W polu **tekstowym Adres URL** odpowiedzi wpisz jeden z następujących adresów URL:
    
    | Region | Wartość adresu URL |
    |----------|---------|
    | Azja i Pacyfik północno-wschodnie (APNE1)|`https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Azja i Pacyfik południowo-wschodnie (AU)|`https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Europa Środkowa (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Ameryka Północna|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Ameryka Południowa (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

1. Aplikacja CylancePROTECT oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu JĘZYKA SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja CylancePROTECT oczekuje, że **atrybut nameidentifier** zostanie zamapowany na **user.mail** i usunie wszystkie  pozostałe oświadczenia, dlatego należy edytować mapowanie atrybutów, klikając ikonę Edytuj i zmienić mapowanie atrybutów.

    ![image (obraz)](common/edit-attribute.png)

1. Na stronie Konfigurowanie logowania pojedynczego za pomocą saml w sekcji Certyfikat podpisywania  **SAML** znajdź pozycję Certyfikat  **(Base64)** i wybierz pozycję Pobierz, aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W **sekcji Konfigurowanie aplikacji CylancePROTECT** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

> [!NOTE]
> Otwórz pobrany certyfikat zakodowany w formacie Base64 w edytorze tekstów i skopiuj TYLKO tekst między tagami **START** i **END,** aby wkleić go w portalu administracyjnym aplikacji Cylance.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w aplikacji Azure Portal B.Simon.

1. W okienku po lewej stronie w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy,** a następnie wybierz pozycję **Wszyscy użytkownicy.**
1. Wybierz **pozycję Nowy** użytkownik w górnej części ekranu.
1. We **właściwościach** Użytkownika wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **polu Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji CylancePROTECT.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **CylancePROTECT**.
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy** w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-cylanceprotect-sso"></a>Konfigurowanie logowania jednokrotnego aplikacji CylancePROTECT

Aby skonfigurować logowanie pojedyncze po stronie aplikacji **CylancePROTECT,** musisz wysłać pobrany certyfikat **(Base64)** i odpowiednie adresy URL skopiowane z aplikacji Azure Portal do zespołu pomocy technicznej aplikacji [CylancePROTECT.](https://www.cylance.com/en-us/resources/support/support-overview.html) Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach. Aby uzyskać więcej informacji, skorzystaj z dokumentacji języka Cylance: [https://support.cylance.com/s/](https://support.cylance.com/s/) .

### <a name="create-cylanceprotect-test-user"></a>Tworzenie użytkownika testowego aplikacji CylancePROTECT

W tej sekcji utworzysz użytkownika Britta Simon w aplikacji CylancePROTECT. Aby dodać użytkowników na platformie CylancePROTECT, skontaktuj się z administratorem konsoli. Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem, którego użyje w celu potwierdzenia konta, zanim stanie się ono aktywne.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Test this application in Azure Portal (Przetestuj tę aplikację w aplikacji). Powinno na celu to automatyczne zalogowanie się do aplikacji CylancePROTECT, dla której ustawiono logowanie jednokrotne.

* Możesz użyć usługi Microsoft Moje aplikacje. Po kliknięciu kafelka CylancePROTECT w aplikacji Moje aplikacje powinno na celu automatyczne zalogowanie się do aplikacji CylancePROTECT, dla której ustawiono logowanie jednokrotne. Aby uzyskać więcej informacji na Moje aplikacje, [zobacz Introduction to the Moje aplikacje (Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu aplikacji CylancePROTECT można wymusić kontrolę sesji, która chroni eksfiltrację i przeniknięcie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozszerza zakres dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
