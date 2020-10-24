---
title: 'Samouczek: integracja Azure Active Directory z usługą Signagelive | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i platformą Signagelive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.openlocfilehash: ad6a4d075ea861068ad0c929d92cd2f12efa6121
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516108"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Samouczek: integracja Azure Active Directory z usługą Signagelive

Z tego samouczka dowiesz się, jak zintegrować platformę Signagelive z usługą Azure Active Directory (Azure AD).
Integrowanie platformy Signagelive z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do platformy Signagelive.
* Możesz umożliwić użytkownikom automatyczne logowanie się do usługi Signagelive (Logowanie jednokrotne) przy użyciu kont w usłudze Azure AD.
* Kontami można zarządzać w jednej centralnej lokalizacji: Azure Portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md). Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z platformą Signagelive, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [miesięczną wersję próbną](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja usługi Signagelive z włączoną obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Signagelive obsługuje logowanie jednokrotne zainicjowane przez usługę SP.

## <a name="add-signagelive-from-the-gallery"></a>Dodaj Signagelive z galerii

Aby skonfigurować integrację programu Signagelive z usługą Azure AD, najpierw Dodaj Signagelive z galerii do listy zarządzanych aplikacji SaaS.

Aby dodać Signagelive z galerii, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)w lewym okienku wybierz ikonę **Azure Active Directory** .

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz opcję **wszystkie aplikacje** .

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk  **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Signagelive**. 

     ![Signagelive na liście wyników](common/search-new-app.png)

5. W okienku wyników wybierz pozycję **Signagelive** , a następnie wybierz przycisk **Dodaj** , aby dodać aplikację.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z platformą Signagelive, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy nawiązać połączenie między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Signagelive.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Signagelive, najpierw wykonaj następujące bloki konstrukcyjne:

1. [Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on) — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. [Skonfiguruj logowanie](#configure-signagelive-single-sign-on) jednokrotne w usłudze Signagelive, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą użytkownika Britta Simon.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. [Utwórz użytkownika testowego Signagelive](#create-a-signagelive-test-user) , aby uzyskać odpowiednik Britta Simon w Signagelive, który jest połączony z reprezentacją usługi Azure AD.
6. [Przetestuj logowanie jednokrotne](#test-single-sign-on), aby sprawdzić działanie konfiguracji.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Signagelive, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Signagelive** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML** , aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz pozycję **Edytuj** , aby otworzyć okno dialogowe **Podstawowa konfiguracja SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja języka SAML** wykonaj następujące czynności:

    ![Domena i adresy URL platformy Signagelive — informacje dotyczące logowania jednokrotnego](common/sp-signonurl.png)

    W polu **adres URL logowania** wprowadź adres URL, który używa następującego wzorca:  `https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj wartość przy użyciu rzeczywistego adresu URL logowania. Aby uzyskać wartość, skontaktuj się z [zespołem obsługi klienta Signagelive](mailto:support@signagelive.com) . Można również odwołać się do wzorców, które są wyświetlane w sekcji podstawowe informacje o **konfiguracji SAML** w Azure Portal.

5. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** , aby pobrać **certyfikat (RAW)** z danej opcji zgodnie z wymaganiami. Następnie zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

6. W sekcji **Konfiguracja Signagelive** Skopiuj wymagane adresy URL.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-signagelive-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Signagelive

Aby skonfigurować Logowanie jednokrotne po stronie Signagelive, Wyślij pobranego **certyfikatu (RAW)** i skopiowane adresy url z Azure Portal do [zespołu pomocy technicznej Signagelive](mailto:support@signagelive.com). Upewnij się, że połączenie SSO protokołu SAML zostało prawidłowo ustawione na obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym **użytkownik** wykonaj następujące czynności.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wprowadź wartość " brittasimon@yourcompanydomain.extension ". Na przykład w tym przypadku można wpisać " BrittaSimon@contoso.com ".

    c. Zaznacz pole wyboru **Pokaż hasło** , a następnie zanotuj wartość wyświetlaną w polu hasło.

    d. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji udostępnisz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do platformy Signagelive.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Signagelive**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Signagelive**.

    ![Link Signagelive na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Wybierz przycisk **Dodaj użytkownika** . Następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** , na liście **Użytkownicy** wybierz pozycję **Britta Simon**. Następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

6. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, następnie w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz przycisk **Przypisz** .

### <a name="create-a-signagelive-test-user"></a>Tworzenie użytkownika testowego Signagelive

W tej sekcji utworzysz użytkownika o nazwie Britta Simon na platformie Signagelive. Współpracuj z [zespołem pomocy technicznej Signagelive](mailto:support@signagelive.com) , aby dodać użytkowników z platformy Signagelive. Przed użyciem logowania jednokrotnego należy utworzyć i aktywować użytkowników.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą portalu aplikacji.

Po wybraniu kafelka **Signagelive** w portalu aplikacji należy automatycznie zalogować się. Aby uzyskać więcej informacji na temat portalu aplikacji, zobacz [co to jest portal usługi webapps?](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)