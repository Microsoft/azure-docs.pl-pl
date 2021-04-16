---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego z New Relic | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedynczej między Azure Active Directory i New Relic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: jeedes
ms.openlocfilehash: 8c0ffe4affb6b30f2e2a1aa97a0f4795c130f59b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517610"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z New Relic

Z tego samouczka dowiesz się, jak zintegrować usługę New Relic z Azure Active Directory (Azure AD). Po zintegrowaniu aplikacji New Relic z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do New Relic.
* Umożliwianie użytkownikom automatycznego zalogowania się do usługi New Relic przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji: Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne będą następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja New Relic z włączoną obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* New Relic obsługuje logowanie jednokrotne inicjowane przez dostawcę usług lub dostawcę tożsamości.

## <a name="add-new-relic-from-the-gallery"></a>Dodawanie New Relic z galerii

Aby skonfigurować integrację aplikacji New Relic usługą Azure AD, należy dodać aplikację **New Relic (według organizacji)** z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konta osobistego konto Microsoft.
1. Wybierz **Azure Active Directory** usługi.
1. Wybierz pozycję **Aplikacje dla przedsiębiorstw** Nowa  >  **aplikacja.**
1. Na stronie **Przeglądanie galerii usługi Azure AD** wpisz New Relic **(według organizacji)** w polu wyszukiwania.
1. Wybierz **New Relic (Według organizacji)** z wyników, a następnie wybierz pozycję **Utwórz.** Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla New Relic

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD New Relic za pomocą użytkownika testowego **o nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji New Relic.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą New Relic:

1. [Skonfiguruj logowanie jednokrotne usługi Azure AD,](#configure-azure-ad-sso) aby umożliwić użytkownikom korzystanie z tej funkcji.
   1. [Tworzenie użytkownika testowego usługi Azure AD w](#create-an-azure-ad-test-user) celu przetestowania logowania pojedynczego usługi Azure AD z użytkownikiem B.Simon.
   1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. [Skonfiguruj New Relic logowania jednokrotnego,](#configure-new-relic-sso) aby skonfigurować ustawienia logowania jednokrotnego po New Relic jednokrotnego.
   1. [Utwórz użytkownika New Relic testowego,](#create-a-new-relic-test-user) aby mieć w aplikacji New Relic odpowiednik użytkownika B.Simon połączony z użytkownikiem usługi Azure AD.
1. [Przetestuj logowanie jednokrotne,](#test-sso) aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na stronie Azure Portal aplikacji **New Relic według** organizacji znajdź **sekcję** Zarządzanie. Następnie wybierz **pozycję Logowanie pojedyncze.**

1. Na stronie **Select a single sign-on method (Wybieranie metody logowania pojedynczego)** wybierz pozycję **SAML**.

1. Na stronie **Konfigurowanie aplikacji Sign-On saml** wybierz ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający konfigurowanie pojedynczego Sign-On saml z wyróżniona ikoną ołówka.](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wypełnij wartości w polach **Identyfikator i** **Adres URL odpowiedzi.**

   * Pobierz te wartości przy użyciu aplikacji New Relic **Moja organizacja.** Aby użyć tej aplikacji:
      1. [Zaloguj się](https://login.newrelic.com/) do New Relic.
      1. W górnym menu wybierz pozycję **Aplikacje.**
      1. W sekcji **Twoje aplikacje** wybierz pozycję My Organization Authentication domains **(Moje domeny**  >  **uwierzytelniania organizacji).**
      1. Wybierz domenę uwierzytelniania, z którą chcesz połączyć się za pomocą logowania jednokrotnego usługi Azure AD (jeśli masz więcej niż jedną domenę uwierzytelniania). Większość firm ma tylko jedną domenę uwierzytelniania o nazwie **Domyślna.** Jeśli istnieje tylko jedna domena uwierzytelniania, nie trzeba niczego wybierać.
      1. W sekcji **Authentication (Uwierzytelnianie)** wartość Assertion consumer URL (Adres URL użytkownika **asercji)** zawiera wartość do użycia dla adresu URL **odpowiedzi**.
      1. W sekcji **Authentication** (Uwierzytelnianie) **element Our entity ID** (Identyfikator jednostki) zawiera wartość do użycia dla elementu Identifier **(Identyfikator).**

1. W sekcji **Atrybuty użytkownika & oświadczenia** upewnij  się, że unikatowy identyfikator użytkownika jest mapowany na pole zawierające adres e-mail używany w New Relic.

   * Pole domyślne **user.userprincipalname** będzie działać, jeśli jego wartości są takie same jak New Relic e-mail.
   * Pole  **user.mail** może działać lepiej, jeśli **user.userprincipalname** nie jest adresem New Relic e-mail.

1. W sekcji **Certyfikat podpisywania SAML** skopiuj adres **URL** metadanych federacji aplikacji i zapisz jego wartość do późniejszego użycia.

1. W **sekcji Konfigurowanie New Relic według organizacji** skopiuj adres **URL** logowania i zapisz jego wartość do późniejszego użycia.

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

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji New Relic.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **New Relic**.
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy w** **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie została ustawiona żadna rola, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-new-relic-sso"></a>Konfigurowanie New Relic jednokrotnego

Wykonaj następujące kroki, aby skonfigurować logowanie jednokrotne na New Relic.

1. [Zaloguj się](https://login.newrelic.com/) do New Relic.

1. W górnym menu wybierz pozycję **Aplikacje.**

1. W sekcji **Twoje aplikacje** wybierz pozycję My Organization Authentication domains **(Moje domeny**  >  **uwierzytelniania organizacji).**

1. Wybierz domenę uwierzytelniania, z którą ma się łączyć logowanie jednokrotne usługi Azure AD (jeśli masz więcej niż jedną domenę uwierzytelniania). Większość firm ma tylko jedną domenę uwierzytelniania o nazwie **Domyślne.** Jeśli istnieje tylko jedna domena uwierzytelniania, nie trzeba niczego wybierać.

1. W sekcji **Uwierzytelnianie** wybierz pozycję **Konfiguruj.**

   1. W **polu Źródło metadanych SAML** wprowadź wcześniej zapisaną wartość z pola Adres **URL** metadanych federacji aplikacji usługi Azure AD.

   1. W **polu Docelowy adres URL logowania** jednokrotnego wprowadź wcześniej zapisaną wartość z pola Adres URL logowania **usługi** Azure AD.

   1. Po sprawdzeniu, czy ustawienia wyglądają dobrze zarówno po stronie usługi Azure AD, jak i New Relic, wybierz pozycję **Zapisz.** Jeśli obie strony nie są prawidłowo skonfigurowane, użytkownicy nie będą mogli zalogować się do New Relic.

### <a name="create-a-new-relic-test-user"></a>Tworzenie użytkownika New Relic testowego

W tej sekcji utworzysz użytkownika o nazwie B.Simon w New Relic.

1. [Zaloguj się](https://login.newrelic.com/) do New Relic.

1. W górnym menu wybierz pozycję **Aplikacje.**

1. W sekcji **Twoje aplikacje** wybierz pozycję **Zarządzanie użytkownikami.**

1. Wybierz pozycję **Dodaj użytkownika**.

   1. W **nazwa**, wprowadź **B.Simon**.
   
   1. W **przypadku** adresu e-mail wprowadź wartość, która zostanie wysłana przez logowanie jednokrotne usługi Azure AD.
   
   1. Wybierz typ **użytkownika** i **grupę** użytkowników dla użytkownika. W przypadku użytkownika testowego **opcje Podstawowy użytkownik** dla typu i **Użytkownik** w grupie są rozsądnymi wyborami.
   
   1. Aby zapisać użytkownika, wybierz pozycję **Dodaj użytkownika.**

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Inicjowane przez sp:

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do New Relic URL logowania, pod którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio New Relic url logowania i zainicjuj przepływ logowania.

#### <a name="idp-initiated"></a>Inicjowane przez IDP:

* Kliknij pozycję **Test this application** in Azure Portal (Przetestuj tę aplikację w aplikacji). Powinno na New Relic zostać automatycznie zalogowane logowanie jednokrotne, dla którego została ustawiona logowanie jednokrotne. 

Możesz również użyć usługi Microsoft Moje aplikacje, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka New Relic w trybie Moje aplikacje, jeśli zostanie skonfigurowany w trybie sp nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli jest skonfigurowany w trybie dostawcy tożsamości, powinno na celu automatyczne zalogowanie się do usługi New Relic, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu New Relic można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozszerza zakres dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
