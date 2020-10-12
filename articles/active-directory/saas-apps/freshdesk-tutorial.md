---
title: 'Samouczek: integracja Azure Active Directory z usługą usługi FreshDesk | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją FreshDesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: jeedes
ms.openlocfilehash: 0bbfdc2463795770b52f5008fc633fe9e95244a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056573"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Samouczek: integracja Azure Active Directory z usługą usługi FreshDesk

Z tego samouczka dowiesz się, jak zintegrować aplikację FreshDesk z usługą Azure Active Directory (Azure AD).
Zintegrowanie aplikacji FreshDesk z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować za pomocą usługi Azure AD, kto ma dostęp do aplikacji FreshDesk.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji FreshDesk (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją FreshDesk, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji FreshDesk z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja FreshDesk obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**
* Po skonfigurowaniu usługi FreshDesk można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-freshdesk-from-the-gallery"></a>Dodawanie aplikacji FreshDesk z galerii

Aby skonfigurować integrację aplikacji FreshDesk z usługą Azure AD, należy dodać aplikację FreshDesk z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **usługi FreshDesk** w polu wyszukiwania.
1. Wybierz pozycję **usługi FreshDesk** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla usługi FreshDesk

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą usługi FreshDesk przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usługi FreshDesk.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą usługi FreshDesk, wykonaj następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego w aplikacji FreshDesk](#configure-freshdesk-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji FreshDesk](#create-freshdesk-test-user)** — aby mieć w aplikacji FreshDesk odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **usługi FreshDesk** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.freshdesk.com` lub dowolna inna wartość usługi FreshDesk.

    b. W polu tekstowym **Identyfikator (Identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.freshdesk.com` lub wprowadź inną wartość podaną przez dostawcę aplikacji FreshDesk.
     
    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<tenant-name>.freshdesk.com/login/saml`
    
    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej aplikacji FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja FreshDesk oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenów języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych, natomiast **unikatowy identyfikator użytkownika** jest mapowany na **User. userPrincipalName** , ale usługi FreshDesk oczekuje na mapowanie tego żądania z **użytkownikiem. mail**, dlatego należy edytować mapowanie atrybutu, klikając ikonę Edytuj i zmieniając mapowanie atrybutu.

    ![image (obraz)](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **set-up usługi FreshDesk** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon \@ yourcompanydomain. Extension**  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.


### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji FreshDesk.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **FreshDesk**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz **FreshDesk**.

    ![Link aplikacji FreshDesk na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-freshdesk-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji FreshDesk

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji FreshDesk jako administrator.

2. Wybierz **ikonę zabezpieczenia** i w sekcji **zabezpieczenia** wykonaj następujące czynności:

    ![Logowanie jednokrotne](./media/freshdesk-tutorial/configure-1.png "Logowanie jednokrotne")
  
    a. W przypadku **logowania jednokrotnego**wybierz pozycję **włączone**.

    b. W **metodzie logowania**wybierz pozycję **SAML SSO**.

    c. W **identyfikatorze jednostki podanej przez** pole tekstowe dostawcy tożsamości wklej wartość **identyfikatora jednostki** , która została skopiowana z Azure Portal.

    d. W polu tekstowym **adres URL logowania JEDNOkrotnego SAML** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    e. W **opcjach podpisywania**wybierz **tylko podpisane potwierdzenia** z listy rozwijanej.

    f. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    przykład W polu tekstowym **certyfikat zabezpieczeń** wklej wartość **certyfikatu (base64)** , którą uzyskano wcześniej.
  
    h. Kliknij przycisk **Zapisz**.

## <a name="create-freshdesk-test-user"></a>Tworzenie użytkownika testowego aplikacji FreshDesk

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji FreshDesk, należy ich aprowizować w aplikacji FreshDesk.  
W przypadku aplikacji FreshDesk aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do swojej dzierżawy aplikacji **FreshDesk**.

1. W menu po lewej stronie kliknij pozycję **administrator** , a następnie na karcie **Ustawienia ogólne** kliknij pozycję **agenci**.
  
    ![Agenci](./media/freshdesk-tutorial/create-user-1.png "Agenci")

1. Kliknij pozycję **New Agent** (Nowy agent).

    ![Nowy Agent](./media/freshdesk-tutorial/create-user-2.png "Nowy Agent")

1. W oknie dialogowym Informacje o agencie wprowadź wymagane pola i kliknij przycisk **Utwórz agenta**.

    ![Informacje o agencie](./media/freshdesk-tutorial/create-user-3.png "Informacje o agencie")

    >[!NOTE]
    >Właściciel konta usługi Azure AD otrzyma wiadomość e-mail zawierającą link do potwierdzenia konta, zanim zostanie ono aktywowane.
    >
    >[!NOTE]
    >Do obsługi kont użytkowników usługi Azure AD w usłudze usługi FreshDesk można użyć innych narzędzi do tworzenia kont użytkowników usługi FreshDesk i interfejsów API udostępnionych przez usługę usługi FreshDesk.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi FreshDesk w panelu dostępu należy automatycznie zalogować się do usługi FreshDesk, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

