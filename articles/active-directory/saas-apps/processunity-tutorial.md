---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą ProcessUnity | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedyncze między Azure Active Directory i ProcessUnity.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/09/2021
ms.author: jeedes
ms.openlocfilehash: bf38a013dbe59fdc4be646264840fe8906d0f11b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520939"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-processunity"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą ProcessUnity

Z tego samouczka dowiesz się, jak zintegrować narzędzie ProcessUnity z Azure Active Directory (Azure AD). Po zintegrowaniu aplikacji ProcessUnity z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do jednostki ProcessUnity.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji ProcessUnity przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji ProcessUnity z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Urządzenie ProcessUnity obsługuje logowanie **jednokrotne inicjowane przez spedycyjną** i za pomocą dostawcy tożsamości.
* Proces ProcessUnity obsługuje **aprowizowanie** użytkowników just in time.

## <a name="add-processunity-from-the-gallery"></a>Dodawanie jednostki ProcessUnity z galerii

Aby skonfigurować integrację aplikacji ProcessUnity z usługą Azure AD, musisz dodać aplikację ProcessUnity z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub osobistego konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W **sekcji Dodaj z galerii** wpisz **ProcessUnity** w polu wyszukiwania.
1. Wybierz **pozycję ProcessUnity** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-processunity"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla jednostki ProcessUnity

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z testem ProcessUnity przy użyciu użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w aplikacji ProcessUnity.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z testem ProcessUnity, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedynczej usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD —](#assign-the-azure-ad-test-user)** aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego w aplikacji ProcessUnity](#configure-processunity-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji ProcessUnity](#create-processunity-test-user)** — aby mieć w aplikacji ProcessUnity odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **integracji aplikacji ProcessUnity** znajdź sekcję **Zarządzanie** i wybierz **pozycję Logowanie pojedynczej.**
1. Na stronie **Select a single sign-on method (Wybieranie** metody logowania pojedynczego) wybierz pozycję **SAML**.
1. Na stronie Konfigurowanie logowania pojedynczego za pomocą **saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym  przez dostawcy tożsamości, w sekcji Podstawowa konfiguracja protokołu **SAML** wprowadź wartości następujących pól:

    a. W polu **tekstowym** Identyfikator wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.processunity.net/<DOMAIN_NAME>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.processunity.net/<DOMAIN_NAME>/SAML/AssertionConsumerServiceV2.aspx`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie **inicjowanym przez** spjęcie:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.processunity.net/<DOMAIN_NAME>/SAML/SamlLoginV2.aspx`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z zespołem pomocy technicznej klienta aplikacji [ProcessUnity.](mailto:customer.support@processunity.com) Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie Konfigurowanie logowania pojedynczego za pomocą języka SAML w sekcji Certyfikat podpisywania  **SAML** znajdź plik **XML** metadanych **federacji** i wybierz pozycję Pobierz, aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W **sekcji Konfigurowanie aplikacji ProcessUnity** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji ProcessUnity.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **ProcessUnity.**
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy** w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-processunity-sso"></a>Konfigurowanie logowania jednokrotnego w aplikacji ProcessUnity

Aby skonfigurować logowanie pojedyncze po stronie aplikacji **ProcessUnity,** musisz wysłać pobrany plik XML metadanych federacji i odpowiednie adresy **URL** skopiowane z aplikacji Azure Portal zespołowi pomocy technicznej aplikacji [ProcessUnity.](mailto:customer.support@processunity.com) Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-processunity-test-user"></a>Tworzenie użytkownika testowego aplikacji ProcessUnity

W tej sekcji w aplikacji ProcessUnity jest tworzony użytkownik o nazwie Britta Simon. Aplikacja ProcessUnity obsługuje aprowizowanie użytkowników just in time, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w jednostce ProcessUnity, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Inicjowane przez sp:

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania ProcessUnity, pod którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania ProcessUnity i zainicjuj przepływ logowania.

#### <a name="idp-initiated"></a>Inicjowane przez IDP:

* Kliknij pozycję **Test this application** in Azure Portal (Przetestuj tę aplikację w aplikacji). Powinno na celu to automatyczne zalogowanie się do aplikacji ProcessUnity, dla której została ustawiona logowanie jednokrotne. 

Możesz również użyć usługi Microsoft Moje aplikacje, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka ProcessUnity w trybie Moje aplikacje, jeśli został skonfigurowany w trybie sp nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie skonfigurowane w trybie IDP, powinno nas automatycznie zalogować się do aplikacji ProcessUnity, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na Moje aplikacje, [zobacz Introduction to the Moje aplikacje (Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu jednostki ProcessUnity można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozszerza zakres dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
