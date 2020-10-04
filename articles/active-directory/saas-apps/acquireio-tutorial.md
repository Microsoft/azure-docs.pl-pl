---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą AcquireIO | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i AcquireIO.
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
ms.openlocfilehash: cd07370c75ac64fa8e3806fed50a64db90122c76
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708326"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-acquireio"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą AcquireIO

W tym samouczku dowiesz się, jak zintegrować usługę AcquireIO z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi AcquireIO z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do AcquireIO.
* Zezwól użytkownikom na automatyczne logowanie się do usługi AcquireIO przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) AcquireIO.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa AcquireIO obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne

## <a name="adding-acquireio-from-the-gallery"></a>Dodawanie AcquireIO z galerii

Aby skonfigurować integrację programu AcquireIO z usługą Azure AD, musisz dodać AcquireIO z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **AcquireIO** w polu wyszukiwania.
1. Wybierz pozycję **AcquireIO** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-acquireio"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla AcquireIO

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą AcquireIO przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w AcquireIO.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą AcquireIO, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-acquireio-sso)** w usłudze AcquireIO, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego AcquireIO](#create-acquireio-test-user)** , aby dysponować odpowiednikiem B. Simon w AcquireIO, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **AcquireIO** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:  `https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Otrzymasz rzeczywisty adres URL odpowiedzi, który został wyjaśniony **w dalszej części tego** samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie AcquireIO** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi AcquireIO.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **AcquireIO**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-acquireio-sso"></a>Konfigurowanie logowania jednokrotnego AcquireIO

1. Aby zautomatyzować konfigurację w programie AcquireIO, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj AcquireIO**, która kieruje użytkownika do aplikacji AcquireIO. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi AcquireIO. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować AcquireIO, w innym oknie przeglądarki sieci Web Zaloguj się do AcquireIO jako administrator.

1. Z lewej strony menu kliknij pozycję **sklep z aplikacjami**.

     ![Zrzut ekranu, który wyróżnia sklep z aplikacjami.](./media/acquireio-tutorial/config01.png)

1. Przewiń w dół do **Active Directory** i kliknij pozycję **Zainstaluj**.

    ![Zrzut ekranu, który podświetla sekcję Active Directory i przycisk Instaluj.](./media/acquireio-tutorial/config02.png)

1. W oknie podręcznym Active Directory wykonaj następujące czynności:

    ![Screnshot, który wyświetla ekran Active Directory.](./media/acquireio-tutorial/config03.png)

    a. Kliknij przycisk **Kopiuj** , aby skopiować adres URL odpowiedzi dla swojego wystąpienia i wkleić go w polu tekstowym **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    b. W polu tekstowym **adres URL logowania** wklej wartość **adresu URL logowania**, który został skopiowany z Azure Portal.

    c. Otwórz w Notatniku certyfikat szyfrowany algorytmem Base64, skopiuj jego zawartość i wklej go w polu tekstowym **certyfikat X. 509** .

    d. Kliknij przycisk **Połącz teraz**.

### <a name="create-acquireio-test-user"></a>Utwórz użytkownika testowego AcquireIO

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze AcquireIO, muszą one być obsługiwane w usłudze AcquireIO. W AcquireIO, Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. W innym oknie przeglądarki sieci Web Zaloguj się do AcquireIO jako administrator.

1. Z lewej strony menu kliknij pozycję **Profile** i przejdź do obszaru **Dodawanie profilu**.

     ![Zrzut ekranu, który wyróżnia profile w menu po lewej stronie ekranu, a także opcję Dodaj profil.](./media/acquireio-tutorial/config04.png)

1. W oknie podręcznym **Dodaj klienta** wykonaj następujące czynności:

    ![Konfiguracja AcquireIO](./media/acquireio-tutorial/config05.png)

    a. W polu tekstowym **Nazwa** wprowadź nazwę użytkownika, na przykład **B. Simon**.

    b. W polu tekstowym **Email** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład **B.simon@contoso.com**.

    c. Kliknij przycisk **Prześlij**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka AcquireIO w panelu dostępu należy automatycznie zalogować się do AcquireIO, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj AcquireIO z usługą Azure AD](https://aad.portal.azure.com/)
