---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą InVision | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i InVision.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/09/2020
ms.author: jeedes
ms.openlocfilehash: 2e8f542f8a734f5bd6592ebc743b30676dd61f07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850556"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-invision"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą InVision

W tym samouczku dowiesz się, jak zintegrować usługę InVision z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi InVision z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do InVision.
* Zezwól użytkownikom na automatyczne logowanie się do usługi InVision przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) InVision.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* InVision obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* Po skonfigurowaniu InVision można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-invision-from-the-gallery"></a>Dodawanie InVision z galerii

Aby skonfigurować integrację programu InVision z usługą Azure AD, musisz dodać InVision z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **InVision** w polu wyszukiwania.
1. Wybierz pozycję **InVision** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-invision"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla InVision

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą InVision przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w InVision.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą InVision, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-invision-sso)** w usłudze InVision, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego InVision](#create-invision-test-user)** , aby dysponować odpowiednikiem B. Simon w InVision, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **InVision** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.invisionapp.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.invisionapp.com//sso/auth`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.invisionapp.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta InVision](mailto:support@invisionapp.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie InVision** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi InVision.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **InVision**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-invision-sso"></a>Konfigurowanie logowania jednokrotnego InVision

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny InVision jako administrator.

1. Kliknij pozycję **zespół** i wybierz pozycję **Ustawienia**.

    ![Zrzut ekranu przedstawia kartę zespół z wybranymi ustawieniami.](./media/invision-tutorial/config1.png)

1. Przewiń w dół do okna **rejestracja** jednokrotna, a następnie kliknij przycisk **Zmień**.

    ![Zrzut ekranu przedstawia przycisk Zmień na potrzeby logowania jednokrotnego.](./media/invision-tutorial/config3.png)

1. Na stronie **Logowanie** jednokrotne wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia stronę logowania jednokrotnego, na której wprowadzane są wartości w tym kroku.](./media/invision-tutorial/config4.png)

    a. Zmień **wymaganie logowania jednokrotnego dla każdego elementu członkowskiego < nazwy konta >** na **włączone**.

    b. W polu tekstowym **Nazwa** wprowadź nazwę na przykład `azureadsso` .

    c. Wprowadź wartość adresu URL logowania w polu tekstowym **adres URL logowania** .

    d. W polu tekstowym **adres URL wylogowania** wklej wartość adresu URL **Wyloguj** , która została skopiowana z Azure Portal.

    e. W polu tekstowym **certyfikat SAML** Otwórz pobrany **certyfikat (base64)** do Notatnika, skopiuj zawartość i wklej ją do pola tekstowego certyfikatu SAML.

    f. W polu tekstowym **Format identyfikatora nazwy** Użyj `Unspecified` **formatu identyfikatora nazwy**.

    przykład Wybierz pozycję **SHA-256** z listy rozwijanej **algorytmu wyznaczania wartości skrótu**.

    h. Wprowadź odpowiednią nazwę **etykiety przycisku rejestracji jednokrotnej**.

    i. Zezwalaj na udostępnianie **just-in-Time** w usłudze.

    j. Kliknij przycisk **Update** (Aktualizuj).

### <a name="create-invision-test-user"></a>Utwórz użytkownika testowego InVision

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny InVision jako administrator.

1. Kliknij pozycję **zespół** i wybierz pozycję **osoby**.

    ![Zrzut ekranu przedstawia kartę zespołu z wybranymi osobami.](./media/invision-tutorial/config2.png)

1. Kliknij **ikonę +** , aby dodać nowego użytkownika.

    ![Zrzut ekranu przedstawia ikonę +, aby dodać użytkownika.](./media/invision-tutorial/user1.png)

1. Wprowadź adres e-mail użytkownika, a następnie kliknij przycisk **dalej**.

    ![Zrzut ekranu przedstawia okno dialogowe zapraszanie do, w którym można wprowadzać adresy.](./media/invision-tutorial/user2.png)

1. Zweryfikuj adres e-mail, a następnie kliknij pozycję **Zaproś**.

    ![Zrzut ekranu przedstawia okno dialogowe zaproszenia, w którym można wybrać opcję Zapraszanie do wykonania.](./media/invision-tutorial/user3.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka InVision w panelu dostępu należy automatycznie zalogować się do InVision, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj InVision z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić InVision z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)