---
title: 'Samouczek: integracja Azure Active Directory z usługą platforma Kiteworks | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i platforma Kiteworks.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.openlocfilehash: ae504a7dcd6f598c13b5fabffaca4ec6ec067e4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88549934"
---
# <a name="tutorial-integrate-kiteworks-with-azure-active-directory"></a>Samouczek: integracja platforma Kiteworks z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę platforma Kiteworks z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi platforma Kiteworks z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do platforma Kiteworks.
* Zezwól użytkownikom na automatyczne logowanie się do usługi platforma Kiteworks przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej [bezpłatnej wersji](https://azure.microsoft.com/pricing/free-trial/)próbnej.
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) platforma Kiteworks.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Platforma Kiteworks obsługuje logowanie jednokrotne w usłudze **SP**
* Platforma Kiteworks obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-kiteworks-from-the-gallery"></a>Dodawanie platforma Kiteworks z galerii

Aby skonfigurować integrację programu Platforma Kiteworks z usługą Azure AD, musisz dodać platforma Kiteworks z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **platforma Kiteworks** w polu wyszukiwania.
1. Wybierz pozycję **platforma Kiteworks** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą platforma Kiteworks przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w platforma Kiteworks.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą platforma Kiteworks, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-kiteworks-sso)** w usłudze platforma Kiteworks, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego platforma Kiteworks](#create-kiteworks-test-user)** , aby uzyskać odpowiednik Britta Simon w platforma Kiteworks, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **platforma Kiteworks** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<kiteworksURL>.kiteworks.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<kiteworksURL>/sp/module.php/saml/sp/saml2-acs.php/sp-sso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta platforma Kiteworks](https://accellion.com/support) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie platforma Kiteworks** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-kiteworks-sso"></a>Konfigurowanie logowania jednokrotnego platforma Kiteworks

1. Zaloguj się do firmowej witryny platforma Kiteworks jako administrator.

1. Na pasku narzędzi u góry kliknij pozycję **Settings** (Ustawienia).

    ![Konfigurowanie logowania jednokrotnego](./media/kiteworks-tutorial/tutorial_kiteworks_06.png)

1. W sekcji **uwierzytelnianie i autoryzacja** kliknij pozycję **Konfiguracja logowania jednokrotnego**.

    ![Konfigurowanie logowania jednokrotnego](./media/kiteworks-tutorial/tutorial_kiteworks_07.png)

1. Na stronie Konfiguracja logowania jednokrotnego wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/kiteworks-tutorial/tutorial_kiteworks_09.png)

    a. Wybierz pozycję **Uwierzytelnij za pomocą logowania jednokrotnego**.

    b. Wybierz pozycję **Inicjuj AuthnRequest**.

    c. W polu tekstowym **Identyfikator jednostki dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD**, która została skopiowana z Azure Portal.

    d. W polu tekstowym **adres URL usługi Single Sign-On** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.

    e. W polu tekstowym **adres URL usługi wylogowania jednokrotnego** wklej wartość **adresu URL wylogowania**, który został skopiowany z Azure Portal.

    f. Otwórz pobrany certyfikat w Notatniku, skopiuj zawartość, a następnie wklej ją do pola tekstowego **certyfikat klucza publicznego RSA** .

    przykład Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi platforma Kiteworks.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **platforma Kiteworks**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-kiteworks-test-user"></a>Utwórz użytkownika testowego platforma Kiteworks

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w platforma Kiteworks.

Platforma Kiteworks obsługuje obsługę just-in-Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik zostanie utworzony podczas próby uzyskania dostępu do platforma Kiteworks, jeśli jeszcze nie istnieje.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej platforma Kiteworks](https://accellion.com/support).

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka platforma Kiteworks w panelu dostępu należy automatycznie zalogować się do platforma Kiteworks, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
