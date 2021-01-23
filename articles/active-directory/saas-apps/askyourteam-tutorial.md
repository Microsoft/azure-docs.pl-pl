---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą AskYourTeam | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i AskYourTeam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: b239811e9fe49f420b5e9d15afafdcf26832dbf8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735406"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą AskYourTeam

W tym samouczku dowiesz się, jak zintegrować usługę AskYourTeam z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi AskYourTeam z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do AskYourTeam.
* Zezwól użytkownikom na automatyczne logowanie się do usługi AskYourTeam przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) AskYourTeam.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa AskYourTeam obsługuje funkcję SSO zainicjowaną przez usługę **SP i dostawcy tożsamości** .

## <a name="adding-askyourteam-from-the-gallery"></a>Dodawanie AskYourTeam z galerii

Aby skonfigurować integrację programu AskYourTeam z usługą Azure AD, musisz dodać AskYourTeam z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **AskYourTeam** w polu wyszukiwania.
1. Wybierz pozycję **AskYourTeam** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-askyourteam"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla AskYourTeam

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą AskYourTeam przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w AskYourTeam.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą AskYourTeam, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-askyourteam-sso)** w usłudze AskYourTeam, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego AskYourTeam](#create-askyourteam-test-user)** , aby dysponować odpowiednikiem B. Simon w AskYourTeam, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **AskYourTeam** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:  `https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu adresu URL rzeczywistego odpowiedzi i wartości adresu URL Sign-On, które zostały wyjaśnione w dalszej części tego samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie AskYourTeam** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi AskYourTeam.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **AskYourTeam**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-askyourteam-sso"></a>Konfigurowanie logowania jednokrotnego AskYourTeam

1. Aby zautomatyzować konfigurację w programie AskYourTeam, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj** , aby przekierować użytkownika do aplikacji AskYourTeam. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi AskYourTeam. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować AskYourTeam, w innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy AskYourTeam jako administrator.

1. Kliknij **moją organizację**.

    ![Zrzut ekranu przedstawia link my Organization.](./media/askyourteam-tutorial/user1.png)

1. Kliknij pozycję **integracji**.

    ![Zrzut ekranu przedstawia link integracji.](./media/askyourteam-tutorial/configure1.png)

1. Kliknij pozycję **Edytuj ustawienia**.

    ![Zrzut ekranu przedstawia komunikat o pojedynczej Sign-On z przyciskiem Edytuj ustawienia.](./media/askyourteam-tutorial/configure2.png)

1. Na stronie **Edytuj integrację pojedynczego Sign-On** wykonaj następujące czynności: 

    ![Zrzut ekranu przedstawia edytowanie integracji z jednym Sign-On, w której można wprowadzić wartości tego kroku.](./media/askyourteam-tutorial/configure3.png)

    a. W polu tekstowym **adres URL usługi SAML Single Sign-On** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    b. W polu tekstowym **Identyfikator jednostki SAML** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    c. W polu tekstowym **adres URL wylogowania** wklej wartość **adresu URL wylogowania** , która została skopiowana z Azure Portal.

    d. Otwórz pobrany **certyfikat (base64)** z Azure Portal do Notatnika i wklej zawartość do pola tekstowego **certyfikatu podpisywania SAML-Base64** .

    > [!NOTE]
    > Alternatywnie można również przekazać plik **XML metadanych Federacji** , klikając opcję **Wybierz plik** .

    e. Kopiuj **adres URL odpowiedzi (adres URL usługi potwierdzenia)** , wklej tę wartość do pola tekstowego **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    f. Skopiuj wartość **adresu URL logowania** , wklej tę wartość do pola tekstowego **adres URL logowania** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    przykład Kliknij pozycję **Zapisz**.

### <a name="create-askyourteam-test-user"></a>Utwórz użytkownika testowego AskYourTeam

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny internetowej AskYourTeam jako administrator.

1. Kliknij **moją organizację**.

    ![Zrzut ekranu przedstawia link my Organization, w którym rozpoczyna się to zadanie.](./media/askyourteam-tutorial/user1.png)

1. Kliknij pozycję **Użytkownicy** i wybierz pozycję **nowy użytkownik**.

    ![Zrzut ekranu przedstawia link użytkownicy z nowym użytkownikiem.](./media/askyourteam-tutorial/user2.png)

1. W sekcji **nowy użytkownik** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia nową sekcję użytkownika, w której wprowadzane są informacje o użytkowniku.](./media/askyourteam-tutorial/user3.png)

    1. W polu tekstowym **imię i nazwisko** Wprowadź imię użytkownika.

    1. W polu **tekstowym nazwisko Wprowadź nazwisko użytkownika** .

    1. W polu tekstowym **adres e-mail** wprowadź EmailAddress użytkownika B.Simon@contoso.com .

    1. Wybierz **rolę** dla użytkownika zgodnie z wymaganiami organizacji.

    1. Kliknij pozycję **Zapisz**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania AskYourTeam, w którym można zainicjować przepływ logowania.

* Przejdź bezpośrednio do adresu URL logowania AskYourTeam i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do AskYourTeam, dla którego skonfigurowano Logowanie jednokrotne

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka AskYourTeam w obszarze Moje aplikacje, jeśli jest skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do AskYourTeam, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu AskYourTeam można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).