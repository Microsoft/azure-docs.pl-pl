---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą 8x8 | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i 8x8.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.openlocfilehash: 5f7e85cc37d5a899681b5dd1466c216f1be39a61
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708805"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą 8x8

W tym samouczku dowiesz się, jak zintegrować usługę 8x8 z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi 8x8 z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do 8x8.
* Zezwól użytkownikom na automatyczne logowanie się do usługi 8x8 przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi 8x8.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* 8x8 obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

* Po skonfigurowaniu 8x8 można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-8x8-from-the-gallery"></a>Dodawanie 8x8 z galerii

Aby skonfigurować integrację programu 8x8 z usługą Azure AD, musisz dodać 8x8 z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **8x8** w polu wyszukiwania.
1. Wybierz pozycję **8x8** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-8x8"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla 8x8

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą 8x8 przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w 8x8.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą 8x8, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-8x8-sso)** w usłudze 8x8, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego 8x8](#create-8x8-test-user)** , aby dysponować odpowiednikiem B. Simon w 8x8, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **8X8** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `https://sso.8x8.com/saml2`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: `https://sso.8x8.com/saml2`

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze. Certyfikat zostanie użyty później w samouczku w sekcji **Konfigurowanie logowania jednokrotnego 8x8** .

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie 8x8** Skopiuj adresy URL, a następnie użyj tych wartości adresu URL w dalszej części tego samouczka.

    ![Kopiowanie adresów URL konfiguracji](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi 8x8.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **8x8**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-8x8-sso"></a>Konfigurowanie logowania jednokrotnego 8x8

Kolejna część tego samouczka zależy od rodzaju subskrypcji z 8x8.

* W przypadku wersji 8x8 i klientów serii X z wykorzystaniem Configuration Manager do administrowania zapoznaj się z tematem [Konfigurowanie 8x8 Configuration Manager](#configure-8x8-configuration-manager).
* W przypadku klientów wirtualnych pakietu Office korzystających z Menedżera kont do administrowania zapoznaj się z tematem [Konfigurowanie Menedżera kont 8x8](#configure-8x8-account-manager).

### <a name="configure-8x8-configuration-manager"></a>Konfigurowanie Configuration Manager 8x8

1. Aby zautomatyzować konfigurację w programie 8x8, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj** , aby przekierować użytkownika do aplikacji 8x8. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi 8x8. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować 8x8, zaloguj się do 8x8 [Configuration Manager](https://vo-cm.8x8.com/) jako administrator.

1. Na stronie głównej kliknij pozycję **Zarządzanie tożsamościami**.

    ![Zrzut ekranu, który podświetla kafelek Zarządzanie tożsamościami.](./media/8x8virtualoffice-tutorial/configure1.png)

1. Sprawdź Logowanie jednokrotne **(SSO)** , a następnie wybierz pozycję **Microsoft Azure AD**.

    ![Zrzut ekranu, który wyróżnia opcje logowania jednokrotnego (SSO) i Microsoft Azure AD.](./media/8x8virtualoffice-tutorial/configure2.png)

1. Skopiuj trzy adresy URL i certyfikat podpisywania z strony **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w usłudze Azure AD do sekcji **ustawienia protokołu SAML Microsoft Azure AD** w programie 8x8 Configuration Manager.

    ![8x8 Configuration Manager](./media/8x8virtualoffice-tutorial/configure3.png)

    a. Skopiuj **adres URL logowania** do **adresu URL logowania dostawcy tożsamości**.

    b. Skopiuj **Identyfikator usługi Azure AD** do **adresu URL/nazwy URN wystawcy dostawcy tożsamości**.

    c. Kopiuj **adres URL wylogowywania** na **adres URL wylogowania dostawcy tożsamości**.

    d. Pobierz **certyfikat (base64)** i przekaż go do **certyfikatu**.

    e. Kliknij pozycję **Zapisz**.

### <a name="configure-8x8-account-manager"></a>Konfigurowanie Menedżera kont 8x8

1. Zaloguj się do swojej dzierżawy usługi 8x8 Virtual Office jako administrator.

1. Na panelu aplikacji wybierz pozycję **Menedżer konta usługi 8x8 Virtual Office**.

    ![Zrzut ekranu, który wyróżnia kafelek Menedżer kont w pakiecie Office.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Wybierz konto **firmowe**, którym chcesz zarządzać, a następnie kliknij przycisk **Zaloguj się**.

    ![Zrzut ekranu, który podświetla opcję biznes i przycisk Zaloguj.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Kliknij kartę **KONTA** na liście menu.

    ![Zrzut ekranu, który podświetla kartę konta na liście menu.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Kliknij pozycję **Logowanie jednokrotne** na liście kont.

    ![Zrzut ekranu, który wyróżnia opcję logowania jednokrotnego.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. W obszarze Metody uwierzytelniania wybierz pozycję **Logowanie jednokrotne** i kliknij pozycję **SAML**.

    ![Zrzut ekranu, który podświetla element SAML w obszarze Logowanie jednokrotne.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. W sekcji **Logowanie jednokrotne SAML** wykonaj następujące kroki:

    ![Konfigurowanie po stronie aplikacji](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. W polu tekstowym **Adres URL logowania** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    b. W polu tekstowym **Adres URL wylogowywania** wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    c. W polu tekstowym **Adres URL wystawcy** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    d. Kliknij przycisk **Przeglądaj**, aby przekazać certyfikat pobrany z witryny Azure Portal.

    e. Kliknij przycisk **Zapisz**.

### <a name="create-8x8-test-user"></a>Utwórz użytkownika testowego 8x8

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w 8x8. Aby dodać użytkowników na platformie 8x8, Pracuj z [zespołem pomocy technicznej 8x8](https://www.8x8.com/about-us/contact-us) . Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka 8x8 w panelu dostępu należy automatycznie zalogować się do 8x8, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj 8x8 z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić 8x8 z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)