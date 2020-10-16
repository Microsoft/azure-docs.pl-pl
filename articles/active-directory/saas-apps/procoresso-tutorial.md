---
title: 'Samouczek: integracja Azure Active Directory z podstawowym logowaniem jednokrotnym | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i autonomicznym logowaniem jednokrotnym.
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
ms.openlocfilehash: ea69f1e634c66bff98462d41f3eeef19f8e15eb0
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92105247"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Samouczek: integracja z usługą Azure Active Directory przy użyciu podstawowego logowania jednokrotnego

W tym samouczku dowiesz się, jak zintegrować podstawowe Logowanie jednokrotne z usługą Azure Active Directory (Azure AD).
Integracja jednordzeniowego logowania jednokrotnego za pomocą usługi Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do podstawowego logowania jednokrotnego.
* Możesz pozwolić użytkownikom na automatyczne logowanie do podstawowego logowania jednokrotnego (Logowanie jednokrotne) przy użyciu swoich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD przy użyciu podstawowego logowania jednokrotnego, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja włączonej jednordzeniowej rejestracji jednokrotnej

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Podstawowe Logowanie jednokrotne obsługuje **dostawcy tożsamości** zainicjowane Logowanie jednokrotne

## <a name="adding-procore-sso-from-the-gallery"></a>Dodawanie podstawowego logowania jednokrotnego z galerii

Aby skonfigurować integrację wielordzeniowego logowania jednokrotnego w usłudze Azure AD, musisz dodać podstawowe Logowanie jednokrotne z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać podstawowe Logowanie jednokrotne z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz podstawowe polecenie **rejestracji jednokrotnej**, wybierz pozycję **samopodstawowe Logowanie jednokrotne** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Podstawowe Logowanie jednokrotne na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego w usłudze Azure AD na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ramach podstawowego logowania jednokrotnego.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD przy użyciu podstawowego logowania jednokrotnego, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj jednordzeniowe logowanie](#configure-procore-sso-single-sign-on)** jednokrotne — w celu skonfigurowania pojedynczych ustawień Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie prordzeniowego użytkownika testowego rejestracji](#create-procore-sso-test-user)** jednokrotnej — Aby uzyskać odpowiednik usługi Britta Simon w ramach podstawowego logowania jednokrotnego, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne w usłudze Azure AD przy użyciu podstawowego logowania jednokrotnego, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie **prordzeniowa** integracja aplikacji SSO wybierz pozycję **Logowanie**jednokrotne.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![Podstawowe informacje dotyczące rejestracji jednokrotnej domeny i adresów URL logowania jednokrotnego](common/preintegrated.png)

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie prordzeniowego logowania jednokrotnego** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-procore-sso-single-sign-on"></a>Konfigurowanie samoSign-Onowego logowania jednokrotnego

1. Aby skonfigurować Logowanie jednokrotne na stronie autonomicznej **rejestracji jednokrotnej** , zaloguj się do witryny firmowej firmy jako administrator.

2. Z listy rozwijanej przybornika kliknij pozycję **administrator** , aby otworzyć stronę Ustawienia logowania jednokrotnego.

    ![Zrzut ekranu przedstawia podstawową witrynę firmy z wybranym katalogiem.](./media/procoresso-tutorial/procore_tool_admin.png)

3. Wklej wartości w polach zgodnie z opisem poniżej.

    ![Zrzut ekranu przedstawia okno dialogowe Dodawanie osoby.](./media/procoresso-tutorial/procore_setting_admin.png) 

    a. W polu tekstowym **adres URL wystawcy logowania** jednokrotnego wklej wartość **identyfikatora usługi Azure AD** , który został skopiowany z Azure Portal.

    b. W polu **adres URL celu** logowania przy użyciu protokołu SAML wklej wartość **adresu URL logowania** skopiowanego z Azure Portal.

    c. Teraz Otwórz **plik XML metadanych Federacji** pobrany powyżej z Azure Portal i skopiuj certyfikat w tagu o nazwie **x509**. Wklej skopiowaną wartość do pola **certyfikatu x509 logowania** jednokrotnego.

4. Kliknij przycisk **Zapisz zmiany**.

5. Po przeprowadzeniu tych ustawień należy wysłać **nazwę domeny** (np. **contoso.com**), za pomocą której rejestrujesz się w celu uzyskania podstawowego [zespołu pomocy technicznej](https://support.procore.com/) i aktywują federacyjne Logowanie jednokrotne dla tej domeny.

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

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do podstawowego logowania jednokrotnego.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **samopodstawowe Logowanie jednokrotne**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **samopodstawowe logowanie JEDNOkrotne**.

    ![Podstawowe łącze rejestracji jednokrotnej na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-procore-sso-test-user"></a>Tworzenie wielordzeniowego użytkownika testowego logowania jednokrotnego

Wykonaj poniższe czynności, aby utworzyć użytkownika testowego na potrzeby podstawowego logowania jednokrotnego.

1. Zaloguj się do witryny firmowej firmy jako administrator.    

2. Z listy rozwijanej przybornika kliknij pozycję **katalog** , aby otworzyć stronę katalog firmy.

    ![Zrzut ekranu przedstawia podstawową lokację firmy z katalogiem wybranym z przybornika.](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Kliknij opcję **Dodaj osobę** , aby otworzyć formularz, i wprowadź następujące opcje:

    ![Zrzut ekranu przedstawia Boylaną, gdzie można wprowadzać informacje o użytkowniku.](./media/procoresso-tutorial/Procore_user_add.png)

    a. W polu tekstowym **imię i nazwisko** wpisz imię użytkownika, np. **Britta**.

    b. W polu **tekstowym nazwisko wpisz nazwisko użytkownika** , np. **Simon**.

    c. W polu tekstowym **adres e-mail** wpisz adres e-mail użytkownika, np BrittaSimon@contoso.com .

    d. Wybierz **szablon uprawnień** jako **Zastosuj szablon uprawnień później**.

    e. Kliknij pozycję **Utwórz**.

4. Sprawdź i Zaktualizuj szczegóły nowo dodanego kontaktu.

    ![Zrzut ekranu przedstawia stronę edycji, na której można sprawdzić ustawienia użytkownika.](./media/procoresso-tutorial/Procore_user_check.png)

5. Kliknij pozycję **Zapisz i Wyślij zaproszenie** (jeśli jest wymagana zapraszanie za pomocą poczty e-mail) lub **Zapisz** (Zapisz bezpośrednio), aby ukończyć rejestrację użytkownika.
    
    ![Zrzut ekranu przedstawia bieżące ustawienia projektu, w których można zapisywać i wysyłać zaproszenia.](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu wielordzeniowego kafelka rejestracji jednokrotnej w panelu dostępu należy automatycznie zalogować się do podstawowego logowania jednokrotnego, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

