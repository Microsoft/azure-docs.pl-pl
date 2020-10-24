---
title: 'Samouczek: integracja Azure Active Directory z danymi Periscope | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Periscope Data.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: deac4dcde51049d744706b7479f5b5f3bbb4d9c0
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520862"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>Samouczek: integracja Azure Active Directory z danymi Periscope

Z tego samouczka dowiesz się, jak zintegrować aplikację Periscope Data z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Periscope Data z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, kto ma dostęp do aplikacji Periscope Data, w usłudze Azure AD.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Periscope Data (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Periscope Data potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Periscope Data z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Periscope Data obsługuje logowanie jednokrotne zainicjowane przez **dostawcę usługi**

## <a name="adding-periscope-data-from-the-gallery"></a>Dodawanie aplikacji Periscope Data z galerii

Aby skonfigurować integrację aplikacji Periscope Data z usługą Azure AD, musisz dodać tę aplikację z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Periscope Data z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Periscope Data**, wybierz pozycję **Periscope Data** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Periscope Data na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Periscope Data, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Periscope Data.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Periscope Data, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Periscope Data](#configure-periscope-data-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego w aplikacji Periscope Data](#create-periscope-data-test-user)** — aby w aplikacji Periscope Data utworzyć odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z aplikacją Periscope Data, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Periscope Data** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena i adresy URL aplikacji Periscope Data — informacje dotyczące logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz jeden z następujących adresów URL:
    
    ```https
    https://app.periscopedata.com/
    https://app.periscopedata.com/app/<SITENAME>
    ```

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://app.periscopedata.com/<SITENAME>/sso`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp wartości rzeczywistymi wartościami adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta aplikacji Periscope Data](mailto:support@periscopedata.com), aby uzyskać tę wartość, a wartość identyfikatora uzyskasz w sekcji **Konfigurowanie logowania jednokrotnego w aplikacji Periscope Data** w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Periscope Data

1. W innym oknie przeglądarki internetowej zaloguj się do aplikacji Periscope Data jako administrator.

2. Otwórz menu koła zębatego w lewym dolnym rogu i otwórz menu zabezpieczenia **rozliczeń**  >  **Security** i wykonaj następujące czynności. Tylko administratorzy mają dostęp do tych ustawień.

    ![Zrzut ekranu przedstawiający okno dialogowe "zabezpieczenia" z wybranymi ustawieniami.](./media/periscope-data-tutorial/configure01.png)

    a. Skopiuj **adres URL metadanych federacji aplikacji** z kroku 5. **Certyfikat podpisywania SAML** i otwórz go w przeglądarce. Spowoduje to otwarcie dokumentu XML.

    b. W polu tekstowym **Logowanie jednokrotne** wybierz pozycję **Azure Active Directory**.

    c. Znajdź tag **SingleSignOnService** i wklej wartość pola**Location** (Lokalizacja) w polu tekstowym **SSO URL** (Adres URL logowania jednokrotnego).

    d. Znajdź tag **SingleLogoutService** i wklej wartość pola**Location** w polu tekstowym **SLO URL** (Adres URL wylogowania jednokrotnego).

    e. Skopiuj wartość **identyfikatora** wystąpienia i wklej ją w polu **Identyfikator (identyfikator jednostki)** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    f. Znajdź pierwszy tag pliku XML, skopiuj wartość **entityID** i wklej ją w polu tekstowym **wystawcy** .

    przykład Znajdź tag **IDPSSODescriptor** przy użyciu protokołu SAML. W tej sekcji znajdź tag **KeyDescriptor** przy użyciu polecenia **use=signing**. skopiuj wartość **X509Certificate** i wklej ją w polu tekstowym **Certyfikat**.

    h. W witrynach z wieloma przestrzeniami można wybrać domyślną przestrzeń w menu rozwijanym **Default Space** (Domyślna przestrzeń). Jest to przestrzeń, do której dodawani są nowy użytkownicy podczas pierwszego logowania w aplikacji Periscope Data i aprowizacji za pomocą logowania jednokrotnego w usłudze Active Directory.

    i. Na koniec kliknij polecenie **Save** (Zapisz) i **potwierdź** zmianę ustawień logowania jednokrotnego, wpisując **Logout** (Wyloguj).

    ![Zrzut ekranu przedstawiający okno dialogowe "aktualizacja konfiguracji S" z wylogowaniem, które zostało wpisane w polu tekstowym i wybranym przycisku "Potwierdź".](./media/periscope-data-tutorial/configure02.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon \@ yourcompanydomain. Extension**  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego na platformie Azure, udzielając dostępu do aplikacji Periscope Data.

1. W witrynie Azure Portal wybierz kolejno pozycje **Aplikacje dla przedsiębiorstw**, **Wszystkie aplikacje** oraz **Periscope Data**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz aplikację **Periscope Data**.

    ![Link do aplikacji Periscope Data na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-periscope-data-test-user"></a>Tworzenie użytkownika testowego w aplikacji Periscope Data

Użytkownicy usługi Azure AD mogą logować się w aplikacji Periscope Data po aprowizowaniu w usłudze Periscope Data. W aplikacji Periscope Data aprowizowanie wykonywane jest ręcznie.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się w aplikacji Periscope Data jako administrator.

2. Kliknij ikonę **Settings** (Ustawienia) w lewym dolnym rogu menu i przejdź do pozycji **Permissions** (Uprawnienia).

    ![Zrzut ekranu pokazujący menu "Ustawienia" z wybranymi przyciskami "uprawnienia".](./media/periscope-data-tutorial/configure03.png)

3. Kliknij pozycję **ADD USER** (Dodaj użytkownika) i wykonaj następujące czynności:

      ![Informacje na temat konfigurowania aplikacji Periscope Data](./media/periscope-data-tutorial/configure04.png)

    a. W polu tekstowym **First Name** (Imię) wprowadź imię użytkownika, na przykład **Britta**.

    b. W polu tekstowym **nazwisko** Wprowadź nazwisko użytkownika, np. **Simon**.

    c. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, np. **brittasimon \@ contoso.com**.

    d. Kliknij przycisk **Dodaj**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Periscope Data w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Periscope Data, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)