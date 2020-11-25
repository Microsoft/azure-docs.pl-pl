---
title: 'Samouczek: integracja Azure Active Directory z usługą IdeaScale | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją IdeaScale.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.openlocfilehash: ef04bf9f6d9ca6ff5089d2826c05a5acfb759b04
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001664"
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Samouczek: integracja Azure Active Directory z usługą IdeaScale

Z tego samouczka dowiesz się, jak zintegrować aplikację IdeaScale z usługą Azure Active Directory (Azure AD).
Integracja aplikacji IdeaScale z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji IdeaScale.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji IdeaScale (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją IdeaScale są potrzebne następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji IdeaScale z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja IdeaScale obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="adding-ideascale-from-the-gallery"></a>Dodawanie aplikacji IdeaScale z galerii

Aby skonfigurować integrację aplikacji IdeaScale z usługą Azure AD, należy dodać aplikację IdeaScale z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację IdeaScale z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **IdeaScale**, wybierz pozycję **IdeaScale** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja IdeaScale na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją IdeaScale, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji IdeaScale.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją IdeaScale, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji IdeaScale](#configure-ideascale-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji IdeaScale](#create-ideascale-test-user)** — aby w aplikacji IdeaScale utworzyć odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD przy użyciu aplikacji IdeaScale, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **IdeaScale** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji IdeaScale](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.ideascale.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: 
    
    ```http
    http://<companyname>.ideascale.com
    https://<companyname>.ideascale.com
    ```

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji IdeaScale](https://support.ideascale.com/), aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie aplikacji IdeaScale** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-ideascale-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji IdeaScale

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji IdeaScale jako administrator.

2. Przejdź do sekcji **Community Settings** (Ustawienia społeczności).

    ![Ustawienia społeczności](./media/ideascale-tutorial/ic790847.png "Ustawienia społeczności")

3. Wybierz pozycję **Security \> Single Signon Settings** (Ustawienia > Ustawienia logowania jednokrotnego).

    ![Zrzut ekranu przedstawia pojedyncze ustawienia jednokrotnego wybrane z menu zabezpieczenia.](./media/ideascale-tutorial/ic790848.png "Pojedyncze ustawienia jednokrotnego")

4. W polu **Single-Signon Type** (Typ logowania jednokrotnego) wybierz opcję **SAML 2.0**.

    ![Pojedynczy typ jednokrotnego](./media/ideascale-tutorial/ic790849.png "Pojedynczy typ jednokrotnego")

5. W oknie dialogowym **Single Signon Settings** (Ustawienia logowania jednokrotnego) wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia pojedyncze okno dialogowe ustawień jednokrotnego.](./media/ideascale-tutorial/ic790850.png "Pojedyncze ustawienia jednokrotnego")

    a. W polu tekstowym **SAML IdP Entity ID** (Identyfikator jednostki dostawcy tożsamości SAML) wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    b. Otwórz w Notatniku plik metadanych pobrany z witryny Azure Portal, skopiuj jego zawartość i wklej ją w polu tekstowym **SAML IdP Metadata** (Metadane dostawcy tożsamości SAML).

    c. W polu tekstowym **Logout Success URL** (Adres URL pomyślnego wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    d. Kliknij przycisk **Zapisz zmiany**.

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji IdeaScale.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **IdeaScale**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **IdeaScale**.

    ![Link IdeaScale na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-ideascale-test-user"></a>Tworzenie użytkownika testowego aplikacji IdeaScale

Aby umożliwić użytkownikom usługi Azure AD logowanie się w aplikacji IdeaScale, muszą być oni aprowizowaniu w aplikacji IdeaScale. W aplikacji IdeaScale aprowizacja jest zadaniem ręcznym.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. Zaloguj się w witrynie firmowej aplikacji **IdeaScale** jako administrator.

2. Przejdź do sekcji **Community Settings** (Ustawienia społeczności).

    ![Ustawienia społeczności](./media/ideascale-tutorial/ic790847.png "Ustawienia społeczności")

3. Wybierz pozycję **Basic Settings \> Member Management** (Ustawienia podstawowe > Zarządzanie członkami).

4. Kliknij pozycję **Add Member** (Dodaj członka).

    ![Zarządzanie elementami członkowskimi](./media/ideascale-tutorial/ic790852.png "Zarządzanie elementami członkowskimi")

5. W sekcji Add New Member (Dodaj nowego członka) wykonaj następujące kroki:

    ![Dodaj nowy element członkowski](./media/ideascale-tutorial/ic790853.png "Dodaj nowy element członkowski")

    a. W polu tekstowym **Email Addresses** (Adresy e-mail) wpisz adres e-mail prawidłowego konta usługi Azure AD, które chcesz aprowizować.

    b. Kliknij przycisk **Zapisz zmiany**.

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem w celu potwierdzenia konta, zanim stanie się ono aktywne.

> [!NOTE]
> Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników IdeaScale i interfejsów API udostępnionych przez usługę IdeaScale.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka IdeaScale w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji IdeaScale, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)