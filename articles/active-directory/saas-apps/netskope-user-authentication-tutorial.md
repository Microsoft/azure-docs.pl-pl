---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą Netskope User Authentication | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedyncze między usługą Azure Active Directory i uwierzytelnianiem użytkowników netskope.
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
ms.openlocfilehash: 78ef49740c3e74c4ad5a30838d9721969ca70b36
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517001"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z uwierzytelnianiem użytkowników netskope

Z tego samouczka dowiesz się, jak zintegrować uwierzytelnianie użytkowników netskope z usługą Azure Active Directory (Azure AD). Po zintegrowaniu uwierzytelniania użytkowników netskope z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do uwierzytelniania użytkowników netskope.
* Umożliwianie użytkownikom automatycznego zalogowania się do uwierzytelniania użytkowników netskope przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji Netskope User Authentication z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Uwierzytelnianie użytkowników netskope obsługuje **logowanie jednokrotne inicjowane przez SP i IDP.**

## <a name="add-netskope-user-authentication-from-the-gallery"></a>Dodawanie uwierzytelniania użytkowników netskope z galerii

Aby skonfigurować integrację uwierzytelniania użytkowników netskope z usługą Azure AD, należy dodać uwierzytelnianie użytkowników Netskope z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub osobistego konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W sekcji **Dodaj z galerii** wpisz **Netskope User Authentication** w polu wyszukiwania.
1. Wybierz **pozycję Netskope User Authentication** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-netskope-user-authentication"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD na platformie Netskope User Authentication

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z uwierzytelnianiem użytkownika netskope przy użyciu użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem usługi Netskope User Authentication.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z uwierzytelnianiem użytkowników netskope, wykonaj czynności z poniższych bloków konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedynczej usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD —](#assign-the-azure-ad-test-user)** aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego w aplikacji Netskope User Authentication](#configure-netskope-user-authentication-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego usługi Netskope User Authentication](#create-netskope-user-authentication-test-user)** — aby mieć w aplikacji Netskope User Authentication odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W sekcji Azure Portal na **stronie integracji aplikacji Netskope**  User Authentication znajdź sekcję Zarządzanie i wybierz pozycję **Logowanie pojedynczej.**
1. Na stronie **Select a single sign-on method (Wybieranie** metody logowania pojedynczego) wybierz pozycję **SAML**.
1. Na stronie Konfigurowanie logowania pojedynczego za pomocą **saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym  przez dostawcy tożsamości, w sekcji Podstawowa konfiguracja protokołu **SAML** wprowadź wartości następujących pól:

    a. W polu **tekstowym** Identyfikator wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.goskope.com/<customer entered string>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Te wartości zostaną wyjaśnione w dalszej części tego samouczka.

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie **inicjowanym przez** sp. z o.o.:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Wartości adresu URL logowania nie są prawdziwe. Zaktualizuj wartość adresu URL logowania przy użyciu rzeczywistego adresu URL logowania. Skontaktuj się z zespołem pomocy technicznej klienta uwierzytelniania użytkowników [netskope,](mailto:support@netskope.com) aby uzyskać wartość adresu URL logowania. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W **sekcji Konfigurowanie uwierzytelniania użytkowników netskope** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do uwierzytelniania użytkowników netskope.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **Netskope User Authentication**.
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy** w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-netskope-user-authentication-sso"></a>Konfigurowanie logowania jednokrotnego w aplikacji Netskope User Authentication

1. Otwórz nową kartę w przeglądarce i zaloguj się do firmowej witryny usługi Netskope User Authentication jako administrator.

1. Kliknij **kartę Aktywna platforma.**

    ![Zrzut ekranu przedstawia aktywną platformę wybraną z opcji Ustawienia.](./media/netskope-user-authentication-tutorial/user-1.png)

1. Przewiń w dół do **serwera PROXY PRZESYŁANIA DALEJ** i wybierz pozycję **SAML.**

    ![Zrzut ekranu przedstawia saml wybrany z aktywnej platformy.](./media/netskope-user-authentication-tutorial/configuration.png)

1. Na stronie **Ustawienia SAML** wykonaj następujące kroki:

    ![Zrzut ekranu przedstawiający ustawienia saml, w których można wprowadzić opisane wartości.](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Skopiuj wartość SAML Entity ID (Identyfikator jednostki **SAML)** i wklej ją w polu **tekstowym Identifier** (Identyfikator) w sekcji **Podstawowa konfiguracja języka SAML** w Azure Portal.

    b. Skopiuj **wartość SAML ACS URL i** wklej ją w polu tekstowym Adres **URL** odpowiedzi w sekcji Podstawowa **konfiguracja saml** w Azure Portal.

1. Kliknij **pozycję ADD ACCOUNT (DODAJ KONTO).**

    ![Zrzut ekranu przedstawia wybraną pozycję DODAJ KONTO w okienku SAML.](./media/netskope-user-authentication-tutorial/add-account.png)

1. Na stronie **Dodawanie konta SAML** wykonaj następujące kroki:

    ![Zrzut ekranu przedstawiający dodawanie konta SAML, w którym można wprowadzić opisane wartości.](./media/netskope-user-authentication-tutorial/configure-settings.png)

    a. W polu **tekstowym NAME** (NAZWA) podaj nazwę, na przykład Azure AD.

    b. W polu **tekstowym IDP URL** (Adres URL dostawcy tożsamości) wklej wartość adresu **URL** logowania skopiowaną z Azure Portal.

    c. W polu **tekstowym IDP ENTITY ID** (Identyfikator jednostki dostawcy tożsamości) wklej wartość identyfikatora usługi **Azure AD** skopiowaną z Azure Portal.

    d. Otwórz pobrany plik metadanych w Notatniku, skopiuj jego zawartość do schowka, a następnie wklej ją w polu **tekstowym CERTYFIKAT DOSTAWCY** TOŻSAMOŚCI.

    e. Kliknij **przycisk SAVE (ZAPISZ).**

### <a name="create-netskope-user-authentication-test-user"></a>Tworzenie użytkownika testowego uwierzytelniania użytkowników netskope

1. Otwórz nową kartę w przeglądarce i zaloguj się do firmowej witryny usługi Netskope User Authentication jako administrator.

1. Kliknij **kartę Ustawienia** w lewym okienku nawigacji.

    ![Zrzut ekranu przedstawiający wybraną opcję Ustawienie.](./media/netskope-user-authentication-tutorial/configuration-settings.png)

1. Kliknij **kartę Aktywna platforma.**

    ![Zrzut ekranu przedstawia aktywną platformę wybraną z opcji Ustawienia.](./media/netskope-user-authentication-tutorial/user-1.png)

1. Kliknij **kartę** Użytkownicy.

    ![Zrzut ekranu przedstawia użytkowników wybranych z aktywnej platformy.](./media/netskope-user-authentication-tutorial/add-user.png)

1. Kliknij **pozycję ADD USERS (DODAJ UŻYTKOWNIKÓW).**

    ![Zrzut ekranu przedstawia okno dialogowe Użytkownicy, w którym można wybrać pozycję DODAJ UŻYTKOWNIKÓW.](./media/netskope-user-authentication-tutorial/user-add.png)

1. Wprowadź adres e-mail użytkownika, którego chcesz dodać, a następnie kliknij przycisk **DODAJ**.

    ![Zrzut ekranu przedstawiający pozycję Dodaj użytkowników, na której można wprowadzić listę użytkowników.](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Inicjowane przez sp:

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania użytkownika netskope, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania użytkownika netskope i zainicjuj przepływ logowania.

#### <a name="idp-initiated"></a>Inicjowane przez IDP:

* Kliknij pozycję **Test this application** in Azure Portal (Przetestuj tę aplikację w aplikacji). Powinno nas na przykład automatycznie zalogować się do uwierzytelniania użytkowników netskope, dla którego została ustawiona logowanie jednokrotne. 

Możesz również użyć usługi Microsoft Moje aplikacje, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka Netskope User Authentication w trybie Moje aplikacje, jeśli zostanie skonfigurowany w trybie sp nastąpi przekierowanie do strony logowania aplikacji w celu zainicjowania przepływu logowania, a jeśli jest skonfigurowany w trybie dostawcy tożsamości, powinno na celu automatyczne zalogowanie się do uwierzytelniania użytkownika netskope, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na Moje aplikacje, [zobacz Introduction to the Moje aplikacje (Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu uwierzytelniania użytkowników Netskope można wymusić kontrolę sesji, która chroni eksfiltrację i przenikliwę poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozszerza zakres dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
