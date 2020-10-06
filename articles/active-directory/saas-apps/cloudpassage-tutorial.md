---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą CloudPassage | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją CloudPassage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: 8fdb59db7e11e3c8b946be940a2f98bd42810b88
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758357"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą CloudPassage

W tym samouczku dowiesz się, jak zintegrować usługę CloudPassage z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi CloudPassage z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do CloudPassage.
* Zezwól użytkownikom na automatyczne logowanie się do usługi CloudPassage przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) CloudPassage.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja CloudPassage obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-cloudpassage-from-the-gallery"></a>Dodawanie aplikacji CloudPassage z galerii

Aby skonfigurować integrację aplikacji CloudPassage z usługą Azure AD, musisz dodać aplikację CloudPassage z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **CloudPassage** w polu wyszukiwania.
1. Wybierz pozycję **CloudPassage** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla CloudPassage

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą CloudPassage przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w CloudPassage.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą CloudPassage, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-cloudpassage-sso)** w usłudze CloudPassage, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego CloudPassage](#create-cloudpassage-test-user)** , aby dysponować odpowiednikiem B. Simon w CloudPassage, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **CloudPassage** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

     a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://portal.cloudpassage.com/saml/init/accountid`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://portal.cloudpassage.com/saml/consume/accountid`. Wartość tego atrybutu można uzyskać, klikając pozycję **Dokumentacja dotycząca konfiguracji logowania jednokrotnego** w sekcji **Ustawienia logowania jednokrotnego** witryny CloudPassage.

    ![Zrzut ekranu przedstawia Portal CloudPassage z linkiem "s" z dokumentacją Instalatora o nazwie out.](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem obsługi klienta aplikacji CloudPassage](https://www.cloudpassage.com/company/contact/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja CloudPassage oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/edit-attribute.png)

1. Oprócz powyższych, aplikacja CloudPassage oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | poczta e-mail |user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie CloudPassage** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi CloudPassage.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **CloudPassage**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-cloudpassage-sso"></a>Konfigurowanie logowania jednokrotnego CloudPassage

1. W innym oknie przeglądarki zaloguj się do firmowej witryny aplikacji CloudPassage jako administrator.

1. W menu u góry kliknij pozycję **Settings (Ustawienia)**, a następnie **Site Administration (Administrowanie witryną)**. 
   
    ![Zrzut ekranu przedstawia witrynę CloudPassage z wybraną administracją witryny.][12]

1. Kliknij kartę **Authentication Settings (Ustawienia uwierzytelniania)**. 
   
    ![Zrzut ekranu przedstawia witrynę CloudPassage z wybraną kartą ustawienia uwierzytelniania.][13]

1. W sekcji **Single Sign-on Settings (Ustawienia logowania jednokrotnego)** wykonaj następujące kroki: 
   
    ![Zrzut ekranu przedstawia sekcję ustawień logowania jednokrotnego, w której można wprowadzić informacje w tym kroku.][14]

    a. Zaznacz pole wyboru **Enable Single sign-on(SSO)(SSO Setup Documentation) (Włącz logowanie jednokrotne[SSO] [Dokumentacja dotycząca konfiguracji logowania jednokrotnego])**.
    
    b. Wklej **Identyfikator usługi Azure AD** do pola tekstowego **adresu URL wystawcy SAML** .
  
    c. Wklej **adres URL logowania** w polu tekstowym **SAML endpoint URL (Adres URL punktu końcowego SAML)**.
  
    d. Wklej **adres URL wylogowywania** w polu tekstowym **Logout landing page (Strona docelowa wylogowywania)**.
  
    e. Otwórz pobrany plik certyfikatu w Notatniku, skopiuj zawartość pobranego certyfikatu do schowka, a następnie wklej ją w polu tekstowym **x 509 certificate (Certyfikat x 509)**.
  
    f. Kliknij pozycję **Zapisz**.

### <a name="create-cloudpassage-test-user"></a>Tworzenie użytkownika testowego aplikacji CloudPassage

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w CloudPassage.

**Aby utworzyć użytkownika o nazwie B. Simon w CloudPassage, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny aplikacji **CloudPassage** jako administrator. 

1. Na pasku narzędzi u góry kliknij pozycję **Settings (Ustawienia)**, a następnie **Site Administration (Administrowanie witryną)**. 
   
    ![Zrzut ekranu przedstawia CloudPassage z wybraną administracją witryny.][22] 

1. Kliknij kartę **Users (Użytkownicy)**, a następnie kliknij pozycję **Add New User (Dodaj nowego użytkownika)**. 
   
    ![Zrzut ekranu przedstawia Administrowanie witryną CloudPassage z wybraną kartą Użytkownicy i opcją dodania nowego użytkownika.][23]

1. W sekcji **Add New User (Dodaj nowego użytkownika)** wykonaj następujące kroki: 
   
    ![Zrzut ekranu przedstawia sekcję Dodaj nowy użytkownik, w której można określić informacje o użytkowniku.][24]
    
    a. W polu tekstowym **First Name (Imię)** wpisz Britta. 
  
    b. W polu tekstowym **Last Name (Nazwisko)** wpisz Simon.
  
    c. W polu tekstowym **Username (Nazwa użytkownika)**, polu tekstowym**Email (Adres e-mail)** i polu tekstowym **Retype Email (Wpisz ponownie adres e-mail)** wpisz nazwę użytkownika Britta w usłudze Azure AD.
  
    d. Jako **typ dostępu** wybierz opcję **Enable Halo Portal Access (Włącz dostęp z poziomu portalu Halo)**.
  
    e. Kliknij pozycję **Dodaj**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka CloudPassage na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji CloudPassage, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj CloudPassage z usługą Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

