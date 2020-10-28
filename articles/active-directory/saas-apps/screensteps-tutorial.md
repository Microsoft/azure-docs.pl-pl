---
title: 'Samouczek: integracja Azure Active Directory z usługą ScreenSteps | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i ScreenSteps.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 3816239798290318404980ded388b726d8134395
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895188"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Samouczek: integracja Azure Active Directory z usługą ScreenSteps

W tym samouczku dowiesz się, jak zintegrować usługę ScreenSteps z usługą Azure Active Directory (Azure AD).
Integracja ScreenSteps z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi ScreenSteps.
* Możesz pozwolić użytkownikom na automatyczne logowanie do ScreenSteps (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą ScreenSteps, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z włączonym logowaniem jednokrotnym w ScreenSteps

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* ScreenSteps obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-screensteps-from-the-gallery"></a>Dodawanie ScreenSteps z galerii

Aby skonfigurować integrację programu ScreenSteps z usługą Azure AD, musisz dodać ScreenSteps z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać ScreenSteps z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory** .

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje** .

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **ScreenSteps** , wybierz pozycję **ScreenSteps** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![ScreenSteps na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą ScreenSteps na podstawie użytkownika testowego o nazwie **Britta Simon** .
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ScreenSteps.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi ScreenSteps, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-screensteps-single-sign-on)** jednokrotne w usłudze ScreenSteps, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego ScreenSteps](#create-screensteps-test-user)** , aby uzyskać odpowiednik Britta Simon w ScreenSteps, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą ScreenSteps, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **ScreenSteps** wybierz pozycję **Logowanie jednokrotne** .

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed** , aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj** , aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![ScreenSteps domenę i adresy URL Logowanie jednokrotne](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj tę wartość za pomocą rzeczywistego adresu URL Sign-On, który został wyjaśniony w dalszej części tego samouczka.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz** , aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie ScreenSteps** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-screensteps-single-sign-on"></a>Konfigurowanie pojedynczego Sign-On ScreenSteps

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny ScreenSteps jako administrator.

1. Kliknij pozycję **Ustawienia konta** .

    ![Zarządzanie kontami](./media/screensteps-tutorial/ic778523.png "Zarządzanie kontami")

1. Kliknij pozycję **Logowanie jednokrotne** .

    ![Zrzut ekranu przedstawiający wybraną wartość "Logowanie jednokrotne".](./media/screensteps-tutorial/ic778524.png "Zdalne uwierzytelnianie")

1. Kliknij pozycję **Utwórz punkt końcowy logowania** jednokrotnego.

    ![Zdalne uwierzytelnianie](./media/screensteps-tutorial/ic778525.png "Zdalne uwierzytelnianie")

1. W sekcji **Tworzenie punktu końcowego logowania** jednokrotnego wykonaj następujące czynności:

    ![Tworzenie punktu końcowego uwierzytelniania](./media/screensteps-tutorial/ic778526.png "Tworzenie punktu końcowego uwierzytelniania")

    a. W polu tekstowym **tytuł** wpisz tytuł.

    b. Z listy **tryb** wybierz pozycję **SAML** .

    c. Kliknij pozycję **Utwórz** .

1. **Edytuj** nowy punkt końcowy.

    ![Edytuj punkt końcowy](./media/screensteps-tutorial/ic778528.png "Edytuj punkt końcowy")

1. W sekcji **Edytuj punkt końcowy logowania** jednokrotnego wykonaj następujące czynności:

    ![Punkt końcowy uwierzytelniania zdalnego](./media/screensteps-tutorial/ic778527.png "Punkt końcowy uwierzytelniania zdalnego")

    a. Kliknij przycisk **Przekaż nowy plik certyfikatu SAML** , a następnie Przekaż certyfikat pobrany z Azure Portal.

    b. Wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal do pola tekstowego **zdalne logowanie adresu URL** .

    c. Wklej wartość **adresu URL wylogowywania** skopiowaną z Azure Portal do pola tekstowego **adres URL wylogowywania** .

    d. Wybierz **grupę** , do której mają zostać przypisani użytkownicy po zainicjowaniu obsługi administracyjnej.

    e. Kliknij przycisk **Update** (Aktualizuj).

    f. Skopiuj **adres URL odbiorcy SAML** do schowka i wklej go do pola tekstowego **adres URL logowania** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    przykład Wróć do **punktu końcowego Edytuj logowanie** jednokrotne.

    h. Kliknij przycisk **Ustaw jako domyślny dla konta** , aby użyć tego punktu końcowego dla wszystkich użytkowników logujących się do usługi ScreenSteps. Alternatywnie możesz kliknąć przycisk **Dodaj do witryny** , aby użyć tego punktu końcowego dla określonych lokacji w **ScreenSteps** .

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory** , wybierz opcję **Użytkownicy** , a następnie wybierz pozycję **Wszyscy użytkownicy** .

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon** .
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz** .

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi ScreenSteps.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw** , wybierz pozycję **wszystkie aplikacje** , a następnie wybierz pozycję **ScreenSteps** .

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **ScreenSteps** .

    ![Link ScreenSteps na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy** .

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika** , a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania** .

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-screensteps-test-user"></a>Utwórz użytkownika testowego ScreenSteps

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w ScreenSteps. Współpracuj z [zespołem obsługi klienta ScreenSteps](https://www.screensteps.com/contact) , aby dodać użytkowników z platformy ScreenSteps. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka ScreenSteps w panelu dostępu należy automatycznie zalogować się do ScreenSteps, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)