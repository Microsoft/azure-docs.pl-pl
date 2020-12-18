---
title: 'Samouczek: Azure Active Directory integrację z logowaniem jednokrotnym SAML dla BitBucket za pomocą rozwiązania GmbH | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: 5e0b1d8d2cb80886131a88c01a6c24102ad7c77c
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673563"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Samouczek: integracja Azure Active Directory z logowaniem jednokrotnym SAML dla BitBucket przez rozwiązanie GmbH

Z tego samouczka dowiesz się, jak zintegrować usługę logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH z usługą Azure Active Directory (Azure AD).
Zintegrowanie usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do usługi logowania jednokrotnego SAML dla usługi Bitbucket firmy resolution GmbH (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji z usługą Azure AD w usłudze logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH z włączonym logowaniem jednokrotnym

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa logowania jednokrotnego dla aplikacji Bitbucket firmy resolution GmbH obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług i dostawcę tożsamości**
* Usługa logowania jednokrotnego dla aplikacji Bitbucket firmy resolution GmbH obsługuje aprowizowanie użytkowników typu **Just In Time**


## <a name="adding-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Dodawanie usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH z galerii

Aby skonfigurować integrację usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH z usługą Azure AD, musisz dodać usługę logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać usługę logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Usługa logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH**, wybierz pozycję **Usługa logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

    ![Usługa logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w usłudze logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD a powiązanym użytkownikiem usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w usłudze logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH, musisz utworzyć następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w usłudze logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** — aby mieć w usłudze logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w usłudze logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML, w której można wprowadzić identyfikator, odpowiedź U R L i wybrać pozycję Zapisz.](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![Zrzut ekranu przedstawia ustawienie dodatkowego U R LS, gdzie można wprowadzić znak U R L.](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej dla klientów usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w usłudze logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH

1. Zaloguj się do firmowej witryny usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH jako administrator.

2. Z prawej strony głównego paska narzędzi kliknij pozycję **Ustawienia**.

3. Przejdź do sekcji KONTA i kliknij pozycję **SAML SingleSignOn**.

    ![Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. Na stronie **konfiguracji wtyczki SIngleSignOn języka SAML** kliknij pozycję **Dodaj dostawcę tożsamości**. 

    ![Dodawanie dostawcy tożsamości](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. Na stronie **wybierania dostawcy tożsamości języka SAML** wykonaj następujące kroki:

    ![Dostawca tożsamości](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. W obszarze **Typ dostawcy tożsamości** ustaw wartość **AZURE AD**.

    b. W polu tekstowym **Nazwa** wpisz nazwę.

    c. W polu tekstowym **Opis** wpisz opis.

    d. Kliknij przycisk **Dalej**.

6. Na **stronie konfiguracji dostawcy tożsamości** kliknij przycisk **Dalej**.

    ![Konfiguracja tożsamości](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  Na stronie **importowania metadanych dostawcy tożsamości języka SAML** kliknij pozycję **Załaduj plik**, aby przekazać plik **METADATA XML** pobrany z witryny Azure Portal.

    ![Idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)

8. Kliknij przycisk **Dalej**.

9. Kliknij pozycję **Zapisz ustawienia**.

    ![Zapisywanie](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)

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

W tej sekcji udostępnisz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw** i **Wszystkie aplikacje**, a następnie **Usługa logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz pozycję **Usługa logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH**.

    ![Usługa logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Tworzenie użytkownika testowego usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w obrębie funkcji logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH. Logowanie jednokrotne SAML dla aplikacji Bitbucket firmy resolution GmbH obsługuje aprowizację typu just in time, a użytkowników można tworzyć ręcznie. Skontaktuj się z [zespołem pomocy technicznej dotyczącej logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) w sprawie swoich wymagań.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH w panelu dostępu powinno nastąpić automatyczne zalogowanie się do usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)