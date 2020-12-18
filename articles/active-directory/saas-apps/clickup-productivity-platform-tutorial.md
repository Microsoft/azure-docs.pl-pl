---
title: 'Samouczek: integracja Azure Active Directory z platformą produktywności ClickUp | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją ClickUp Productivity Platform.
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
ms.openlocfilehash: 753256adddf8b28518bac45011d582bc7a079629
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97670443"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Samouczek: integracja Azure Active Directory z platformą produktywności ClickUp

Z tego samouczka dowiesz się, jak zintegrować aplikację ClickUp Productivity Platform z usługą Azure Active Directory (Azure AD).
Integrowanie aplikacji ClickUp Productivity Platform z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji ClickUp Productivity Platform.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji ClickUp Productivity Platform (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją ClickUp Productivity Platform potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji ClickUp Productivity Platform z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja ClickUp Productivity Platform obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>Dodawanie aplikacji ClickUp Productivity Platform z galerii

Aby skonfigurować integrację aplikacji ClickUp Productivity Platform z usługą Azure AD, należy dodać tę aplikację z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikacji ClickUp Productivity Platform z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **ClickUp Productivity Platform**, wybierz pozycję **ClickUp Productivity Platform** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

    ![Aplikacja ClickUp Productivity Platform na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją ClickUp Productivity Platform, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD a powiązanym użytkownikiem aplikacji ClickUp Productivity Platform.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją ClickUp Productivity Platform, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji ClickUp Productivity Platform](#configure-clickup-productivity-platform-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego w aplikacji ClickUp Productivity Platform](#create-clickup-productivity-platform-test-user)** — aby udostępnić w aplikacji ClickUp Productivity Platform odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji ClickUp Productivity Platform, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **ClickUp Productivity Platform** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji ClickUp Productivity Platform](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://app.clickup.com/login/sso`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > Wartość identyfikatora nie jest prawdziwa. Zaktualizuj tę wartość przy użyciu rzeczywistej wartości identyfikatora opisanej w dalszej części tego samouczka.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji ClickUp Productivity Platform

1. W innym oknie przeglądarki internetowej zaloguj się do dzierżawy aplikacji ClickUp Productivity Platform jako administrator.

2. Kliknij **profil użytkownika**, a następnie wybierz pozycję **Ustawienia**.

    ![Zrzut ekranu przedstawia dzierżawę produktywność ClickUp z wybraną ikoną ustawienia.](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![Zrzut ekranu przedstawia ustawienia.](./media/clickup-productivity-platform-tutorial/configure1.png)

3. W obszarze „Single Sign-On (SSO) Provider” (Dostawca logowania jednokrotnego) wybierz pozycję **Microsoft**.

    ![Zrzut ekranu przedstawia okienko uwierzytelnianie z wybranym przez firmę Microsoft.](./media/clickup-productivity-platform-tutorial/configure2.png)

4. Na stronie **Configure Microsoft Single Sign On** (Konfigurowanie logowania jednokrotnego firmy Microsoft) wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia stronę Konfigurowanie logowania jednokrotnego firmy Microsoft, na której można skopiować jednostkę I D i zapisać metadane federacji platformy Azure U R L.](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. Kliknij przycisk **Copy** (Kopiuj), aby skopiować wartość identyfikatora jednostki, i wklej ją w polu tekstowym **Identyfikator (identyfikator jednostki)** w sekcji **Podstawowa konfiguracja protokołu SAML** witryny Azure Portal.

    b. W polu tekstowym **Azure Federation Metadata URL** (Adres URL metadanych federacyjnych platformy Azure) wklej wartość pola „Adres URL metadanych federacyjnych aplikacji” skopiowaną z witryny Azure Portal i kliknij przycisk **Save** (Zapisz).

5. Aby ukończyć instalację, kliknij pozycję **Authenticate With Microsoft to complete setup** (Uwierzytelnij za pomocą konta Microsoft w celu ukończenia instalacji) i uwierzytelnij się przy użyciu konta Microsoft.

    ![Zrzut ekranu przedstawia przycisk Uwierzytelnij z firmą Microsoft, aby zakończyć instalację.](./media/clickup-productivity-platform-tutorial/configure4.png)

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

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji ClickUp Productivity Platform.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **ClickUp Productivity Platform**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **ClickUp Productivity Platform**.

    ![Link do aplikacji ClickUp Productivity Platform na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-clickup-productivity-platform-test-user"></a>Tworzenie użytkownika testowego w aplikacji ClickUp Productivity Platform

1. W innym oknie przeglądarki internetowej zaloguj się do dzierżawy aplikacji ClickUp Productivity Platform jako administrator.

2. Kliknij **profil użytkownika**, a następnie wybierz pozycję **osoby**.

    ![Zrzut ekranu przedstawia dzierżawę wydajności ClickUp.](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![Zrzut ekranu przedstawia wybrany link osoby.](./media/clickup-productivity-platform-tutorial/user1.png)

3. W polu tekstowym wprowadź adres e-mail użytkownika i kliknij przycisk **Invite** (Zaproś).

    ![Zrzut ekranu przedstawia ustawienia użytkowników zespołu, w których można zapraszać osoby za pośrednictwem poczty e-mail.](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > Użytkownik otrzyma powiadomienie i musi zaakceptować zaproszenie do aktywowania konta.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka ClickUp Productivity Platform w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji ClickUp Productivity Platform, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)