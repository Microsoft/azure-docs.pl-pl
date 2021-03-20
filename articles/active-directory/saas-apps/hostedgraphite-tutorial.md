---
title: 'Samouczek: integracja z Azure Active Directory przy użyciu hostowanego grafitu | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Hosted Graphite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 786c6309f685af6b8e42108c490ce86c7844bc81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92443225"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Samouczek: integracja Azure Active Directory z hostowaniem grafitu

Z tego samouczka dowiesz się, jak zintegrować aplikację Hosted Graphite z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Hosted Graphite z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Hosted Graphite.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Hosted Graphite (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Hosted Graphite, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Hosted Graphite z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Hosted Graphite obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług oraz dostawcę tożsamości**
* Aplikacja Hosted Graphite obsługuje aprowizację użytkowników typu **Just In Time**

## <a name="adding-hosted-graphite-from-the-gallery"></a>Dodawanie aplikacji Hosted Graphite z galerii

Aby skonfigurować integrację aplikacji Hosted Graphite w usłudze Azure AD, należy dodać aplikację Hosted Graphite z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Hosted Graphite z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Hosted Graphite**, w panelu wyników wybierz pozycję **Hosted Graphite**, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Hosted Graphite na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz funkcję logowania jednokrotnego usługi Azure AD z aplikacją Hosted Graphite, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w aplikacji Hosted Graphite.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w aplikacji Hosted Graphite, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Hosted Graphite](#configure-hosted-graphite-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Hosted Graphite](#create-hosted-graphite-test-user)** — aby mieć w aplikacji Hosted Graphite odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z aplikacją Hosted Graphite, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Hosted Graphite** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML, w której można wprowadzić identyfikator, odpowiedź U R L i wybrać pozycję Zapisz.](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://www.hostedgraphite.com/metadata/<user id>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://www.hostedgraphite.com/complete/saml/<user id>`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![Zrzut ekranu przedstawia ustawienie dodatkowego U R LS, gdzie można wprowadzić znak U R L.](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.hostedgraphite.com/login/saml/<user id>/`

    > [!NOTE]
    > Należy pamiętać, że nie są to rzeczywiste wartości. Te wartości należy zaktualizować za pomocą rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Aby uzyskać te wartości, możesz wybrać pozycję Access -> SAML Setup (Dostęp -> Ustawienia SAML) po stronie aplikacji lub skontaktować się z [zespołem pomocy technicznej aplikacji Hosted Graphite](mailto:help@hostedgraphite.com).

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji **Konfigurowanie aplikacji Hosted Graphite** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-hosted-graphite-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji Hosted Graphite

1. Zaloguj się do dzierżawy aplikacji Hosted Graphite jako administrator.

2. Przejdź na **stronę konfiguracji SAML** na pasku bocznym (**Access -> SAML Setup** [Dostęp -> Ustawienia SAML]).

    ![Zrzut ekranu przedstawia menu dostęp z wybranym ustawieniem SAML.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

3. Upewnij się, że te identyfikatory URI są zgodne z konfiguracją wykonaną w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    ![Zrzut ekranu przedstawia podstawową konfigurację protokołu SAML.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

4. W polach tekstowych **Entity or Issuer ID** (Identyfikator użytkownika lub jednostki) i **SSO Login URL** (Adres URL logowania dla logowania jednokrotnego) wklej wartości **identyfikatora usługi Azure AD** i **adresu URL logowania** skopiowane z witryny Azure Portal.

    ![Zrzut ekranu przedstawia wpisy dla dostawcy tożsamości.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

5. Wybierz opcję **Read-only** (Tylko do odczytu) dla pozycji **Default User Role** (Domyślna rola użytkownika).

    ![Zrzut ekranu przedstawia domyślną rolę użytkownika, która jest tylko do odczytu.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

6. Otwórz w Notatniku swój certyfikat zakodowany w formacie base-64 pobrany z witryny Azure Portal, skopiuj zawartość do schowka, a następnie wklej ją w polu tekstowym **Certyfikat X.509**.

    ![Zrzut ekranu przedstawia certyfikat X dot 509.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

7. Kliknij przycisk **Save** (Zapisz).

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Hosted Graphite.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **Hosted Graphite**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Hosted Graphite**.

    ![Link Hosted Graphite na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-hosted-graphite-test-user"></a>Tworzenie użytkownika testowego aplikacji Hosted Graphite

W tej sekcji w aplikacji Hosted Graphite jest tworzony użytkownik o nazwie Britta Simon. Aplikacja Hosted Graphite obsługuje aprowizację użytkowników just-in-time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Hosted Graphite, zostanie utworzony po uwierzytelnieniu.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z zespołem pomocy technicznej klienta aplikacji Hosted Graphite pod adresem <mailto:help@hostedgraphite.com>.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Hosted Graphite na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Hosted Graphite, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)