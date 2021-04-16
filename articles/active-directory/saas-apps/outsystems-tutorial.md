---
title: 'Samouczek: Azure Active Directory integracji z usługą Azure AD OutSystems | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedyncze między usługami Azure Active Directory i OutSystems w usłudze Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 6a4a2f4edb3fcc56bafb3847280958a7803ba3bd
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519905"
---
# <a name="tutorial-integrate-outsystems-azure-ad-with-azure-active-directory"></a>Samouczek: integrowanie usługi OutSystems usługi Azure AD z usługą Azure Active Directory

Z tego samouczka dowiesz się, jak zintegrować usługę OutSystems azure AD z usługą Azure Active Directory (Azure AD). Integracja usługi Azure AD OutSystems z usługą Azure AD umożliwia:

* Kontroluj w usłudze Azure AD, kto ma dostęp do usługi OutSystems Azure AD.
* Umożliwianie użytkownikom automatycznego zalogowania się do usługi OutSystems usługi Azure AD przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji OutSystems z obsługą logowania jednokrotnego (SSO) usługi Azure AD.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa OutSystems Azure AD obsługuje logowanie jednokrotne **inicjowane** przez spedycyjną i IDP oraz **aprowizowanie** użytkowników just in time.

## <a name="add-outsystems-azure-ad-from-the-gallery"></a>Dodawanie usługi OutSystems usługi Azure AD z galerii

Aby skonfigurować integrację usługi Azure AD OutSystems z usługą Azure AD, należy dodać usługę OutSystems Azure AD z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub osobistego konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W sekcji **Dodaj z galerii** wpisz **OutSystems Azure AD** w polu wyszukiwania.
1. Wybierz **pozycję OutSystems Azure AD** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-outsystems-azure-ad"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla outSystems Azure AD

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z usługą OutSystems Azure AD przy użyciu użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Azure AD OutSystems.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z usługą Azure AD OutSystems, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedynczej usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD —](#assign-the-azure-ad-test-user)** aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD OutSystems](#configure-outsystems-azure-ad-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego](#create-outsystems-azure-ad-test-user)** usługi Azure AD w systemie OutSystems — aby mieć w usłudze Azure AD outSystems odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W witrynie Azure Portal na stronie integracji aplikacji **OutSystems**  usługi Azure AD znajdź sekcję Zarządzanie i wybierz **pozycję Logowanie pojedyncze.**
1. Na stronie **Select a Single sign-on method (Wybieranie** metody logowania pojedynczego) wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie aplikacji Sign-On saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym  przez dostawcy tożsamości, w sekcji Podstawowa konfiguracja protokołu **SAML** wykonaj następujące kroki:

    a. W polu **tekstowym** Identyfikator wpisz adres URL, korzystając z następującego wzorca: `http://<YOURBASEURL>/IdP`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<YOURBASEURL>/IdP/SSO.aspx`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie **inicjowanym przez** spjęcie:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<YOURBASEURL>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się [z zespołem pomocy technicznej klienta OutSystems,](mailto:support@outsystems.com) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie Konfigurowanie usługi single Sign-On za pomocą języka SAML w  sekcji Certyfikat **podpisywania** **SAML** znajdź plik **XML** metadanych federacji i wybierz pozycję Pobierz, aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W **sekcji Konfigurowanie outSystems usługi Azure AD** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do usługi Azure AD OutSystems.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **OutSystems Azure AD.**
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy** w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-outsystems-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD w systemie OutSystems

Aby skonfigurować logowanie pojedyncze po stronie outSystems, musisz pobrać składnik [forge idP](https://www.outsystems.com/forge/component-overview/599/idp) i skonfigurować go zgodnie z [instrukcjami](https://success.outsystems.com/Documentation/Development_FAQs/How_to_configure_OutSystems_to_use_identity_providers_using_SAML#Configure_your_application_to_use_IdP_connector). Po zainstalowaniu składnika i wykonuj niezbędne zmiany kodu, skonfiguruj usługę Azure AD, pobierając kod XML metadanych federacji z usługi Azure Portal i przekaż go do składnika outSystems IdP, zgodnie z poniższymi [instrukcjami.](https://success.outsystems.com/Documentation/Development_FAQs/How_to_configure_OutSystems_to_use_identity_providers_using_SAML#Azure_AD_.2F_ADFS)

### <a name="create-outsystems-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD OutSystems

W tej sekcji w systemie OutSystems jest tworzony użytkownik o nazwie B.Simon. System OutSystems obsługuje aprowizowanie użytkowników just in time, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w systemach OutSystems, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Inicjowane przez sp:

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania usługi Azure AD OutSystems, pod którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania usługi Azure AD OutSystems i zainicjuj przepływ logowania.

#### <a name="idp-initiated"></a>Inicjowane przez IDP:

* Kliknij pozycję **Test this application** in Azure Portal and you should be automatically signed in to the OutSystems Azure AD which you set up the SSO (Przetestuj tę aplikację w usłudze Azure AD). Powinno nas to oznaczać, że logowanie jednokrotne powinno zostać automatycznie zalogowane do usługi Azure AD. 

Możesz również użyć usługi Microsoft Moje aplikacje, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka OutSystems usługi Azure AD w usłudze Moje aplikacje, jeśli został skonfigurowany w trybie sp, nastąpi przekierowanie do strony logowania aplikacji w celu zainicjowania przepływu logowania, a jeśli skonfigurowano je w trybie dostawcy tożsamości, powinno na celu automatyczne zalogowanie się do usługi Azure AD OutSystems, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na Moje aplikacje, [zobacz Introduction to the Moje aplikacje (Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi Azure AD OutSystems można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozszerza zakres dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
