---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z miejscem pracy w serwisie Facebook | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Workplace by Facebook.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.openlocfilehash: fade4176cbbf432aabae4422553a3caf0235ebed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88526436"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z miejscem pracy w serwisie Facebook

W tym samouczku dowiesz się, jak zintegrować miejsce pracy w usłudze Facebook z Azure Active Directory (Azure AD). Gdy integrujesz miejsce pracy w usłudze Facebook z usługą Azure AD, możesz:

* Kontrola w usłudze Azure AD, która ma dostęp do miejsca pracy w serwisie Facebook.
* Zezwól użytkownikom na automatyczne logowanie do miejsca pracy przez serwis Facebook przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Miejsce pracy przez subskrypcję z włączonym logowaniem jednokrotnym w serwisie Facebook.

> [!NOTE]
> Serwis Facebook oferuje dwa produkty: Workplace Standard (bezpłatny) i Workplace Premium (płatny). W przypadku dowolnej dzierżawy Workplace Premium można skonfigurować integrację ze standardem SCIM i logowaniem jednokrotnym bez żadnych dodatkowych kosztów czy licencji. Logowanie jednokrotne i standard SCIM nie są dostępne w wystąpieniach Workplace Standard.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Workplace by Facebook obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**
* Aplikacja Workplace by Facebook obsługuje **aprowizację just in time**
* Aplikacja Workplace by Facebook obsługuje **[automatyczne aprowizowanie użytkowników](workplacebyfacebook-provisioning-tutorial.md)**
* W miejscu pracy według aplikacji mobilnej Facebook można teraz skonfigurować usługę Azure AD w celu włączenia logowania jednokrotnego. W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.
* Po skonfigurowaniu miejsca pracy przez serwis Facebook można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Dodawanie aplikacji Workplace by Facebook z galerii

Aby skonfigurować integrację aplikacji Workplace by Facebook z usługą Azure AD, należy dodać aplikację Workplace by Facebook z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **miejsce pracy według serwisu Facebook** .
1. Wybierz pozycję **miejsce pracy przez serwis Facebook** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla miejsca pracy przez serwis Facebook

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD w miejscu pracy przez serwis Facebook przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w miejscu pracy przez serwis Facebook.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD w miejscu pracy przez serwis Facebook, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
2. **[Skonfiguruj miejsce pracy przez logowanie jednokrotne w serwisie Facebook](#configure-workplace-by-facebook-sso)** , aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    * **[Utwórz miejsce pracy przez użytkownika testowego serwisu Facebook](#create-workplace-by-facebook-test-user)** , aby dysponować odpowiednikiem B. Simon w miejscu pracy przez serwis Facebook, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **miejsce pracy według integracji aplikacji w serwisie Facebook** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<instancename>.facebook.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://www.facebook.com/company/<instanceID>`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE]
    > Te wartości nie są rzeczywiste. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Zobacz stronę uwierzytelnianie na pulpicie nawigacyjnym firmy w miejscu pracy, aby uzyskać prawidłowe wartości społeczności w miejscu pracy, co zostało opisane w dalszej części tego samouczka.

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie miejsca pracy według serwisu Facebook** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do miejsca pracy w serwisie Facebook.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **miejsce pracy w serwisie Facebook**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-workplace-by-facebook-sso"></a>Konfigurowanie miejsca pracy przez logowanie jednokrotne w usłudze Facebook

1. Aby zautomatyzować konfigurację w miejscu pracy przez serwis Facebook, musisz zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Skonfiguruj miejsce pracy w serwisie Facebook** przekieruje Cię do miejsca pracy przez aplikację w serwisie Facebook. W tym miejscu podaj poświadczenia administratora, aby zalogować się do miejsca pracy w serwisie Facebook. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-5.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować miejsce pracy przez serwis Facebook, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do miejsca pracy przez witrynę firmy w serwisie Facebook jako administrator i wykonaj następujące czynności:

    > [!NOTE]
    > W ramach procesu uwierzytelniania SAML aplikacja Workplace może używać ciągów zapytań o rozmiarze do 2,5 kilobajta w celu przekazania parametrów do usługi Azure AD.

1. W okienku nawigacji po lewej stronie przejdź do **Security**  >  karty**uwierzytelnianie** zabezpieczeń.

    ![Panel administracyjny](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Zaznacz opcję logowania jednokrotnego **(SSO)** .
    
    b. Kliknij pozycję **+ Dodaj nowego dostawcę rejestracji jednokrotnej**.
    > [!NOTE]
    > Upewnij się, że pole wyboru Logowanie przy użyciu hasła jest również zaznaczone. Administratorzy mogą potrzebować tej opcji, aby zalogować się podczas przerzucania certyfikatu w celu zaniechania zablokowania.

1. Na karcie **Uwierzytelnianie** wybierz pozycję **Logowanie jednokrotne**, a następnie wykonaj następujące czynności:

    ![Karta Uwierzytelnianie](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. W polu **Nazwa dostawcy rejestracji jednokrotnej**wprowadź nazwę wystąpienia logowania jednokrotnego, na przykład Azureadsso.

    b. W polu tekstowym **Adres URL SAML** wklej wartość **Adres URL logowania** skopiowaną z witryny Azure Portal.

    c. W polu tekstowym **adres URL wystawcy SAML** wklej wartość **identyfikatora usługi Azure AD**, która została skopiowana z Azure Portal.

    d. Otwórz w Notatniku swój **certyfikat zakodowany w formacie base-64** pobrany z witryny Azure Portal, skopiuj zawartość do schowka, a następnie wklej ją w polu tekstowym **Certyfikat SAML**.

    e. Skopiuj **adres URL odbiorców** swojego wystąpienia i wklej go w polu **Identyfikator (identyfikator jednostki)** w sekcji **Podstawowa konfiguracja SAML** na Azure Portal.

    f. Skopiuj **adres URL adresata** swojego wystąpienia i wklej go w polu tekstowym **Logowanie do adresu URL** w sekcji **Podstawowa konfiguracja SAML** na Azure Portal.

    przykład Skopiuj **adres URL ACS (usługa konsumencka odbiorcy)** dla swojego wystąpienia i wklej go w polu tekstowym **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** na Azure Portal.

    h. Przewiń w dół do końca sekcji i kliknij przycisk **Testuj logowanie jednokrotne**. Zostanie wtedy otworzone okno podręczne ze stroną logowania usługi Azure AD. Wprowadź swoje poświadczenia jak zwykle w celu uwierzytelnienia.

    **Rozwiązywanie problemów:** Upewnij się, że adres e-mail zwrotny z usługi Azure AD jest taki sam jak konto w miejscu pracy, na którym się zalogowano.

    i. Po pomyślnym zakończeniu testu przewiń w dół do końca strony i kliknij przycisk **Zapisz**.

    j. Wszystkim użytkownikom korzystającym z aplikacji Workplace zostanie wyświetlona strona logowania usługi Azure AD w celu uwierzytelnienia.

1. **Przekierowanie wylogowania SAML (opcjonalnie)** -

    Możesz opcjonalnie skonfigurować adres URL wylogowywania SAML, który może prowadzić do strony wylogowywania usługi Azure AD. Gdy to ustawienie jest włączone i skonfigurowane, użytkownik nie będzie przekierowywany do strony wylogowywania aplikacji Workplace. Zamiast tego użytkownik zostanie przekierowany do adresu URL dodanego do ustawienia Przekierowanie po wylogowaniu SAML.

### <a name="configuring-reauthentication-frequency"></a>Konfigurowanie częstotliwości ponownego uwierzytelniania

Możesz skonfigurować aplikację Workplace, aby wyświetlała monit o sprawdzenie protokołu SAML codziennie, co tydzień, co 2 tygodnie, co miesiąc lub aby nie wyświetlała go wcale.

> [!NOTE]
> Minimalna wartość sprawdzania protokołu SAML w przypadku aplikacji mobilnych to 1 tydzień.

Możesz również wymusić Resetowanie SAML dla wszystkich użytkowników za pomocą przycisku: Wymagaj uwierzytelniania SAML dla wszystkich użytkowników.

### <a name="create-workplace-by-facebook-test-user"></a>Tworzenie użytkownika testowego aplikacji Workplace by Facebook

W tej sekcji użytkownik o nazwie B. Simon został utworzony w miejscu pracy przez serwis Facebook. Aplikacja Workplace by Facebook obsługuje aprowizację just in time, która jest domyślnie włączona.

W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje w aplikacji Workplace by Facebook, podczas próby uzyskania dostępu do aplikacji Workplace by Facebook zostanie utworzony nowy użytkownik.

>[!Note]
>Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Workplace by Facebook](https://www.workplace.com/help/work/)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Workplace by Facebook w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Workplace by Facebook, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Testowanie logowania jednokrotnego w miejscu pracy przez serwis Facebook (urządzenia przenośne)

1. Otwórz obszar roboczy za pomocą aplikacji mobilnej w serwisie Facebook. Na stronie logowania kliknij pozycję **Zaloguj**się.

    ![Logowanie](./media/workplacebyfacebook-tutorial/test05.png)

2. Wprowadź służbowy adres e-mail i kliknij pozycję **Kontynuuj**.

    ![Wiadomość e-mail](./media/workplacebyfacebook-tutorial/test02.png)

3. Kliknij opcję **tylko raz**.

    ![Raz](./media/workplacebyfacebook-tutorial/test04.png)

4. Kliknij przycisk **Zezwalaj**.

    ![Zezwalaj](./media/workplacebyfacebook-tutorial/test03.png)

5. Na koniec po pomyślnym zalogowaniu zostanie wyświetlona strona główna aplikacji.    

    ![Strona główna](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurowanie aprowizacji użytkowników](workplacebyfacebook-provisioning-tutorial.md)

- [Wypróbuj miejsce pracy w usłudze Facebook przy użyciu usługi Azure AD](https://aad.portal.azure.com)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
