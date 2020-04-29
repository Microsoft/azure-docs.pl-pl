---
title: 'Samouczek: integracja Azure Active Directory z systemem inspekcji opartym na wyjaśnieniu | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i system inspekcji oparty na wyjaśnieniu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 419744c2-3b71-4953-9434-99b632a10854
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/27/2019
ms.author: jeedes
ms.openlocfilehash: 49c15365b60359bc393dcd854c7f9487810f0a7d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73156311"
---
# <a name="tutorial-azure-active-directory-integration-with-explanation-based-auditing-system"></a>Samouczek: integracja Azure Active Directory z systemem inspekcji opartym na wyjaśnieniu

W tym samouczku dowiesz się, jak zintegrować system inspekcji oparty na wyjaśnieniu z Azure Active Directory (Azure AD).
Integracja systemu inspekcji opartego na wyjaśnieniu z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do systemu inspekcji opartego na wyjaśnieniu.
* Możesz umożliwić użytkownikom automatyczne logowanie do systemu inspekcji opartego na wyjaśnieniu (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD przy użyciu systemu inspekcji opartego na wyjaśnieniu, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Wyjaśnienie subskrypcja obsługująca Logowanie jednokrotne systemu

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* System inspekcji oparty na wyjaśnieniu obsługuje logowanie jednokrotne w usłudze **SP**

* System inspekcji oparty na wyjaśnieniu obsługuje Inicjowanie obsługi użytkowników **just in Time** 

## <a name="adding-explanation-based-auditing-system-from-the-gallery"></a>Dodawanie systemu inspekcji opartego na wyjaśnieniu z galerii

Aby skonfigurować integrację systemu inspekcji opartego na wyjaśnieniu z usługą Azure AD, należy dodać system inspekcji oparty na wyjaśnieniu z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać system inspekcji oparty na wyjaśnieniu z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **system inspekcji oparty na wyjaśnieniu**, wybierz opcję **system inspekcji oparty na wyjaśnieniu** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![System inspekcji oparty na wyjaśnieniu na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji należy skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD przy użyciu systemu inspekcji opartego na wyjaśnieniu na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w systemie inspekcji opartym na wyjaśnieniu.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD przy użyciu systemu inspekcji opartego na wyjaśnieniu, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. Skonfiguruj funkcję inspekcji logowania jednokrotnego na **[podstawie wyjaśnień](#configure-explanation-based-auditing-system-single-sign-on)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz oparty na wyjaśnieniu system testowy użytkownika testowego](#create-explanation-based-auditing-system-test-user)** — Aby uzyskać odpowiednik Britta Simon w systemie inspekcji opartym na tym, który jest połączony z reprezentacją usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD przy użyciu systemu inspekcji opartego na wyjaśnieniu, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie Omówienie integracji aplikacji **systemu na podstawie wyjaśnień** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Wyjaśnienie na podstawie informacji na temat domeny systemu i adresów URL logowania jednokrotnego](common/sp-signonurl.png)

    W polu tekstowym **adres URL logowania** wpisz adres URL:`https://ebas.maizeanalytics.com`

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-explanation-based-auditing-system-single-sign-on"></a>Konfigurowanie logowania jednokrotnego systemu inspekcji opartego na wyjaśnieniu

Aby skonfigurować Logowanie jednokrotne na stronie **systemowej inspekcji opartej na wyjaśnieniu** , należy wysłać **adres URL metadanych federacji aplikacji** do programu z [wyjaśnieniem zespół pomocy technicznej systemu inspekcji](mailto:support@maizeanalytics.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon\@yourcompanydomain. Extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do systemu inspekcji opartego na wyjaśnieniu.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **system inspekcji oparty na wyjaśnieniu**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz opcję **system inspekcji oparty na wyjaśnieniu**.

    ![Link systemowy do inspekcji na podstawie wyjaśnień na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-explanation-based-auditing-system-test-user"></a>Utwórz użytkownika testowego systemu inspekcji na podstawie wyjaśnień

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w systemie inspekcji opartym na wyjaśnieniu. System inspekcji oparty na wyjaśnieniu obsługuje funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w systemie inspekcji opartym na wyjaśnieniu, zostanie utworzony nowy po uwierzytelnieniu.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka system inspekcji na podstawie wyjaśnień w panelu dostępu należy automatycznie zalogować się do systemu inspekcji opartego na wyjaśnieniu, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

