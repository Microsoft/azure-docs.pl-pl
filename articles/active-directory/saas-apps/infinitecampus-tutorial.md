---
title: 'Samouczek: integracja z nieograniczoną Azure Active Directoryą | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i nieograniczone.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 08cee87d94b20e99bcc46d42cbac7eb4272561bb
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460241"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Samouczek: integracja Azure Active Directory z nieograniczonej szkoły

W ramach tego samouczka nauczysz się, jak zintegrować nieograniczone kampus z usługą Azure Active Directory (Azure AD).
Integracja z nieograniczonej szkoły z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do nieograniczonej szkoły.
* Możesz pozwolić użytkownikom na automatyczne logowanie się do nieskończonego (logowania jednokrotnego) przy użyciu swoich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z nieograniczonym zasięgiem, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Nieskończona subskrypcja z włączonym logowaniem jednokrotnym
* Aby dokończyć konfigurację, musisz być administratorem Azure Active Directory i mieć rolę zabezpieczeń produktu kampusowego "system informacji studenta" (SIS).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Nieskończona obsługa logowania jednokrotnego przez usługę **SP**

## <a name="adding-infinite-campus-from-the-gallery"></a>Dodawanie nieskończonej szkoły z galerii

Aby skonfigurować integrację z nieograniczonej częścią usługi Azure AD, musisz dodać nieskończoność z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać nieskończoność z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **nieskończoność**, wybierz pozycję **nieskończoność** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Nieskończoność na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji konfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD z nieograniczonej szkoły na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w nieskończony zasięg.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD z nieograniczonej szkoły, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj nieograniczone logowanie](#configure-infinite-campus-single-sign-on)** jednokrotne, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz nieskończonego użytkownika testowego](#create-infinite-campus-test-user)** , aby uzyskać odpowiednik Britta Simon w nieskończonej części, która jest połączona z reprezentacją usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne w usłudze Azure AD z nieograniczonej szkoły, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na **nieograniczoną** integrację aplikacji kampusowych wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji Podstawowa konfiguracja języka SAML wykonaj następujące czynności (należy zauważyć, że domena będzie się różnić w zależności od modelu hostingu, ale w **pełni kwalifikowana wartość domeny** musi być zgodna z nieograniczoną instalacją kampusową):

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Nieograniczone informacje o rejestracji jednokrotnej w domenie kampusowej i adresach URL](common/sp-identifier-reply.png)

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Konfigurowanie nieskończonej pojedynczej Sign-Onej

1. W innym oknie przeglądarki sieci Web Zaloguj się do nieograniczonej szkoły jako administrator zabezpieczeń.

2. Po lewej stronie menu kliknij pozycję **Administracja systemu**.

    ![Administrator](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Przejdź do **User Security**  >  **SAML Management**  >  **Konfiguracja dostawcy usługi SSO zarządzania usługą**SAML zabezpieczeń użytkownika.

    ![Element SAML](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Na stronie **Konfiguracja dostawcy usługi SSO** wykonaj następujące czynności:

    ![Logowanie jednokrotne](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Wybierz opcję **Włącz logowanie jednokrotne SAML**.

    b. Edytuj **opcjonalną nazwę atrybutu** , aby zawierała **nazwę**

    c. Na stronie **Wybierz opcję pobierania danych serwera dostawcy tożsamości (dostawcy tożsamości)** wybierz pozycję **adres URL metadanych**, wklej wartość **adresu URL metadanych federacji aplikacji** , która została skopiowana z Azure Portal w polu, a następnie kliknij pozycję **Synchronizuj**.

    d. Po kliknięciu przycisku **Synchronizuj** wartości Pobierz automatycznie wypełnione na stronie **Konfiguracja dostawcy usługi SSO** . Te wartości można zweryfikować, aby odpowiadały wartościom widocznym w kroku 4 powyżej.

    e. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension` . Na przykład BrittaSimon@contoso.com.

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

> [!NOTE]
> Jeśli chcesz, aby wszyscy użytkownicy platformy Azure mieli dostęp z logowaniem jednokrotnym do nieskończoności i polegają na nieograniczonym systemie uprawnień wewnętrznych do kontroli dostępu, możesz ustawić właściwość **wymagane przypisanie użytkownika** na wartość nie i pominąć następujące czynności.

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do nieskończonej szkoły.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **nieograniczone kampusy**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **nieograniczone kampus**.

    ![Nieograniczone połączenie Kampusowe na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-infinite-campus-test-user"></a>Utwórz nieskończonego użytkownika testowego

Nieskończone szkoły mają architekturę z centrami danych demograficznych. Skontaktuj się z [nieograniczonym zespołem pomocy technicznej](mailto:sales@infinitecampus.com) , aby dodać użytkowników w nieskończonej platformie kampusowej.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka nieskończoność w panelu dostępu należy automatycznie zalogować się do nieskończonej szkoły, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)