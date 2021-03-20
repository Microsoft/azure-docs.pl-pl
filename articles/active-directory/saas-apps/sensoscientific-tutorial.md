---
title: 'Samouczek: integracja Azure Active Directory z systemem monitorowania temperatury sieci bezprzewodowej SensoScientific | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i SensoScientific systemie monitorowania temperatury bezprzewodowej.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: 27512ac694f20544f1fdd5b79b27d7bf2cabdd66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92675490"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Samouczek: integracja Azure Active Directory z systemem monitorowania temperatury sieci bezprzewodowej SensoScientific

W tym samouczku dowiesz się, jak zintegrować system monitorowania temperatury bezprzewodowej SensoScientific z Azure Active Directory (Azure AD).
Integracja systemu monitorowania temperatury bezprzewodowej SensoScientific z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do systemu monitorowania temperatury sieci bezprzewodowej SensoScientific.
* Możesz pozwolić użytkownikom na automatyczne logowanie do SensoScientific systemu monitorowania temperatury sieci bezprzewodowej (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD przy użyciu systemu monitorowania temperatury bezprzewodowej SensoScientific, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym dla systemu monitorowania temperatury sieci bezprzewodowej SensoScientific

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* System monitorowania temperatury sieci bezprzewodowej SensoScientific obsługuje logowanie jednokrotne w **dostawcy tożsamości**

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Dodawanie systemu monitorowania temperatury sieci bezprzewodowej SensoScientific z galerii

Aby skonfigurować integrację systemu monitorowania temperatury sieci bezprzewodowej SensoScientific z usługą Azure AD, należy dodać system monitorowania temperatury sieci bezprzewodowej SensoScientific z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać system monitorowania temperatury sieci bezprzewodowej SensoScientific z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SensoScientific system monitorowania temperatury sieci bezprzewodowej**, wybierz pozycję **SensoScientific system monitorowania temperatury bezprzewodowej** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![System monitorowania temperatury bezprzewodowej SensoScientific na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji należy skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu systemu monitorowania temperatury sieci bezprzewodowej SensoScientific na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w systemie monitorowania temperatury sieci bezprzewodowej SensoScientific.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD przy użyciu systemu monitorowania temperatury bezprzewodowej SensoScientific, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Logowanie jednokrotne w systemie monitorowania temperatury bezprzewodowej SensoScientific](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)** , aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie systemu monitorowania temperatury sieci bezprzewodowej SensoScientific użytkownika testowego](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)** — Aby uzyskać odpowiednik Britta Simon w systemie SensoScientific temperatury bezprzewodowej, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD przy użyciu systemu monitorowania temperatury bezprzewodowej SensoScientific, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **systemu monitorowania temperatury bezprzewodowej SensoScientific** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![SensoScientific sieci bezprzewodowej monitorowania temperatury i adresów URL logowania jednokrotnego](common/preintegrated.png)

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie systemu monitorowania temperatury bezprzewodowej SensoScientific** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>Konfigurowanie jednego Sign-On systemu monitorowania temperatury bezprzewodowej SensoScientific

1. Zaloguj się do aplikacji systemowej monitorowania temperatury bezprzewodowej SensoScientific jako administrator.

1. W menu nawigacji u góry kliknij pozycję **Konfiguracja** i przejdź do pozycji **Konfiguruj** w obszarze **Logowanie jednokrotne** , aby otworzyć ustawienia rejestracji jednokrotnej i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Wybierz **nazwę wystawcy** jako usługę Azure AD.

    b. W polu tekstowym **adres URL wystawcy** wklej **Identyfikator usługi Azure AD** , który został skopiowany z Azure Portal.

    c. W polu tekstowym **adres URL usługi Single Sign-On** wklej **adres URL logowania** , który został skopiowany z Azure Portal.

    d. W polu tekstowym **adres URL usługi Single Sign-Out** wklej **adres URL wylogowania** , który został skopiowany z Azure Portal.

    e. Przejrzyj certyfikat pobrany z Azure Portal i przekaż go tutaj.

    f. Kliknij pozycję **Zapisz**.

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

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do systemu monitorowania temperatury SensoScientific bezprzewodowej.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **SensoScientific system monitorowania temperatury sieci bezprzewodowej**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **SensoScientific system monitorowania temperatury sieci bezprzewodowej**.

    ![Łącze systemu monitorowania temperatury bezprzewodowej SensoScientific na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Utwórz użytkownika testowego systemu monitorowania temperatury sieci bezprzewodowej SensoScientific

Aby umożliwić użytkownikom usługi Azure AD logowanie się w systemie monitorowania temperatury bezprzewodowej SensoScientific, muszą one być obsługiwane w systemie monitorowania temperatury sieci bezprzewodowej SensoScientific. Współpraca z [zespołem pomocy technicznej systemu monitorowania temperatury bezprzewodowej SensoScientific](https://www.sensoscientific.com/contact-us/) , aby dodać użytkowników na platformie systemu monitorowania temperatury bezprzewodowej SensoScientific. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka system monitorowania temperatury bezprzewodowej SensoScientific w panelu dostępu należy automatycznie zalogować się do systemu monitorowania temperatury sieci SensoScientific, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)