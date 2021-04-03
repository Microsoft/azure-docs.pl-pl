---
title: 'Samouczek: integracja Azure Active Directory z administratorem dostępu prywatnego rozwiązania Zscaler | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i administratora dostępu prywatnego rozwiązania Zscaler.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 803f5c25429b33e6d1af130196d38e125b410eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891027"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Samouczek: integracja Azure Active Directory z administratorem dostępu prywatnego rozwiązania Zscaler

W ramach tego samouczka nauczysz się zintegrować rozwiązania Zscaler prywatnego dostępu administratora z usługą Azure Active Directory (Azure AD).
Integracja administratora prywatnego dostępu rozwiązania Zscaler z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do rozwiązania Zscaler prywatnego dostępu administratora.
* Możesz pozwolić użytkownikom na automatyczne logowanie do rozwiązania Zscaler prywatnego dostępu administratora (Logowanie jednokrotne) za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD za pomocą administratora prywatnego dostępu rozwiązania Zscaler, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym dla administratora dostępu rozwiązania Zscaler

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Administrator dostępu prywatnego rozwiązania Zscaler obsługuje logowanie jednokrotne z użyciem **SP** i **dostawcy tożsamości**

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Dodawanie administratora prywatnego dostępu rozwiązania Zscaler z galerii

Aby skonfigurować integrację administratora dostępu prywatnego rozwiązania Zscaler z usługą Azure AD, musisz dodać administratora prywatnego dostępu rozwiązania Zscaler z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać administratora prywatnego dostępu rozwiązania Zscaler z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **rozwiązania Zscaler administrator dostępu prywatnego**, wybierz pozycję **rozwiązania Zscaler administrator dostępu prywatnego** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Administrator dostępu prywatnego rozwiązania Zscaler na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji należy skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą administratora prywatnego dostępu rozwiązania Zscaler na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ramach rozwiązania Zscaler prywatnego dostępu administratora.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu administratora rozwiązania Zscaler dostępu prywatnego, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. Skonfiguruj Logowanie jednokrotne dla **[administratora dostępu prywatnego rozwiązania Zscaler](#configure-zscaler-private-access-administrator-single-sign-on)** — aby skonfigurować ustawienia pojedynczej Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz administratora prywatnego dostępu rozwiązania Zscaler użytkownika testowego](#create-zscaler-private-access-administrator-test-user)** — Aby uzyskać odpowiednik Britta Simon w rozwiązania Zscaler prywatnym dostępu, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD przy użyciu administratora rozwiązania Zscaler dostępu prywatnego, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **administratora dostępu prywatnego rozwiązania Zscaler** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML, w której można wprowadzić podane wartości.](common/idp-relay.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    d. W polu tekstowym **Stan przekaźnika** wpisz adres URL: `idpadminsso`

5.  Jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** , wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia ustawienie dodatkowego U R LS, gdzie można wprowadzić znak U R L.](common/both-signonurl.png)

    W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej rozwiązania Zscaler klienta administratora dostępu prywatnego](https://help.zscaler.com/zpa-submit-ticket) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfigurowanie administratora prywatnego dostępu rozwiązania Zscaler** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Konfigurowanie jednego Sign-On administratora dostępu prywatnego rozwiązania Zscaler

1. W innym oknie przeglądarki sieci Web Zaloguj się, aby rozwiązania Zscaler prywatny administrator dostępu jako administrator.

2. W górnej części kliknij pozycję **Administracja** i przejdź do sekcji **uwierzytelnianie** , a następnie kliknij pozycję **Konfiguracja dostawcy tożsamości**.

    ![Administrator administratora dostępu prywatnego rozwiązania Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. W prawym górnym rogu kliknij pozycję **Dodaj konfigurację dostawcy tożsamości**. 

    ![Administrator dostępu prywatnego rozwiązania Zscaler addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. Na stronie **Dodawanie konfiguracji dostawcy tożsamości** wykonaj następujące czynności:
 
    ![Administrator dostępu prywatnego rozwiązania Zscaler idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Kliknij pozycję **Wybierz plik** , aby przekazać pobrany plik metadanych z usługi Azure AD w polu **przekazywania pliku metadanych dostawcy tożsamości** .

    b. Odczytuje **metadane dostawcy tożsamości** z usługi Azure AD i wypełnia wszystkie informacje o polach, jak pokazano poniżej.

    ![Administrator dostępu prywatnego rozwiązania Zscaler idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Wybierz pozycję **Logowanie jednokrotne** jako **administrator**.

    d. Wybierz **domenę z pola domeny.**
    
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
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension` . Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do administratora prywatnego dostępu rozwiązania Zscaler.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz opcję **administrator dostępu prywatnego rozwiązania Zscaler**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **rozwiązania Zscaler prywatny dostęp administratora**.

    ![Link administratora dostępu prywatnego rozwiązania Zscaler na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Utwórz użytkownika testowego administratora rozwiązania Zscaler dostępu prywatnego

Aby umożliwić użytkownikom usługi Azure AD logowanie się w celu rozwiązania Zscaler prywatnego dostępu administratora, muszą one być przydzielone do administratora prywatnego dostępu rozwiązania Zscaler. W przypadku administratora prywatnego dostępu rozwiązania Zscaler Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do swojej witryny administratora dostępu prywatnego rozwiązania Zscaler jako administrator.

2. W górnej części kliknij pozycję **Administracja** i przejdź do sekcji **uwierzytelnianie** , a następnie kliknij pozycję **Konfiguracja dostawcy tożsamości**.

    ![Administrator administratora dostępu prywatnego rozwiązania Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Po lewej stronie menu kliknij pozycję **administratorzy** .

    ![Administrator administratora dostępu prywatnego rozwiązania Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. W prawym górnym rogu kliknij pozycję **Dodaj administratora**:

    ![Administrator dostępu prywatnego rozwiązania Zscaler Dodaj administratora](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Na stronie **Dodawanie administratora** wykonaj następujące czynności:

    ![Administrator administratora dostępu prywatnego rozwiązania Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. W polu tekstowym **Nazwa użytkownika** wprowadź adres e-mail użytkownika, np BrittaSimon@contoso.com .

    b. W polu tekstowym **hasło** wpisz hasło.

    c. W polu tekstowym **Potwierdź hasło** wpisz hasło.

    d. Wybierz **rolę** jako **rozwiązania Zscaler administrator dostępu prywatnego**.

    e. W polu tekstowym **E-mail** wprowadź adres e-mail użytkownika, na przykład BrittaSimon@contoso.com.

    f. W polu tekstowym **telefon** wpisz numer telefonu.

    przykład W polu tekstowym **strefa czasowa** wybierz strefę czasową.

    h. Kliknij pozycję **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka administrator dostępu prywatnego rozwiązania Zscaler w panelu dostępu należy automatycznie zalogować się do administratora prywatnego dostępu usługi rozwiązania Zscaler, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)