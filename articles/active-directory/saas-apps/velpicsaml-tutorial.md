---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Velpic SAML | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Velpic SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.openlocfilehash: 81523a1c11b6a3da4e7d1db5d8249921ad5047ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92635868"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Velpic SAML

W tym samouczku dowiesz się, jak zintegrować Velpic SAML z Azure Active Directory (Azure AD). Gdy integrujesz Velpic SAML z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do Velpic SAML.
* Zezwól użytkownikom na automatyczne logowanie do Velpic SAML przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym w usłudze SAML Velpic.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Velpic **SAML obsługuje logowanie** jednokrotne z użyciem protokołu SSO

## <a name="adding-velpic-saml-from-the-gallery"></a>Dodawanie Velpic SAML z galerii

Aby skonfigurować integrację Velpic SAML z usługą Azure AD, należy dodać Velpic SAML z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Velpic SAML** w polu wyszukiwania.
1. Wybierz pozycję **VELPIC SAML** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Velpic SAML

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Velpic SAML przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Velpic SAML.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Velpic SAML, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj usługę VELPIC SAML SSO](#configure-velpic-saml-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego SAML Velpic](#create-velpic-saml-test-user)** , aby miał odpowiednik B. Simon w Velpic SAML, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **SAML Velpic** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<sub-domain>.velpicsaml.net`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Należy pamiętać, że adres URL logowania będzie dostarczany przez zespół SAML Velpic, a wartość identyfikatora będzie dostępna podczas konfigurowania wtyczki logowania jednokrotnego na Velpic SAML. Należy skopiować tę wartość ze strony aplikacji SAML Velpic i wkleić ją tutaj.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie protokołu SAML Velpic** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Velpic SAML.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **VELPIC SAML**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-velpic-saml-sso"></a>Konfigurowanie rejestracji jednokrotnej Velpic SAML

1. Aby zautomatyzować konfigurację w ramach Velpic SAML, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj VELPIC SAML** przekierowuje do aplikacji Velpic SAML. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do Velpic SAML. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-8.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Velpic SAML, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do witryny firmy Velpic SAML jako administrator i wykonaj następujące czynności:

4. Kliknij przycisk **Zarządzaj** kartami, a następnie przejdź do sekcji **integracja** , w której trzeba kliknąć pozycję **wtyczki** , aby utworzyć nową wtyczkę logowania.

    ![Zrzut ekranu przedstawia stronę integracja, na której można wybrać wtyczki.](./media/velpicsaml-tutorial/velpic_1.png)

5. Kliknij przycisk **"Dodaj plugin" "** .
    
    ![Zrzut ekranu przedstawia wybrany przycisk Dodaj wtyczkę.](./media/velpicsaml-tutorial/velpic_2.png)

6. Kliknij kafelek **SAML** na stronie Dodawanie wtyczki.
    
    ![Zrzut ekranu przedstawia element SAML wybrany na stronie Dodawanie wtyczki.](./media/velpicsaml-tutorial/velpic_3.png)

7. Wprowadź nazwę nowej wtyczki SAML i kliknij przycisk **"Dodaj"** .

    ![Zrzut ekranu przedstawia okno dialogowe Dodaj nowe wtyczki SAML z wprowadzonym systemem Azure A D.](./media/velpicsaml-tutorial/velpic_4.png)

8. Wprowadź następujące szczegóły:

    ![Zrzut ekranu przedstawia stronę usługi Azure A D, na której można wprowadzić podane wartości.](./media/velpicsaml-tutorial/velpic_5.png)

    a. W polu tekstowym **Nazwa** wpisz nazwę wtyczki SAML.

    b. W polu tekstowym **adres URL wystawcy** wklej **Identyfikator usługi Azure AD** skopiowany z okna **Konfigurowanie logowania w** Azure Portal.

    c. W obszarze **Konfiguracja metadanych dostawcy** Przekaż plik XML metadanych pobrany z Azure Portal.

    d. Możesz również włączyć obsługę języka SAML w czasie, włączając opcję **"Utwórz nowego użytkownika** ". Jeśli użytkownik nie istnieje w usłudze Velpic, a ta flaga nie jest włączona, logowanie z platformy Azure zakończy się niepowodzeniem. Jeśli flaga jest włączona, użytkownik zostanie automatycznie zainicjowany do Velpic w czasie logowania. 

    e. Skopiuj **adres URL logowania** jednokrotnego z pola tekstowego i wklej go w Azure Portal.
    
    f. Kliknij pozycję **Zapisz**.

### <a name="create-velpic-saml-test-user"></a>Utwórz użytkownika testowego SAML Velpic

Ten krok zazwyczaj nie jest wymagany, ponieważ aplikacja obsługuje funkcję udostępniania just in Time użytkownika. Jeśli automatyczne Inicjowanie obsługi użytkowników nie jest włączone, ręczne tworzenie użytkowników można wykonać zgodnie z poniższym opisem.

Zaloguj się do witryny firmy SAML Velpic jako administrator i wykonaj następujące czynności:
    
1. Kliknij przycisk Zarządzaj kartami i przejdź do sekcji Użytkownicy, a następnie kliknij przycisk Nowy przycisk, aby dodać użytkowników.

    ![Dodaj użytkownika](./media/velpicsaml-tutorial/velpic_7.png)

2. Na stronie **"Tworzenie nowego użytkownika"** wykonaj następujące czynności.

    ![użytkownik](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. W polu tekstowym **imię i nazwisko** wpisz imię B.

    b. W polu **tekstowym nazwisko wpisz nazwisko Simon** .

    c. W polu tekstowym **Nazwa użytkownika** wpisz nazwę użytkownika B. Simon.

    d. W polu tekstowym adres **e-mail** wpisz adres e-mail B.Simon@contoso.com konta.

    e. Pozostałe informacje są opcjonalne, ale w razie konieczności można je wypełnić.
    
    f. Kliknij przycisk **Zapisz**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

1. Po kliknięciu kafelka SAML Velpic w panelu dostępu należy uzyskać stronę logowania aplikacji Velpic SAML. Na stronie logowania powinien zostać wyświetlony przycisk **"Zaloguj się za pomocą usługi Azure AD"** .

    ![Zrzut ekranu przedstawia Portal uczenia z zaznaczoną opcją Zaloguj się przy użyciu usługi Azure A D.](./media/velpicsaml-tutorial/velpic_6.png)

1. Kliknij przycisk **Zaloguj się przy użyciu usługi Azure AD** , aby zalogować się do usługi Velpic przy użyciu konta usługi Azure AD.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj Velpic SAML z usługą Azure AD](https://aad.portal.azure.com/)