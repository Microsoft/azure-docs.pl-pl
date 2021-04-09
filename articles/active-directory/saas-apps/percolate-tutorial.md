---
title: 'Samouczek: integracja Azure Active Directory z usługą Percolate | Microsoft Docs'
description: W tym samouczku dowiesz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Percolate.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 913ffc0670e40e749bd28382d492a16891fdc5c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522274"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Samouczek: integracja Azure Active Directory z usługą Percolate

W tym samouczku dowiesz się, jak zintegrować usługę Percolate z usługą Azure Active Directory (Azure AD).

Ta integracja zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do usługi Percolate.
* Możesz umożliwić użytkownikom automatyczne logowanie się do usługi Percolate (Logowanie jednokrotne) przy użyciu kont w usłudze Azure AD.
* Kontami można zarządzać w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](../manage-apps/what-is-single-sign-on.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Percolate, należy dysponować:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi Percolate z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD w środowisku testowym.

* Usługa Percolate obsługuje logowanie jednokrotne z użyciem zainicjowanych przez usługę SP i dostawcy tożsamości.

## <a name="add-percolate-from-the-gallery"></a>Dodaj Percolate z galerii

Aby skonfigurować integrację programu Percolate z usługą Azure AD, musisz dodać Percolate z galerii do listy zarządzanych aplikacji SaaS.

1. W [Azure Portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory**:

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**:

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Percolate**. W wynikach wyszukiwania wybierz pozycję **Percolate** , a następnie wybierz pozycję **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD za pomocą Percolate przy użyciu użytkownika testowego o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD i odpowiednim użytkownikiem w Percolate.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą Percolate, należy wykonać następujące czynności:

1. **[Skonfiguruj logowanie](#configure-azure-ad-single-sign-on)** jednokrotne w usłudze Azure AD, aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-percolate-single-sign-on)** w usłudze Percolate po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** , aby włączyć logowanie jednokrotne usługi Azure AD dla użytkownika.
5. **[Utwórz użytkownika testowego Percolate](#create-a-percolate-test-user)** , który jest połączony z reprezentacją usługi Azure AD użytkownika.
6. **[Przetestuj logowanie](#test-single-sign-on)** jednokrotne, aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne w usłudze Azure AD w Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Percolate, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Percolate** wybierz pozycję **Logowanie jednokrotne**:

    ![Wybierz Logowanie jednokrotne](common/select-sso.png)

2. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML/WS-karmione** , aby włączyć logowanie jednokrotne:

    ![Wybierz metodę logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę **Edytuj** , aby otworzyć okno dialogowe **podstawowe ustawienia SAML** :

    ![Ikona Edytuj](common/edit-urls.png)

4. W oknie dialogowym **Podstawowa konfiguracja SAML** nie trzeba podejmować żadnych działań w celu skonfigurowania aplikacji w trybie inicjowanym przez dostawcy tożsamości. Aplikacja jest już zintegrowana z platformą Azure.

    ![Percolate domenę i adresy URL Logowanie jednokrotne](common/preintegrated.png)

5. Jeśli chcesz skonfigurować aplikację w trybie inicjowania programu SP, wybierz opcję **Ustaw dodatkowe adresy URL** i w polu **adres URL logowania** wpisz **https://percolate.com/app/login** :

   ![Zrzut ekranu pokazujący, że wybrano przycisk "Ustaw dodatkowy U R ls" z wyróżnionym polem tekstowym "Zaloguj się U R L".](common/metadata-upload-additional-signon.png)
6. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz ikonę **kopiowania** , aby skopiować **adres URL metadanych federacji aplikacji**. Zapisz ten adres URL.

    ![Skopiuj adres URL metadanych federacji aplikacji](common/copy-metadataurl.png)

7. W sekcji **Konfiguracja Percolate** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Identyfikator usługi Azure AD**.

    1. **Adres URL wylogowywania**.

### <a name="configure-percolate-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Percolate

1. W nowym oknie przeglądarki sieci Web Zaloguj się do Percolate jako administrator.

2. Po lewej stronie strony głównej wybierz pozycję **Ustawienia**:
    
    ![Wybranie pozycji Ustawienia](./media/percolate-tutorial/configure01.png)

3. W lewym okienku wybierz pozycję **Logowanie jednokrotne** w obszarze **organizacja**:

    ![Wybierz pozycję Logowanie jednokrotne w obszarze organizacja](./media/percolate-tutorial/configure02.png)

    1. W polu **adres URL logowania** wklej wartość **adresu URL logowania** skopiowaną z Azure Portal.

    1. W polu **Identyfikator jednostki** wklej wartość identyfikatora usługi **Azure AD** skopiowaną z Azure Portal.

    1. W Notatniku otwórz certyfikat z kodowaniem Base-64 pobrany z Azure Portal. Skopiuj jej zawartość i wklej ją w polu **Certyfikaty x509** .

    1. W polu **atrybut wiadomości e-mail** wprowadź **EmailAddress**.

    1. Pole **adresu URL metadanych dostawcy tożsamości** jest polem opcjonalnym. W przypadku skopiowania **adresu URL metadanych federacji aplikacji** z Azure Portal można wkleić go w tym polu.

    1. Na liście **powinien AuthNRequests być podpisany?** wybierz pozycję **nie**.

    1. Na liście **Włącz logowanie jednokrotne** , wybierz pozycję **nie**.

    1. Wybierz pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w Azure Portal.

1. W Azure Portal wybierz pozycję **Azure Active Directory** w lewym okienku, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**:

    ![Wybierz pozycję Wszyscy użytkownicy](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu:

    ![Wybierz nowego użytkownika](common/new-user.png)

3. W oknie dialogowym **użytkownik** wykonaj następujące czynności.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **BrittaSimon@ \<yourcompanydomain> . \<extension>**. (Na przykład BrittaSimon@contoso.com .)

    1. Wybierz pozycję **Pokaż hasło**, a następnie Zapisz wartość znajdującą się w polu **hasło** .

    1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego w usłudze Azure AD, przyznając swój dostęp do usługi Percolate.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Percolate**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Percolate**.

    ![Lista aplikacji](common/all-applications.png)

3. W lewym okienku wybierz pozycję **Użytkownicy i grupy**:

    ![Zrzut ekranu pokazujący "Użytkownicy i grupy" wybrane z okienka po lewej stronie.](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Użytkownicy i grupy](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

6. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-percolate-test-user"></a>Tworzenie użytkownika testowego Percolate

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Percolate, należy dodać je do Percolate. Należy dodać je ręcznie.

Aby utworzyć konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do Percolate jako administrator.

2. W lewym okienku wybierz pozycję **Użytkownicy** w obszarze **organizacja**. Wybierz **nowych użytkowników**:

    ![Wybierz nowych użytkowników](./media/percolate-tutorial/configure03.png)

3. Na stronie **Tworzenie użytkowników** wykonaj następujące czynności.

    ![Strona tworzenia użytkowników](./media/percolate-tutorial/configure04.png)

    1. W polu **adres e-mail** wprowadź adres e-mail użytkownika. Na przykład brittasimon@contoso.com.

    1. W polu **pełna nazwa** wprowadź nazwę użytkownika. Na przykład **Brittasimon**.

    1. Wybierz pozycję **Utwórz użytkowników**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz musisz przetestować konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka Percolate w panelu dostępu należy automatycznie zalogować się do wystąpienia Percolate, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji, zobacz [dostęp i używanie aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)