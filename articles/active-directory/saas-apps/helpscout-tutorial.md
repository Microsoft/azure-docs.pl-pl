---
title: 'Samouczek: integracja Azure Active Directory z usługą Help Scout | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Help Scout.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: 7c5e8210bc8b805d72149fd2ef3335c1d637a58f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92445243"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Samouczek: integracja Azure Active Directory z Help Scout

Z tego samouczka dowiesz się, jak zintegrować aplikację Help Scout z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Help Scout z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Help Scout.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Help Scout (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Help Scout potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji Help Scout z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Help Scout obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług oraz dostawcę tożsamości**
* Aplikacja Help Scout obsługuje aprowizowanie użytkowników typu **Just In Time**

## <a name="adding-help-scout-from-the-gallery"></a>Dodawanie aplikacji Help Scout z galerii

Aby skonfigurować integrację aplikacji Help Scout w usłudze Azure AD, należy dodać aplikację Help Scout z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Help Scout** w polu wyszukiwania.
1. Wybierz pozycję **Help Scout** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego w usłudze Azure AD za pomocą pomocy Scout na podstawie użytkownika testowego o nazwie **B. Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Help Scout.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w aplikacji Help Scout, należy wykonać kroki opisane w poniższych blokach konstrukcyjnych:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj pomoc dla logowania jednokrotnego](#configure-help-scout-sso)** w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika Help Scout test](#create-help-scout-test-user)** , aby uzyskać odpowiednika B. Simon w pomocy Scout, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Help Scout, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Help Scout** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

1. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML, w której można wprowadzić identyfikator, odpowiedź U R L i wybrać pozycję Zapisz.](common/idp-intiated.png)

    a. **Identyfikator** to wartość **Audience URI (Service Provider Entity ID)** (Identyfikator URI odbiorców — identyfikator jednostki usługodawcy) z aplikacji Help Scout, która zaczyna się od: `urn:`

    b. **Adres URL odpowiedzi** to wartość **Post-back URL (Assertion Consumer Service URL)** (Zwrotny adres URL — adres URL usługi Assertion Consumer Service) z aplikacji Help Scout, która zaczyna się od: `https://` 

    > [!NOTE]
    > Wartości tych adresów URL są tylko na potrzeby demonstracyjne. Musisz zaktualizować te wartości na podstawie rzeczywistych wartości adresu URL odpowiedzi i identyfikatora. Wartości te uzyskasz z karty **Single Sign-On** (Logowanie jednokrotne) w sekcji Authentication (Uwierzytelnianie), co zostało wyjaśnione w dalszej części tego samouczka.

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![Zrzut ekranu przedstawia ustawienie dodatkowego U R LS, gdzie można wprowadzić znak U R L.](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://secure.helpscout.net/members/login/`

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie aplikacji Help Scout** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest utworzenie użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wpisz **B. Simon**.
  
    b. W polu **Nazwa użytkownika** wpisz **B. Simon \@ yourcompanydomain. Extension**  
    Na przykład B.Simon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do programu Help Scout.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **Help Scout**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Help Scout**.

    ![Link do aplikacji Help Scout na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** na liście Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-help-scout-sso"></a>Konfigurowanie pomocy dla logowania jednokrotnego

1. Aby zautomatyzować konfigurację w ramach pomocy systemu Scout, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Konfiguracja Help Scout** przekieruje Cię do aplikacji Help Scout. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do pomocy usługi Scout. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować pomoc w usłudze Scout, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie pomocy Help Scout jako administrator i wykonaj następujące czynności:

1. Kliknij pozycję **Manage** (Zarządzaj) w górnym menu, a następnie wybierz pozycję **Company** (Firma) z menu rozwijanego.

    ![Zrzut ekranu przedstawia menu Zarządzaj z wybraną firmą.](./media/helpscout-tutorial/settings1.png)

1. Wybierz pozycję **Authentication** (Uwierzytelnianie) z okienka nawigacji po lewej stronie.

    ![Zrzut ekranu przedstawia wybrane uwierzytelnianie.](./media/helpscout-tutorial/settings2.png)

1. Spowoduje to przejście do sekcji ustawień języka SAML w celu wykonania następujących czynności:

    ![Zrzut ekranu przedstawia kartę pojedyncze Sign-On, w której wprowadzane są określone informacje.](./media/helpscout-tutorial/settings3.png)

    a. Skopiuj wartość **Post-back URL (Assertion Consumer Service URL)** (Zwrotny adres URL — adres URL usługi Assertion Consumer Service) i wklej ją w polu tekstowym **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    b. Skopiuj wartość **Audience URI (Service Provider Entity ID)** (Identyfikator URI odbiorców — identyfikator jednostki usługodawcy) i wklej ją w polu tekstowym **Identyfikator** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Włącz ustawienie **Enable SAML** (Włącz SAML) i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia kartę z pojedynczym Sign-On, w której można włączyć protokół SAML i dodać inne informacje.](./media/helpscout-tutorial/settings4.png)

    a. W polu tekstowym **adres URL pojedynczego Sign-On** wklej wartość **adresu URL logowania**, którą skopiowano z Azure Portal.

    b. Kliknij przycisk **Upload Certificate** (Przekaż certyfikat), aby przekazać **certyfikat (Base64)** pobrany z witryny Azure Portal.

    c. Wprowadź domeny poczty e-mail organizacji, na przykład `contoso.com`, w polu tekstowym **Email Domains** (Domeny poczty e-mail). Wiele domen można oddzielić przecinkami. Za każdym razem, gdy użytkownik lub administrator aplikacji Help Scout wprowadzi tę konkretną domenę na [stronie logowania aplikacji Help Scout](https://secure.helpscout.net/members/login/), zostanie przekierowany do dostawcy tożsamości w celu uwierzytelnienia przy użyciu podanych poświadczeń.

    d. Ponadto możesz włączyć ustawienie **Force SAML Sign-on** (Wymuszaj logowanie SAML), jeśli użytkownicy mają logować się w aplikacji Help Scout tylko za pośrednictwem tej metody. Jeśli chcesz pozostawić im opcję logowania się przy użyciu poświadczeń aplikacji Help Scout, możesz pozostawić to ustawienie wyłączone. Nawet jeśli to ustawienie jest włączone, właściciel konta zawsze będzie mógł zalogować się w aplikacji Help Scout za pomocą hasła do swojego konta.

    e. Kliknij pozycję **Zapisz**.

### <a name="create-help-scout-test-user"></a>Tworzenie użytkownika testowego aplikacji Help Scout

W tej sekcji użytkownik o nazwie B. Simon został utworzony w pomocy Scout. Aplikacja Help Scout obsługuje aprowizację użytkowników typu just-in-time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Help Scout, zostanie utworzony po uwierzytelnieniu.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Help Scout w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Help Scout, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj program Scout z usługą Azure AD](https://aad.portal.azure.com/)