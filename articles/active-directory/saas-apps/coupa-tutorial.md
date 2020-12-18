---
title: 'Samouczek: integracja Azure Active Directory z usługą łączenie | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Coupa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.openlocfilehash: c6d102f4d9a67f58469242cb44d2c6466782f006
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674184"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Samouczek: integracja Azure Active Directory z łączeniem

Z tego samouczka dowiesz się, jak zintegrować aplikację Coupa z usługą Azure Active Directory (Azure AD).
Zintegrowanie aplikacji Coupa z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji Coupa.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Coupa (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Coupa, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Coupa z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Coupa obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="adding-coupa-from-the-gallery"></a>Dodawanie aplikacji Coupa z galerii

Aby skonfigurować integrację aplikacji Coupa z usługą Azure AD, musisz dodać aplikację Coupa z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Coupa z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Coupa**, wybierz pozycję **Coupa** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

    ![Aplikacja Coupa na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Coupa, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Coupa.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD dla aplikacji Coupa, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Coupa](#configure-coupa-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Coupa](#create-coupa-test-user)** — aby mieć w aplikacji Coupa odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Coupa, wykonaj następujące kroki:

1. W [witrynie Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Coupa** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji Coupa](common/sp-identifier-reply.png)

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Aby uzyskać tę wartość, skontaktuj się z [zespołem pomocy technicznej i obsługi klienta Coupa](https://success.coupa.com/Support/Contact_Us?).

    b. W polu **Identyfikator** wpisz adres URL: 

    | Środowisko  | Adres URL |
    |:-------------|----|
    | Piaskownica | `sso-stg1.coupahost.com`|
    | Produkcja | `sso-prd1.coupahost.com`|
    | | |

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: 

    | Środowisko | Adres URL |
    |------------- |----|
    | Piaskownica | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Produkcja | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie aplikacji Coupa** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-coupa-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji Coupa

1. Zaloguj się do witryny firmy w aplikacji Coupa jako administrator.

2. Przejdź do pozycji **Setup (Konfiguracja) \> Security Control (Kontrola zabezpieczeń)**.

    ![Kontrolki zabezpieczeń](./media/coupa-tutorial/ic791900.png "Mechanizmy zabezpieczeń")

3. W sekcji **Log in using Coupa credentials** (Zaloguj się przy użyciu poświadczeń Coupa) wykonaj następujące kroki:

    ![Łączenie metadanych SP](./media/coupa-tutorial/ic791901.png "Łączenie metadanych SP")

    a. Wybierz pozycję **Log in using SAML** (Zaloguj się za pośrednictwem protokołu SAML).

    b. Kliknij przycisk **Browse** (Przeglądaj), aby przekazać metadane pobrane z witryny Azure Portal.

    c. Kliknij pozycję **Zapisz**.

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

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Coupa.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Coupa**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz aplikację **Coupa**.

    ![Link aplikacji Coupa na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-coupa-test-user"></a>Tworzenie użytkownika testowego aplikacji Coupa

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Coupa, należy aprowizować ich w aplikacji Coupa.  

* W przypadku aplikacji Coupa aprowizacja jest zadaniem ręcznym.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy w aplikacji **Coupa** jako administrator.

2. W menu u góry kliknij opcję **Setup** (Konfiguracja), a następnie kliknij pozycję **Users** (Użytkownicy).

    ![Użytkownicy](./media/coupa-tutorial/ic791908.png "Użytkownicy")

3. Kliknij pozycję **Utwórz**.

    ![Tworzenie użytkowników](./media/coupa-tutorial/ic791909.png "Tworzenie użytkowników")

4. W sekcji **User Create** (Tworzenie użytkownika) wykonaj następujące kroki:

    ![Szczegóły użytkownika](./media/coupa-tutorial/ic791910.png "Szczegóły użytkownika")

    a. W polach tekstowych **Login**, **First name** (Imię), **Last Name** (Nazwisko), **Single Sign-On ID** (Identyfikator logowania jednokrotnego) i **Email** (Adres e-mail) wpisz wartości prawidłowego konta usługi Azure Active Directory, które chcesz aprowizować.

    b. Kliknij pozycję **Utwórz**.

    >[!NOTE]
    >Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem w celu potwierdzenia konta, zanim stanie się ono aktywne.
    >

>[!NOTE]
>Do udostępniania kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników i interfejsów API dostarczonych przez program.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Coupa w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Coupa, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)