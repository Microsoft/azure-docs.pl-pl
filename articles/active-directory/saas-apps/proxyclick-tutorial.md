---
title: 'Samouczek: integracja Azure Active Directory z usługą Proxyclick | Microsoft Docs'
description: W tym samouczku dowiesz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Proxyclick.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 23ae1a2c1371cda9435ea76f02cebc79c141c904
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522244"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Samouczek: integracja Azure Active Directory z usługą Proxyclick

W tym samouczku dowiesz się, jak zintegrować usługę Proxyclick z usługą Azure Active Directory (Azure AD).
Ta integracja zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do usługi Proxyclick.
* Możesz umożliwić użytkownikom automatyczne logowanie się do usługi Proxyclick (Logowanie jednokrotne) przy użyciu kont w usłudze Azure AD.
* Kontami można zarządzać w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](../manage-apps/what-is-single-sign-on.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Proxyclick, należy dysponować:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz zarejestrować się w celu uzyskania [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja usługi Proxyclick z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD w środowisku testowym.

* Usługa Proxyclick obsługuje logowanie jednokrotne z użyciem zainicjowanych przez usługę SP i dostawcy tożsamości.

## <a name="add-proxyclick-from-the-gallery"></a>Dodaj Proxyclick z galerii

Aby skonfigurować integrację programu Proxyclick z usługą Azure AD, musisz dodać Proxyclick z galerii do listy zarządzanych aplikacji SaaS.

1. W [Azure Portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory**:

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**:

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Proxyclick**. W wynikach wyszukiwania wybierz pozycję **Proxyclick** , a następnie wybierz pozycję **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD za pomocą Proxyclick przy użyciu użytkownika testowego o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD i odpowiednim użytkownikiem w Proxyclick.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą Proxyclick, należy wykonać następujące czynności:

1. **[Skonfiguruj logowanie](#configure-azure-ad-single-sign-on)** jednokrotne w usłudze Azure AD, aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-proxyclick-single-sign-on)** w usłudze Proxyclick po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** , aby włączyć logowanie jednokrotne usługi Azure AD dla użytkownika.
5. **[Utwórz użytkownika testowego Proxyclick](#create-a-proxyclick-test-user)** , który jest połączony z reprezentacją usługi Azure AD użytkownika.
6. **[Przetestuj logowanie](#test-single-sign-on)** jednokrotne, aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne w usłudze Azure AD w Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Proxyclick, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji Proxyclick wybierz pozycję **Logowanie jednokrotne**:

    ![Wybierz Logowanie jednokrotne](common/select-sso.png)

2. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML/WS-karmione** , aby włączyć logowanie jednokrotne:

    ![Wybierz metodę logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę **Edytuj** , aby otworzyć okno dialogowe **podstawowe ustawienia SAML** :

    ![Ikona Edytuj](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie zainicjowanym przy użyciu dostawcy tożsamości, w oknie dialogowym **Podstawowa konfiguracja SAML** wykonaj następujące czynności.

    ![Podstawowa konfiguracja SAML — okno dialogowe](common/idp-intiated.png)

    1. W polu **Identyfikator** wprowadź adres URL w tym wzorcu:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. W polu **adres URL odpowiedzi** wprowadź adres URL w tym wzorcu:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Jeśli chcesz skonfigurować aplikację w trybie zainicjowanym przez program SP, wybierz opcję **Ustaw dodatkowe adresy URL**. W polu **adres URL logowania** wprowadź adres URL w tym wzorcu:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Proxyclick domenę i adresy URL Logowanie jednokrotne](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Te wartości są symbolami zastępczymi. Musisz użyć rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Kroki związane z uzyskaniem tych wartości są opisane w dalszej części tego samouczka.

6. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz link **pobierania** obok pozycji **certyfikat (base64)**, zgodnie z wymaganiami, a następnie Zapisz certyfikat na komputerze:

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji **Konfiguracja Proxyclick** skopiuj odpowiednie adresy URL zgodnie z wymaganiami:

    ![Kopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Identyfikator usługi Azure AD**.

    1. **Adres URL wylogowywania**.

### <a name="configure-proxyclick-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Proxyclick

1. W nowym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny Proxyclick jako administrator.

2. Wybierz **ustawienia & konta**:

    ![Wybierz ustawienia & konta](./media/proxyclick-tutorial/configure1.png)

3. Przewiń w dół do sekcji **integracje** i wybierz pozycję **SAML**:

    ![Wybierz pozycję SAML](./media/proxyclick-tutorial/configure2.png)

4. W sekcji **SAML** wykonaj następujące czynności.

    ![Sekcja SAML](./media/proxyclick-tutorial/configure3.png)

    1. Skopiuj wartość **adres URL odbiorcy SAML** i wklej ją w polu **adres URL odpowiedzi** w oknie dialogowym **podstawowe konfiguracje SAML** w Azure Portal.

    1. Skopiuj wartość **adresu URL przekierowania SSO protokołu SAML** i wklej ją do pól **logowania** i **identyfikatora** w oknie dialogowym **podstawowe konfiguracje SAML** w Azure Portal.

    1. Na liście **Metoda żądania SAML** wybierz opcję **przekierowywanie http**.

    1. W polu **wystawca** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    1. W polu **adres URL punktu końcowego protokołu SAML 2,0** wklej wartość **adresu URL logowania** skopiowaną z Azure Portal.

    1. W Notatniku otwórz plik certyfikatu pobrany z Azure Portal. Wklej zawartość tego pliku do pola **certyfikat** .

    1. Wybierz pozycję **Zapisz zmiany**.

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

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego na platformie Azure, przyznając swój dostęp do usługi Proxyclick.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Proxyclick**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Proxyclick**.

    ![Lista aplikacji](common/all-applications.png)

3. W lewym okienku wybierz pozycję **Użytkownicy i grupy**:

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części okna.

6. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Kliknij przycisk **Wybierz** w dolnej części okna.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-proxyclick-test-user"></a>Tworzenie użytkownika testowego Proxyclick

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Proxyclick, należy dodać je do Proxyclick. Należy dodać je ręcznie.

Aby utworzyć konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do swojej witryny firmowej Proxyclick jako administrator.

1. Wybierz pozycję **współpracownicy** w górnej części okna:

    ![Wybieranie współpracowników](./media/proxyclick-tutorial/user1.png)

1. Wybierz pozycję **Dodaj współpracownika**:

    ![Wybierz pozycję Dodaj współpracownika](./media/proxyclick-tutorial/user2.png)

1. W sekcji **Dodaj współpracownika** wykonaj następujące czynności.

    ![Dodawanie sekcji współpracownika](./media/proxyclick-tutorial/user3.png)

    1. W polu **adres e-mail** wprowadź adres e-mail użytkownika. W tym przypadku **brittasimon \@ contoso.com**.

    1. W polu **imię** Wprowadź imię użytkownika. W tym przypadku **Britta**.

    1. W polu **nazwisko** Wprowadź nazwisko użytkownika. W tym przypadku **Simon**.

    1. Wybierz pozycję **Dodaj użytkownika**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz musisz przetestować konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka Proxyclick w panelu dostępu należy automatycznie zalogować się do wystąpienia Proxyclick, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [dostęp i używanie aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)