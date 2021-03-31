---
title: 'Samouczek: integracja Azure Active Directory z usługą Gigya | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Gigya.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/18/2019
ms.author: jeedes
ms.openlocfilehash: 849b54e6d96b8f6f33f2a0925b9a1b9c3eebd56b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92450088"
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Samouczek: integracja Azure Active Directory z usługą Gigya

Z tego samouczka dowiesz się, jak zintegrować aplikację Gigya z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Gigya z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Gigya.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Gigya (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Gigya, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Gigya z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Gigya obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

## <a name="adding-gigya-from-the-gallery"></a>Dodawanie aplikacji Gigya z galerii

Aby skonfigurować integrację aplikacji Gigya z usługą Azure AD, musisz dodać aplikację Gigya z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Gigya z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Gigya**, wybierz pozycję **Gigya** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Gigya na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Gigya, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Gigya.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD dla aplikacji Gigya, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Gigya](#configure-gigya-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego w usłudze Gigya](#create-gigya-test-user)** — aby w aplikacji Gigya utworzyć odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Gigya, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Gigya** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji Gigya](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `http://<companyname>.gigya.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Gigya](https://developers.gigya.com/display/GD/Opening+A+Support+Incident). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie aplikacji Gigya** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-gigya-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji Gigya

1. W innym oknie przeglądarki internetowej zaloguj się jako administrator do witryny firmowej aplikacji Gigya.

2. Wybierz pozycję **Settings \> SAML Login** (Ustawienia > Logowanie SAML), a następnie kliknij przycisk **Add** (Dodaj).
   
    ![Logowanie SAML](./media/gigya-tutorial/ic789532.png "Logowanie SAML")

3. W sekcji **SAML Login** (Logowanie SAML) wykonaj następujące czynności:
   
    ![Konfiguracja SAML](./media/gigya-tutorial/ic789533.png "Konfiguracja SAML")
   
    a. W polu tekstowym **Name** (Nazwa) wpisz nazwę konfiguracji.
   
    b. W polu tekstowym **Issuer** (Wystawca) wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal. 
   
    c. W polu tekstowym **Single Sign-On Service URL** (Adres URL usługi logowania jednokrotnego) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.
   
    d. W polu tekstowym **Name ID Format** (Format identyfikatora nazwy) wklej wartość **formatu identyfikatora nazwy** skopiowaną z witryny Azure Portal.
   
    e. Otwórz w Notatniku swój certyfikat zakodowany w formacie base-64 pobrany z witryny Azure Portal, skopiuj zawartość do schowka, a następnie wklej ją w polu tekstowym **Certyfikat X.509**.
   
    f. Kliknij przycisk **Zapisz ustawienia**.

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Gigya.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Gigya**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Gigya**.

    ![Link Gigya na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-gigya-test-user"></a>Tworzenie użytkownika testowego aplikacji Gigya

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Gigya, należy aprowizować ich w aplikacji Gigya. W przypadku aplikacji Gigya aprowizacja jest zadaniem ręcznym.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aby aprowizować konta użytkowników, wykonaj następujące czynności:

1. Zaloguj się do firmowej witryny aplikacji **Gigya** jako administrator.

2. Wybierz pozycję **Admin \> Manage Users** (Administrator > Zarządzaj użytkownikami), a następnie kliknij pozycję **Invite Users** (Zaproś użytkowników).
   
    ![Zarządzanie użytkownikami](./media/gigya-tutorial/ic789535.png "Zarządzanie użytkownikami")

3. W oknie dialogowym Invite users (Zaproś użytkowników) wykonaj następujące kroki:
   
    ![Zapraszanie użytkowników](./media/gigya-tutorial/ic789536.png "Zapraszanie użytkowników")
   
    a. W polu tekstowym **Email** (Adres e-mail) wpisz alias e-mail prawidłowego konta usługi Azure Active Directory, które chcesz aprowizować.
    
    b. Kliknij przycisk **Invite User** (Zaproś użytkownika).
      
    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.
    > 

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Gigya na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Gigya, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)