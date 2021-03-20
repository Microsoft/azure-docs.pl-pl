---
title: 'Samouczek: integracja Azure Active Directory z usługą T&E Express | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i T&E Express.
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
ms.openlocfilehash: f44a67b4943c6897f82aedf47d493b36e5e2d3b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92521895"
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Samouczek: integracja Azure Active Directory z usługą T&E Express

W tym samouczku dowiesz się, jak zintegrować usługę T&E Express z usługą Azure Active Directory (Azure AD).
Integracja usługi T&E Express z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi T&E Express.
* Możesz pozwolić użytkownikom na automatyczne logowanie do usługi T&E Express (Logowanie jednokrotne) przy użyciu kont w usłudze Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD za pomocą usługi T&E Express, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi T&E Express z włączoną obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa T&E Express obsługuje **dostawcy tożsamości** zainicjowane Logowanie jednokrotne

## <a name="adding-te-express-from-the-gallery"></a>Dodawanie programu T&E Express z galerii

Aby skonfigurować integrację programu T&E Express z usługą Azure AD, musisz dodać T&E Express z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać T&E Express z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **t&E Express**, wybierz pozycję **t&e Express** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![T&E Express na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD za pomocą usługi T&E Express na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze T&E Express.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi T&E Express, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. Skonfiguruj Logowanie jednokrotne w usłudze **[T&E Express](#configure-te-express-single-sign-on)** — aby skonfigurować ustawienia pojedynczego Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego T&E Express](#create-te-express-test-user)** , aby uzyskać odpowiednik Britta Simon w T&E Express, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą usługi T&E Express, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **T&E Express** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![T&E Express — informacje o rejestracji jednokrotnej w domenie i adresach URL](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz wartość jako adres URL, używając następującego wzorca: `https://<domain>.tyeexpress.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. W tym miejscu zalecamy użycie unikatowej wartości ciągu w identyfikatorze. Skontaktuj się z [zespołem obsługi klienta T&E Express](https://www.tyeexpress.com/contacto.aspx) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfiguracja T&E Express** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-te-express-single-sign-on"></a>Skonfiguruj pojedynczy Sign-On w usłudze T&E Express

1. Aby skonfigurować Logowanie jednokrotne na stronie **t&E Express** , zaloguj się do aplikacji t&e Express bez logowania jednokrotnego SAML przy użyciu poświadczeń administratora.

1. Na karcie **administrator** kliknij pozycję **domena SAML** , aby otworzyć stronę Ustawienia protokołu SAML.

    ![Zrzut ekranu przedstawia domenę protokołu SAML wybraną z menu administratora.](./media/tyeexpress-tutorial/tye-SAML.png)

1. Wybierz opcję **Activar (Activate)** od **nie** do **si (tak)**. W polu tekstowym **metadane dostawcy tożsamości** wklej plik XML metadanych, który został pobrany z Azure Portal.

    ![Zrzut ekranu przedstawia stronę SAML dominio, na której można wprowadzić metadane.](./media/tyeexpress-tutorial/tyeAdmin.png)

1. Kliknij przycisk **Guardar (Zapisz)** , aby zapisać ustawienia.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure, udzielając dostępu do usługi T&E Express.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **T&E Express**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **T&E Express**.

    ![Link T&E Express na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-te-express-test-user"></a>Utwórz użytkownika testowego T&E Express

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze T&E Express, muszą one być obsługiwane w usłudze T&E Express. W przypadku T&E Express, Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konta użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy T&E Express jako administrator.

1. W obszarze Tag administratora kliknij pozycję Użytkownicy, aby otworzyć stronę wzorcową użytkownicy.

    ![Zrzut ekranu przedstawia użytkowników wybranych z menu Administrator.](./media/tyeexpress-tutorial/tye-adminusers.png)

1. Na stronie głównej kliknij pozycję Włącz, **+** Aby dodać użytkowników.

    ![Zrzut ekranu przedstawia ikonę znaku plus, aby dodać użytkowników.](./media/tyeexpress-tutorial/tye-usershome.png)

1. Wprowadź wszystkie wymagane szczegóły zgodnie z prośbą w formularzu i kliknij przycisk Zapisz, aby zapisać szczegóły.

    ![Zrzut ekranu przedstawia sekcję Informacje o użytkowniku, w której można wprowadzić odpowiednie wartości.](./media/tyeexpress-tutorial/tye-usersadd.png)

    ![Zrzut ekranu przedstawia sekcje osoby zatwierdzające i asystenta, w których można wprowadzać odpowiednie wartości.](./media/tyeexpress-tutorial/tye-userssave.png)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka T&E Express w panelu dostępu należy automatycznie zalogować się do programu T&E Express, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)