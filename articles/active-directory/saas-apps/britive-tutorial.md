---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Britive | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Britive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2020
ms.author: jeedes
ms.openlocfilehash: 74f449afe2081feecfe7f0194b20429314ec572d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456674"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-britive"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Britive

W tym samouczku dowiesz się, jak zintegrować usługę Britive z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Britive z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Britive.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Britive przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Britive.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Britive obsługuje logowanie jednokrotne w usłudze **SP**
* Po skonfigurowaniu Britive można wymusić kontrolki sesji, które chronią eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-britive-from-the-gallery"></a>Dodawanie Britive z galerii

Aby skonfigurować integrację programu Britive z usługą Azure AD, musisz dodać Britive z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Britive** w polu wyszukiwania.
1. Wybierz pozycję **Britive** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-britive"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Britive

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Britive przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Britive.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Britive, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-britive-sso)** w usłudze Britive, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Britive](#create-britive-test-user)** , aby dysponować odpowiednikiem B. Simon w Britive, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Britive** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<TENANTNAME>.britive-app.com/sso`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `urn:amazon:cognito:sp:<UNIQUE_ID>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu faktycznego adresu URL i identyfikatora w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie Britive** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Britive.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Britive**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-britive-sso"></a>Konfigurowanie logowania jednokrotnego Britive

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny internetowej Britive jako administrator.

1. Kliknij **ikonę Ustawienia administratora** i wybierz pozycję **zabezpieczenia**.

    ![Zrzut ekranu przedstawia witrynę sieci Web Britive z wybranymi ustawieniami i zabezpieczeniami.](./media/britive-tutorial/configure1.png)

1. Wybierz pozycję **Konfiguracja logowania jednokrotnego** i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia konfigurację S O, w której wprowadzane są informacje w tym kroku.](./media/britive-tutorial/configure2.png)

    a. Skopiuj wartość **Identyfikator odbiorcy/jednostki** i wklej ją do pola tekstowego **Identyfikator (identyfikator jednostki)** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    b. Skopiuj wartość **Inicjuj adres URL logowania JEDNOkrotnego** i wklej ją do pola tekstowego **Logowanie w adresie URL** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    c. Kliknij pozycję **Przekaż metadane SAML** , aby przekazać pobrany plik XML metadanych z Azure Portal. Po przekazaniu pliku metadanych powyższe wartości będą wypełniane automatycznie, a zmiany zostaną zapisane.

### <a name="create-britive-test-user"></a>Utwórz użytkownika testowego Britive

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny internetowej Britive jako administrator.

1. Kliknij **ikonę Ustawienia administratora** i wybierz pozycję **Administracja użytkownikami**.

    ![Zrzut ekranu przedstawia witrynę sieci Web Britive z wybranymi ustawieniami i administracją użytkownika.](./media/britive-tutorial/user1.png)

1. Kliknij pozycję **Dodaj użytkownika**.

    ![Zrzut ekranu przedstawia przycisk Dodaj użytkownika.](./media/britive-tutorial/user2.png)

1. Wypełnij wszystkie niezbędne szczegóły użytkownika zgodnie z wymaganiami organizacji, a następnie kliknij pozycję **Dodaj użytkownika**.

    ![Zrzut ekranu przedstawia stronę użytkownika usługi AD, na której wprowadzane są informacje o użytkowniku.](./media/britive-tutorial/user3.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Britive w panelu dostępu należy automatycznie zalogować się do Britive, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj Britive z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)