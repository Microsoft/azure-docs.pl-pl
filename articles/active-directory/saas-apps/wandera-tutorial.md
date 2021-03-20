---
title: 'Samouczek: integracja Azure Active Directory z administratorem RADARu Wandera | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i administratorem Wandera.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: d13619b818e18c64d9882f9e3181824173403859
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181394"
---
# <a name="tutorial-integrate-wandera-radar-admin-with-azure-active-directory"></a>Samouczek: integracja z Azure Active Directorym administratorem RADARu Wandera

W ramach tego samouczka dowiesz się, jak zintegrować Wanderae z administratorem RADARu z Azure Active Directory (Azure AD). Po zintegrowaniu z usługą Azure AD Wanderaa z administratorem RADARu można:

* Kontrolka w usłudze Azure AD, która ma dostęp do administratora RADARu Wandera.
* Zezwól użytkownikom na automatyczne logowanie do Wanderaego konta usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi Wandera.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Administrator RADARu Wandera obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne
* Po skonfigurowaniu administratora RADARu Wandera można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wandera-radar-admin-from-the-gallery"></a>Dodawanie administratora RADARu Wandera z galerii

Aby skonfigurować integrację administratora Wandera z usługą Azure AD, musisz dodać administratora RADARu Wandera z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Wandera** w polu wyszukiwania.
1. Wybierz pozycję **administrator radaru Wandera** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą administratora Wandera RADARu przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Administratorze Wandera RADARu.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą administratora Wandera RADARu, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
   * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
   * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-wandera-radar-admin-sso)** w usłudze Wandera, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
   * **[Utwórz użytkownika testowego WANDERA radaru](#create-wandera-radar-admin-test-user)** — aby dysponować odpowiednikiem B. Simon w Administratorze usługi Wandera, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **administratora Wandera radaru** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:  `https://radar.wandera.com/saml/acs/<tenant id>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej WANDERA radaru klienta](https://www.wandera.com/about-wandera/contact/#supportsection) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfiguruj pojedyncze Sign-On za pomocą elementu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra dla **certyfikatu podpisywania SAML** , aby edytować ustawienia.

    ![Opcja podpisywania](common/signing-option.png)

    1. Wybierz **opcję podpisywania** , aby **podpisać odpowiedź i potwierdzenie SAML**.

    1. Wybierz **algorytm podpisywania** jako **SHA-256**.

1. W sekcji **Konfigurowanie administratora WANDERA radaru** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do administratora RADARu Wandera.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **administrator Wandera**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-wandera-radar-admin-sso"></a>Konfigurowanie rejestracji jednokrotnej administratora Wandera RADARu

1. Aby zautomatyzować konfigurację w ramach administratora Wandera RADARu, musisz zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij kolejno pozycje **Konfiguracja Wandera — administrator** , który przekierowuje użytkownika do aplikacji administratora Wandera. W tym miejscu podaj poświadczenia administratora, aby zalogować się do administratora RADARu Wandera. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-4.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować administratora Wandera RADARu, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do firmowej witryny administratora Wandera RADARu jako administrator i wykonaj następujące czynności:

4. W prawym górnym rogu strony kliknij pozycję **Ustawienia**  >  **Administracja**  >  **Logowanie jednokrotne** , a następnie zaznacz opcję **Włącz protokół SAML 2,0** , aby wykonać poniższe kroki.

    ![Konfiguracja administratora RADARu Wandera](./media/wandera-tutorial/config01.png)

    a. Kliknij **lub ręcznie wprowadź wymagane pola**.

    b. W polu tekstowym **dostawcy tożsamości EntityId** wklej wartość identyfikatora usługi **Azure AD** , która została skopiowana z Azure Portal.

    c. Otwórz plik XML metadanych Federacji w Notatniku, skopiuj jego zawartość i wklej go do pola tekstowego **dostawcy tożsamości publicznego X. 509 certyfikatu** .

    d. Kliknij pozycję **Zapisz**.

### <a name="create-wandera-radar-admin-test-user"></a>Utwórz użytkownika testowego Wandera RADARu

W tej sekcji utworzysz użytkownika o nazwie B. Simon w Administratorze Wandera RADAR. Współpraca z [zespołem pomocy technicznej działu Wanderaa](https://www.wandera.com/about-wandera/contact/#supportsection) Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka administratora Wandera RADARu w panelu dostępu należy automatycznie zalogować się do administratora RADARu Wandera, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)