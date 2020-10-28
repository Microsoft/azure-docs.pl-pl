---
title: 'Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) przy użyciu narzędzia Obejrzyj według kolorów | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i oglądać kolory.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.openlocfilehash: fae276049ec3ff6c4bf96be38ff0d2a952731a15
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636786"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z czujki przez kolory

W tym samouczku dowiesz się, jak zintegrować usługę Watch przez kolory z Azure Active Directory (Azure AD). Po zintegrowaniu oglądania przez kolory z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do oglądania przez kolory.
* Zezwól użytkownikom na automatyczne logowanie się, aby oglądać kolory na kontach usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Obejrzyj w ramach subskrypcji z włączoną obsługą logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Kontrolki według kolorów obsługują usługę **SP i dostawcy tożsamości** zainicjowane Logowanie jednokrotne

## <a name="adding-watch-by-colors-from-the-gallery"></a>Dodawanie czujki przez kolory z galerii

Aby skonfigurować integrację oglądania przez kolory z usługą Azure AD, musisz dodać kontrolkę Obejrzyj według kolorów z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje** .
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** .
1. W sekcji **Dodaj z galerii** wpisz **Watch według kolorów** w polu wyszukiwania.
1. Wybierz pozycję **Obejrzyj według kolorów** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby oglądania przez kolory

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD przy użyciu funkcji oglądania przez kolory za pomocą użytkownika testowego o nazwie **B. Simon** . Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w obszarze Obejrzyj według kolorów.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą kolorów Watch, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj kontrolowanie według kolorów Logowanie jednokrotne](#configure-watch-by-colors-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego czujki według kolorów](#create-watch-by-colors-test-user)** , aby uzyskać odpowiednika B. Simon w Obejrzyj według kolorów, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na [Azure Portal](https://portal.azure.com/)na stronie " **Obejrzyj według kolorów** " integracji aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne** .
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML** .
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja została wstępnie skonfigurowana w trybie inicjalizacji **dostawcy tożsamości** , a wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz** .

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wpisz adres URL:  `https://app.colorscorporation.com/login`

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory** , wybierz pozycję **Użytkownicy** , a następnie wybierz pozycję **Wszyscy użytkownicy** .
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło** .
   1. Kliknij pozycję **Utwórz** .

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do oglądania przez kolory.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje** .
1. Na liście Aplikacje wybierz pozycję **Obejrzyj według kolorów** .
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy** .

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika** , a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-watch-by-colors-sso"></a>Konfigurowanie czujki według kolorów Logowanie jednokrotne

1. Aby zautomatyzować konfigurację w obszarze czujki według kolorów, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie** .

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję Obejrzyj, aby **zobaczyć** , że aplikacja będzie kierować do aplikacji czujka przez kolory. W tym miejscu podaj poświadczenia administratora, aby zalogować się do czujki przez kolory. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-5.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować kontrolki przez kolory, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy Watch przez kolory jako administrator i wykonaj następujące czynności:

4. W prawym górnym rogu strony kliknij pozycję **profil**  >  **Ustawienia konta**  >  **Logowanie jednokrotne (logowanie** jednokrotne).

    ![Zrzut ekranu przedstawia stronę ustawień konta, na której S jest wyłączona.](./media/watch-by-colors-tutorial/config01.png)

5. Na stronie Logowanie **jednokrotne (logowanie** jednokrotne) wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia kartę Konfiguracja protokołu SAML umożliwiającą włączenie protokołu SAML.](./media/watch-by-colors-tutorial/config02.png)

    a. Przełącz **opcję Włącz protokół SAML** na **włączony** .

    b. W polu tekstowym **adres URL** wklej **adres URL metadanych Federacji** , który został skopiowany z Azure Portal.

    c. Kliknij przycisk **Importuj** , a następnie następujące pola automatycznie wypełnią na stronie.

    d. Kliknij pozycję **Zapisz** .

### <a name="create-watch-by-colors-test-user"></a>Tworzenie użytkownika testowego czujki przez kolory

Aby umożliwić użytkownikom usługi Azure AD logowanie się w celu oglądania według kolorów, muszą one być obsługiwane do oglądania przez kolory. W obszarze Obejrzyj według kolorów Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się, aby obejrzeć kolory jako administrator zabezpieczeń.

1. W prawym górnym rogu strony kliknij pozycję **Profile**  >  **Użytkownicy**  >  **Dodaj użytkownika** .

    ![Zrzut ekranu przedstawia stronę użytkownicy.](./media/watch-by-colors-tutorial/config03.png)

1. Na stronie **szczegóły użytkownika** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia szczegóły użytkownika, w którym można wprowadzić podane wartości.](./media/watch-by-colors-tutorial/config04.png)

    a. W polu tekstowym **imię i nazwisko** , wprowadź imię użytkownika, np. **B** .

    b. W polu tekstowym **nazwisko** Wprowadź nazwisko użytkownika, np. **Simon** .

    c. W polu tekstowym **Email** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład `B.Simon@contoso.com`.

    d. W polu tekstowym **hasło** wprowadź hasło.

    e. Wybierz **uprawnienia konta** zgodnie z Twoją organizacją.

    f. Kliknij pozycję **Zapisz** .

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Obejrzyj według kolorów w panelu dostępu należy automatycznie zalogować się do opcji Obejrzyj według kolorów, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj czujkę według kolorów w usłudze Azure AD](https://aad.portal.azure.com/)