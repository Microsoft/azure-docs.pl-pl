---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z uwierzytelnianiem użytkowników Netskope | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a uwierzytelnianiem użytkowników Netskope.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/01/2019
ms.author: jeedes
ms.openlocfilehash: 1ae9e39f5a23725b17451d7144c8304194d2a1d0
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514371"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z uwierzytelnianiem za pomocą Netskope użytkownika

W tym samouczku dowiesz się, jak zintegrować uwierzytelnianie użytkowników Netskope z usługą Azure Active Directory (Azure AD). W przypadku integrowania uwierzytelniania użytkowników Netskope z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Netskope uwierzytelniania użytkowników.
* Zezwól użytkownikom na automatyczne logowanie do Netskope uwierzytelniania użytkowników przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Netskope subskrypcję obsługującą Logowanie jednokrotne (SSO) uwierzytelniania użytkowników.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Uwierzytelnianie użytkowników Netskope obsługuje logowanie jednokrotne **z użyciem SP i dostawcy tożsamości**

## <a name="adding-netskope-user-authentication-from-the-gallery"></a>Dodawanie uwierzytelniania użytkownika Netskope z galerii

Aby skonfigurować integrację uwierzytelniania użytkownika programu Netskope z usługą Azure AD, musisz dodać uwierzytelnianie użytkownika Netskope z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Netskope User Authentication** w polu wyszukiwania.
1. Wybierz pozycję **Netskope User Authentication** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-user-authentication"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby uwierzytelniania użytkowników Netskope

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą uwierzytelniania użytkownika Netskope przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Netskope uwierzytelniania użytkowników.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu uwierzytelniania użytkownika Netskope, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Konfigurowanie logowania JEDNOkrotnego uwierzytelniania użytkownika Netskope](#configure-netskope-user-authentication-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego uwierzytelniania użytkowników](#create-netskope-user-authentication-test-user)** w usłudze Netskope, aby dysponować odpowiednikiem B. Simon w ramach uwierzytelniania użytkownika Netskope, które jest połączone z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji do **uwierzytelniania użytkowników Netskope** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<tenantname>.goskope.com/<customer entered string>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Te wartości zostaną omówione w dalszej części tego samouczka.

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Wartości adresu URL logowania nie są prawdziwe. Zaktualizuj wartość adresu URL logowania przy użyciu rzeczywistego adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta uwierzytelniania użytkownika Netskope](mailto:support@netskope.com) , aby uzyskać wartość adresu URL logowania. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie uwierzytelniania użytkowników Netskope** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Netskope uwierzytelniania użytkowników.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Netskope User Authentication**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-netskope-user-authentication-sso"></a>Konfigurowanie logowania jednokrotnego uwierzytelniania użytkownika Netskope

1. Otwórz nową kartę w przeglądarce, a następnie zaloguj się do swojej witryny sieci firmowej uwierzytelniania użytkownika Netskope jako administrator.

1. Kliknij pozycję **aktywna karta platformy** .

    ![Zrzut ekranu przedstawia aktywną platformę wybraną z ustawień.](./media/netskope-user-authentication-tutorial/user1.png)

1. Przewiń w dół do **przodu proxy** i wybierz pozycję **SAML**.

    ![Zrzut ekranu przedstawia element SAML wybrany z aktywnej platformy.](./media/netskope-user-authentication-tutorial/config-saml.png)

1. Na stronie **Ustawienia SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia ustawienia protokołu SAML, w których można wprowadzić podane wartości.](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Skopiuj wartość **Identyfikator jednostki SAML** i wklej ją do pola tekstowego **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    b. Skopiuj wartość **adresu URL ACS protokołu SAML** i wklej ją do pola tekstowego **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

1. Kliknij pozycję **Dodaj konto**.

    ![Zrzut ekranu przedstawia Dodawanie konta wybranego w okienku SAML.](./media/netskope-user-authentication-tutorial/config-addaccount.png)

1. Na stronie **Dodawanie konta SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia Dodawanie konta SAML, w którym można wprowadzić podane wartości.](./media/netskope-user-authentication-tutorial/config-settings1.png)

    a. W polu tekstowym **Nazwa** Podaj nazwę, taką jak Azure AD.

    b. W polu tekstowym **adres URL dostawcy tożsamości** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    c. W polu tekstowym **Identyfikator jednostki dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.

    d. Otwórz pobrany plik metadanych w Notatniku, skopiuj jego zawartość do schowka, a następnie wklej go do pola tekstowego **certyfikatu dostawcy tożsamości** .

    e. Kliknij przycisk **Zapisz**.

### <a name="create-netskope-user-authentication-test-user"></a>Utwórz użytkownika testowego uwierzytelniania użytkownika Netskope

1. Otwórz nową kartę w przeglądarce, a następnie zaloguj się do swojej witryny sieci firmowej uwierzytelniania użytkownika Netskope jako administrator.

1. Kliknij kartę **Ustawienia** w okienku nawigacji po lewej stronie.

    ![Zrzut ekranu przedstawia wybrane ustawienie.](./media/netskope-user-authentication-tutorial/config-settings.png)

1. Kliknij pozycję **aktywna karta platformy** .

    ![Zrzut ekranu przedstawia aktywną platformę wybraną z ustawień.](./media/netskope-user-authentication-tutorial/user1.png)

1. Kliknij kartę **Użytkownicy** .

    ![Zrzut ekranu przedstawia użytkowników wybranych z aktywnej platformy.](./media/netskope-user-authentication-tutorial/add-user.png)

1. Kliknij pozycję **Dodaj użytkowników**.

    ![Zrzut ekranu przedstawia okno dialogowe użytkownicy, w którym można wybrać opcję Dodaj użytkowników.](./media/netskope-user-authentication-tutorial/user-add.png)

1. Wprowadź adres e-mail użytkownika, który chcesz dodać, a następnie kliknij przycisk **Dodaj**.

    ![Zrzut ekranu przedstawia Dodawanie użytkowników, w których można wprowadzić listę użytkowników.](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka uwierzytelnianie użytkownika Netskope w panelu dostępu należy automatycznie zalogować się do uwierzytelniania użytkownika Netskope, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj Netskope uwierzytelniania użytkowników w usłudze Azure AD](https://aad.portal.azure.com/)