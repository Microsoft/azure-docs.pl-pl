---
title: 'Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) przy użyciu programu Evernote | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Evernote.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.openlocfilehash: b22bf4ee3ff830a80ee97851f08173257697c50d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826494"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) przy użyciu programu Evernote

W tym samouczku dowiesz się, jak zintegrować program Evernote z usługą Azure Active Directory (Azure AD). Po zintegrowaniu programu Evernote z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do programu Evernote.
* Zezwól użytkownikom na automatyczne logowanie do programu Evernote przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Evernote obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług oraz dostawcę tożsamości**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-evernote-from-the-gallery"></a>Dodawanie aplikacji Evernote z galerii

Aby skonfigurować integrację aplikacji Evernote z usługą Azure AD, należy dodać aplikację Evernote z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Evernote** w polu wyszukiwania.
1. Wybierz opcję **Evernote** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu Evernote

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu Evernote przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Evernote.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu Evernote, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj rejestrację jednokrotną programu Evernote](#configure-evernote-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego programu Evernote](#create-evernote-test-user)** , aby dysponować odpowiednikiem B. Simon w programie Evernote, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacją **Evernote** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Identyfikator** wpisz adres URL: `https://www.evernote.com/saml2`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wpisz adres URL:  `https://www.evernote.com/Login.action`

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. Aby zmodyfikować opcje **podpisywania** , kliknij przycisk **Edytuj** , aby otworzyć okno dialogowe **certyfikat podpisywania SAML** .

    ![Zrzut ekranu przedstawiający okno dialogowe "S A M L Signing Certificate" z wybranym przyciskiem "Edytuj".](common/edit-certificate.png) 

    ![image (obraz)](./media/evernote-tutorial/samlassertion.png)

    a. Wybierz opcję **podpisanie odpowiedzi SAML i potwierdzenie** dla **opcji podpisywania**.

    b. Kliknij pozycję **Zapisz**

1. W sekcji **Konfigurowanie programu Evernote** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do programu Evernote.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Evernote**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-evernote-sso"></a>Konfigurowanie rejestracji jednokrotnej w programie Evernote

1. Aby zautomatyzować konfigurację w programie Evernote, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Konfiguracja programu Evernote** przekieruje Cię do aplikacji Evernote. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do programu Evernote. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować program Evernote, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy Evernote jako administrator i wykonaj następujące czynności:

4. Przejdź do pozycji **Admin Console** (Konsola administracyjna)

    ![Konsola administracyjna](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. Z obszaru **Admin Console** (Konsola administracyjna) przejdź do pozycji **Security** (Zabezpieczenia) i wybierz pozycję **Single Sign-On** (Logowanie jednokrotne)

    ![Ustawienia logowania jednokrotnego](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Skonfiguruj następujące wartości:

    ![Ustawienia certyfikatu](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Włącz logowanie jednokrotne:** Logowanie jednokrotne jest domyślnie włączone (kliknij polecenie **Wyłącz logowanie** jednokrotne, aby usunąć wymaganie logowania jednokrotnego)

    b. W polu tekstowym **SAML HTTP Request URL** (Adres URL żądań HTTP SAML) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    c. Otwórz w Notatniku certyfikat pobrany z usługi Azure AD, skopiuj zawartość włącznie z pozycjami „BEGIN CERTIFICATE” i „END CERTIFICATE”, a następnie wklej ją w polu tekstowym **X.509 Certificate** (Certyfikat X.509). 

    d. Kliknij pozycję **Save Changes** (Zapisz zmiany)

### <a name="create-evernote-test-user"></a>Tworzenie użytkownika testowego aplikacji Evernote

Aby umożliwić użytkownikom usługi Azure AD logowanie się do programu Evernote, muszą one być obsługiwane w programie Evernote.  
W aplikacji Evernote aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konta użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy Evernote jako administrator.

2. Kliknij pozycję **Admin Console** (Konsola administracyjna).

    ![Konsola administracyjna](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Z obszaru **Admin Console** (Konsola administracyjna) przejdź do pozycji **Add users** (Dodaj użytkowników).

    ![Zrzut ekranu pokazujący menu "Użytkownicy" z wybraną pozycją "Dodaj użytkowników".](./media/evernote-tutorial/create_aaduser_0001.png)

4. W obszarze **Add team members** (Dodaj członków zespołu) w polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail konta użytkownika i kliknij pozycję **Invite** (Zaproś).

    ![Dodawanie użytkownika testowego](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Po wysłaniu zaproszenia właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z prośbą o zaakceptowanie zaproszenia.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Evernote na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Evernote, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj program Evernote z usługą Azure AD](https://aad.portal.azure.com/)

