---
title: 'Samouczek: integracja Azure Active Directory z usługą Replicon | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Replicon.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 8915d780e79fa219428c54bad5458ab5966df6c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688505"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Samouczek: integracja Replicon z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Replicon z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Replicon z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Replicon.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Replicon przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej [bezpłatnej wersji](https://azure.microsoft.com/pricing/free-trial/)próbnej.
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Replicon.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Usługa Replicon obsługuje usługę **SP** zainicjowaną przez usługę SSO.

## <a name="adding-replicon-from-the-gallery"></a>Dodawanie Replicon z galerii

Aby skonfigurować integrację programu Replicon z usługą Azure AD, musisz dodać Replicon z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Replicon** w polu wyszukiwania.
1. Wybierz pozycję **Replicon** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Replicon przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Replicon.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Replicon, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-replicon-sso)** w usłudze Replicon, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
5. **[Utwórz użytkownika testowego Replicon](#create-replicon-test-user)** , aby dysponować odpowiednikiem B. Simon w Replicon, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Replicon** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    1. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. W polu **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://global.replicon.com/!/saml2/<client name>`

    1. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta Replicon](https://www.replicon.com/customerzone/contact-support) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Kliknij ikonę Edytuj/pióro dla **certyfikatu podpisywania SAML** , aby edytować ustawienia.

    ![Algorytm podpisywania](common/signing-algorithm.png)

    1. Wybierz pozycję **Podpisz potwierdzenie SAML** jako **opcję podpisywania**.

    1. Jako **algorytmu podpisywania wybierz algorytm** **SHA-256** .

1. Na stronie **Konfiguruj pojedyncze Sign-On za pomocą elementu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Konfigurowanie logowania jednokrotnego Replicon

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny Replicon jako administrator.

2. Aby skonfigurować element SAML 2,0, wykonaj następujące czynności:

    ![Włącz uwierzytelnianie SAML](./media/replicon-tutorial/ic777805.png "Włącz uwierzytelnianie SAML")

    a. Aby wyświetlić okno dialogowe **EnableSAML Authentication2** , po kluczu firmy Dołącz następujący adres URL: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Poniżej przedstawiono schemat kompletnego adresu URL: `https://na2.replicon.com/<YourCompanyKey>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Kliknij, **+** Aby rozwinąć sekcję **v20Configuration** .

   c. Kliknij, **+** Aby rozwinąć sekcję **metaDataConfiguration** .

   d. Wybierz **SHA256** dla xmlSignatureAlgorithm

   e. Kliknij pozycję **Wybierz plik**, aby wybrać plik XML metadanych dostawcy tożsamości, a następnie kliknij przycisk **Prześlij**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Replicon.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Replicon**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-replicon-test-user"></a>Utwórz użytkownika testowego Replicon

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w Replicon.

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. W oknie przeglądarki sieci Web Zaloguj się do firmowej witryny Replicon jako administrator.

2. Przejdź do **pozycji \> Użytkownicy administracyjni**.

    ![Użytkownicy](./media/replicon-tutorial/ic777806.png "Użytkownicy")

3. Kliknij pozycję **+ Dodaj użytkownika**.

    ![Dodaj użytkownika](./media/replicon-tutorial/ic777807.png "Dodaj użytkownika")

4. W sekcji **profil użytkownika** wykonaj następujące czynności:

    ![Profil użytkownika](./media/replicon-tutorial/ic777808.png "Profil użytkownika")

    a. W polu tekstowym **Nazwa logowania** wpisz adres E-mail usługi Azure AD, który ma być udostępniany użytkownikowi usługi Azure AD `B.Simon@contoso.com` .

    > [!NOTE]
    > Nazwa logowania musi być zgodna z adresem e-mail użytkownika w usłudze Azure AD

    b. W obszarze **Typ uwierzytelniania** wybierz pozycję **Logowanie jednokrotne**.

    c. Ustaw identyfikator uwierzytelniania na taką samą wartość jak nazwa logowania (adres e-mail usługi Azure AD użytkownika)

    d. W polu tekstowym **dział** wpisz Dział użytkownika.

    e. Jako **Typ pracownika** wybierz pozycję **administrator**.

    f. Kliknij pozycję **Zapisz profil użytkownika**.

> [!NOTE]
> Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników Replicon i interfejsów API udostępnionych przez usługę Replicon.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka Replicon w panelu dostępu należy automatycznie zalogować się do Replicon, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)