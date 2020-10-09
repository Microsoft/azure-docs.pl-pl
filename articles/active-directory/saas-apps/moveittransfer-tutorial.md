---
title: 'Samouczek: integracja Azure Active Directory z transferem MOVEit — integracja z usługą Azure AD | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i transfer MOVEit — integracja z usługą Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 4ec0a3082b5978e0c540a2e0a70843081201a29b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88544133"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Samouczek: integracja Azure Active Directory z transferem MOVEit — integracja z usługą Azure AD

W tym samouczku dowiesz się, jak zintegrować usługę MOVEit transfer — integrację z usługą Azure AD z usługą Azure Active Directory (Azure AD).
Integrowanie transferu MOVEit — integracja usługi Azure AD z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi MOVEit transfer — integracja z usługą Azure AD.
* Możesz umożliwić użytkownikom automatyczne logowanie do MOVEit transfer — integracja z usługą Azure AD (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD za pomocą transferu MOVEit — integracja z usługą Azure AD wymaga następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Transfer MOVEit — subskrypcja obsługująca Logowanie jednokrotne w usłudze Azure AD

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Transfer MOVEit — integracja z usługą Azure AD obsługuje logowanie jednokrotne w ramach platformy **SP**

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Dodawanie MOVEit transfer — integracja z usługą Azure AD z galerii

Aby skonfigurować integrację transferu MOVEit — integrację usługi Azure AD z usługą Azure AD, musisz dodać integrację MOVEit-Azure AD z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać MOVEit transfer — integrację z usługą Azure AD z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **MOVEit transfer — integracja z usługą Azure AD**, wybierz pozycję **MOVEit transfer — integracja z usługą Azure AD** z poziomu panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Transfer MOVEit — integracja z usługą Azure AD na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji należy skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą transferu MOVEit — integracja z usługą Azure AD oparta na użytkowniku testowym o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze MOVEit transfer-Azure AD Integration.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi MOVEit transfer — integracja z usługą Azure AD, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie transferu MOVEit — logowanie](#configure-moveit-transfer---azure-ad-integration-single-sign-on)** jednokrotne w usłudze Azure AD — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie MOVEit transfer — Użytkownik testowy integracji usługi Azure AD](#create-moveit-transfer---azure-ad-integration-test-user)** , który ma odpowiednik Britta Simon w MOVEit transfer — integracja z usługą Azure AD, która jest połączona z reprezentacją usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD przy użyciu usługi MOVEit transfer — integracja z usługą Azure AD, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja **MOVEit transfer-Azure AD Integration** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych wartość **identyfikatora** i **adres URL odpowiedzi** są pobierane automatycznie w sekcji **Podstawowa konfiguracja SAML** :

    ![Transfer MOVEit — informacje dotyczące logowania jednokrotnego w ramach domeny i adresów URL integracji usługi Azure AD](common/sp-identifier-reply.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://contoso.com`

    > [!NOTE]
    > Wartość **adresu URL logowania** nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Contact [MOVEit transfer — zespół obsługi klienta integracji usługi Azure AD](https://community.ipswitch.com/s/support) , aby pobrać wartość. **Plik metadanych dostawcy usług** można pobrać z **adresu URL metadanych dostawcy usług** , który został wyjaśniony w dalszej części artykułu **Konfigurowanie usługi MOVEit transfer — Azure AD Integration logowanie** jednokrotne w samouczku. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie usługi MOVEit transfer — Azure AD Integration** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>Konfigurowanie transferu MOVEit — pojedyncza Sign-On integracji usługi Azure AD

1. Zaloguj się do dzierżawy transferu MOVEit jako administrator.

2. W okienku nawigacji po lewej stronie kliknij pozycję **Settings** (Ustawienia).

    ![Sekcja ustawień po stronie aplikacji](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Kliknij link **Single jednokrotnego** , który znajduje się w obszarze **zasady zabezpieczeń — > uwierzytelnienie użytkownika**.

    ![Zasady zabezpieczeń po stronie aplikacji](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Kliknij link adresu URL metadanych, aby pobrać dokument metadanych.

    ![Adres URL metadanych dostawcy usług](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Sprawdź, czy **entityID** dopasowuje **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** .
   * Sprawdź, czy adres URL lokalizacji **AssertionConsumerService** pasuje do **adresu URL odpowiedzi**  w sekcji **Podstawowa konfiguracja protokołu SAML** .
    
     ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Kliknij przycisk **Dodaj dostawcę tożsamości** , aby dodać nowego dostawcy tożsamości federacyjnych.

    ![Dodawanie dostawcy tożsamości](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Kliknij przycisk **Przeglądaj...** , aby wybrać plik metadanych pobrany z Azure Portal, a następnie kliknij pozycję **Dodaj dostawcę tożsamości** w celu przekazania pobranego pliku.

    ![Dostawca tożsamości SAML](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Wybierz pozycję "**tak**" jako **włączone** na stronie **Edytowanie ustawień dostawcy tożsamości federacyjnych** , a następnie kliknij przycisk **Zapisz**.

    ![Ustawienia federacyjnego dostawcy tożsamości](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. Na stronie **Edytowanie ustawień użytkownika dostawcy tożsamości federacyjnych** wykonaj następujące czynności:
    
    ![Edytowanie ustawień dostawcy tożsamości federacyjnych](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Wybierz pozycję **SAML NameID** jako **nazwę logowania**.
    
    b. Wybierz opcję **inne** jako **pełna nazwa** i w polu tekstowym **nazwa atrybutu** wprowadź wartość: `http://schemas.microsoft.com/identity/claims/displayname` .
    
    c. Wybierz pozycję **inne** jako **wiadomość e-mail** , a w polu tekstowym **nazwa atrybutu** wprowadź wartość: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .
    
    d. Wybierz pozycję **tak** jako **AutoCreate Account na jednokrotnego**.
    
    e. Kliknij przycisk **Save** (Zapisz).

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

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi MOVEit transfer — integracja z usługą Azure AD.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **transfer MOVEit — integracja z usługą Azure AD**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **transfer MOVEit — integracja z usługą Azure AD**.

    ![Link MOVEit transfer-Azure AD Integration na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Tworzenie transferu MOVEit — Użytkownik testowy integracji usługi Azure AD

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w usłudze transfer MOVEit — integracja z usługą Azure AD. MOVEit transfer — integracja z usługą Azure AD obsługuje obsługę just in Time, która została włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu do usługi MOVEit transfer — integracja z usługą Azure AD, jeśli jeszcze nie istnieje.

>[!NOTE]
>Jeśli musisz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej MOVEit transfer — Azure AD Integration Client](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka MOVEit transfer — integracja z usługą Azure AD w panelu dostępu należy automatycznie zalogować się do integracji MOVEit transfer-Azure AD, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

