---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą PurelyHR | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i PurelyHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: 344e0c142b905558bffbf339c8e7a29c92113c23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515189"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purelyhr"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą PurelyHR

W tym samouczku dowiesz się, jak zintegrować usługę PurelyHR z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi PurelyHR z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do PurelyHR.
* Zezwól użytkownikom na automatyczne logowanie się do usługi PurelyHR przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) PurelyHR.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* PurelyHR obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* PurelyHR obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-purelyhr-from-the-gallery"></a>Dodawanie PurelyHR z galerii

Aby skonfigurować integrację programu PurelyHR z usługą Azure AD, musisz dodać PurelyHR z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **PurelyHR** w polu wyszukiwania.
1. Wybierz pozycję **PurelyHR** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purelyhr"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla PurelyHR

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą PurelyHR przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w PurelyHR.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą PurelyHR, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-purelyhr-sso)** w usłudze PurelyHR, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego PurelyHR](#create-purelyhr-test-user)** , aby dysponować odpowiednikiem B. Simon w PurelyHR, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **PurelyHR** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:  `https://<companyID>.purelyhr.com/sso-consume`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyID>.purelyhr.com/sso-initiate`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta PurelyHR](https://support.purelyhr.com/) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie PurelyHR** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi PurelyHR.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **PurelyHR**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-purelyhr-sso"></a>Konfigurowanie logowania jednokrotnego PurelyHR

1. Aby zautomatyzować konfigurację w programie PurelyHR, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj** , aby przekierować użytkownika do aplikacji PurelyHR. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi PurelyHR. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-5.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować PurelyHR, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do witryny firmy PurelyHR jako administrator i wykonaj następujące czynności:

1. Otwórz **pulpit nawigacyjny** z opcji na pasku narzędzi, a następnie kliknij pozycję **Ustawienia rejestracji jednokrotnej**.

1. Wklej wartości w polach zgodnie z opisem poniżej.

    ![Konfigurowanie logowania jednokrotnego](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Otwórz **certyfikat (Bas64)** pobrany z Azure Portal w programie Notepad i skopiuj wartość certyfikatu. Wklej skopiowaną wartość do pola **certyfikat X. 509** .

    b. W polu **adres URL wystawcy dostawcy tożsamości** wklej **Identyfikator usługi Azure AD** skopiowany z Azure Portal.

    c. W polu **adres URL punktu końcowego dostawcy tożsamości** wklej **adres URL logowania** skopiowany z Azure Portal. 

    d. Zaznacz pole wyboru **Automatycznie twórz użytkowników** , aby włączyć automatyczne Inicjowanie obsługi administracyjnej użytkowników w programie PurelyHR.

    e. Kliknij przycisk **Zapisz zmiany** , aby zapisać ustawienia.

### <a name="create-purelyhr-test-user"></a>Utwórz użytkownika testowego PurelyHR

Ten krok zazwyczaj nie jest wymagany, ponieważ aplikacja obsługuje funkcję udostępniania just in Time użytkownika. Jeśli automatyczne Inicjowanie obsługi użytkowników nie jest włączone, ręczne tworzenie użytkowników można wykonać zgodnie z poniższym opisem.

Zaloguj się do witryny firmy SAML Velpic jako administrator i wykonaj następujące czynności:

1. Kliknij przycisk Zarządzaj kartami i przejdź do sekcji Użytkownicy, a następnie kliknij przycisk Nowy przycisk, aby dodać użytkowników.

    ![Dodaj użytkownika](./media/velpicsaml-tutorial/velpic_7.png)

2. Na stronie **"Tworzenie nowego użytkownika"** wykonaj następujące czynności.

    ![użytkownik](./media/velpicsaml-tutorial/velpic_8.png)

    a. W polu tekstowym **imię i nazwisko** wpisz imię B.

    b. W polu **tekstowym nazwisko wpisz nazwisko Simon** .

    c. W polu tekstowym **Nazwa użytkownika** wpisz nazwę użytkownika B. Simon.

    d. W polu tekstowym adres **e-mail** wpisz adres e-mail B.Simon@contoso.com konta.

    e. Pozostałe informacje są opcjonalne, ale w razie konieczności można je wypełnić.

    f. Kliknij przycisk **Zapisz**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka PurelyHR w panelu dostępu należy automatycznie zalogować się do PurelyHR, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj PurelyHR z usługą Azure AD](https://aad.portal.azure.com/)