---
title: 'Samouczek: integracja Azure Active Directory z usługą Veritas Enterprise magazyn. Logowanie jednokrotne w chmurze | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Veritas Enterprise magazynu. Logowanie jednokrotne w chmurze.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: cdb3f30813d2650737cfa43507cef2b1d456573d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88532538"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Samouczek: integracja Azure Active Directory z usługą Veritas Enterprise magazyn. Logowanie jednokrotne w chmurze

W tym samouczku dowiesz się, jak zintegrować usługę Veritas Enterprise magazynu. Logowanie jednokrotne w chmurze za pomocą Azure Active Directory (Azure AD).
Integrowanie usługi Veritas Enterprise magazyn. Logowanie jednokrotne w chmurze w usłudze Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do magazynu Veritas Enterprise. w chmurze Logowanie jednokrotne.
* Możesz umożliwić użytkownikom automatyczne logowanie do usługi Veritas Enterprise magazynu. Logowanie jednokrotne w chmurze (Logowanie jednokrotne) za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD za pomocą programu Veritas Enterprise magazyn. Logowanie jednokrotne w chmurze wymaga następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Veritas Enterprise magazyn. subskrypcja obsługująca Logowanie jednokrotne w chmurze

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Veritas Enterprise magazyn. Logowanie jednokrotne w **chmurze obsługuje** zainicjowanie rejestracji jednokrotnej

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Dodawanie usługi Veritas Enterprise magazynu. Logowanie jednokrotne w chmurze z galerii

Aby skonfigurować integrację programu Veritas Enterprise magazyn. w chmurze Logowanie jednokrotne do usługi Azure AD należy dodać program VERITAS Enterprise magazynu. Logowanie jednokrotne w chmurze z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać program VERITAS Enterprise magazynu. Logowanie jednokrotne w chmurze z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Veritas Enterprise magazyn. Logowanie JEDNOkrotne w chmurze**, wybierz pozycję **Veritas Enterprise magazyn. Logowanie jednokrotne w chmurze** z poziomu panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Usługa Veritas Enterprise magazynu. Logowanie jednokrotne w chmurze na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą programu Veritas Enterprise magazynu. Logowanie jednokrotne w chmurze na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, relacja linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w magazynie w systemie Veritas przedsiębiorstwa. należy ustanowić Logowanie jednokrotne w chmurze.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą programu Veritas Enterprise magazynu. Logowanie jednokrotne w chmurze wymaga wykonania następujących bloków konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj usługę Veritas Enterprise magazyn. Logowanie JEDNOkrotne w chmurze](#configure-veritas-enterprise-vaultcloud-sso-single-sign-on)** — Rejestracja logowania jednokrotnego, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz program VERITAS Enterprise magazyn. Test rejestracji jednokrotnej w chmurze dla użytkownika](#create-veritas-enterprise-vaultcloud-sso-test-user)** — ma odpowiednik Britta Simon w programie Veritas Enterprise. w chmurze jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą programu Veritas Enterprise magazynu. Logowanie jednokrotne w chmurze, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na platformie **Veritas Enterprise magazynu. Strona integracji aplikacji logowania jednokrotnego w chmurze** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Veritas Enterprise magazyn. domena logowania jednokrotnego w chmurze i adresy URL logowania jednokrotnego](common/sp-identifier-reply.png)

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. W polu **Identyfikator** Użyj adresu URL dla centrum danych:

    | Centrum danych| Adres URL |
    |----------|----|
    | Ameryka Północna| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Azja i Pacyfik| `https://auth.syd.archivecloud.net`|

    c. W polu tekstowym **adres URL odpowiedzi** Użyj adresu URL dla centrum danych:

    | Centrum danych| Adres URL |
    |----------|----|
    | Ameryka Północna| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Azja i Pacyfik| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z firmą Veritas Enterprise, aby uzyskać tę wartość przez [zespół obsługi klienta rejestracji jednokrotnej w chmurze](https://www.veritas.com/support/.html) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie usługi Veritas Enterprise w chmurze — logowanie JEDNOkrotne** , skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-veritas-enterprise-vaultcloud-sso-single-sign-on"></a>Skonfiguruj usługę Veritas Enterprise magazynu. pojedyncze Sign-On logowania jednokrotnego w chmurze

Aby skonfigurować Logowanie jednokrotne w programie **Veritas Enterprise magazynu. po stronie logowania jednokrotnego w chmurze** musisz wysłać pobrany **certyfikat (base64)** i odpowiednie skopiowane adresy URL z Azure Portal do programu [Veritas Enterprise magazynu. zespół obsługi logowania jednokrotnego w chmurze](https://www.veritas.com/support/.html). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz brittasimon@yourcompanydomain.extension . Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do magazynu Veritas Enterprise. w chmurze Logowanie jednokrotne.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Veritas Enterprise magazyn. Logowanie jednokrotne w chmurze**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Veritas Enterprise magazyn. Logowanie JEDNOkrotne w chmurze**.

    ![Usługa Veritas Enterprise magazynu. link rejestracji jednokrotnej w chmurze na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Utwórz usługę Veritas Enterprise magazyn. Użytkownik testowy logowania jednokrotnego w chmurze

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w programie Veritas Enterprise magazynu. Logowanie jednokrotne w chmurze. Pracuj z usługą [Veritas Enterprise magazyn. zespół obsługi logowania jednokrotnego w chmurze](https://www.veritas.com/support/.html) umożliwiający dodanie użytkowników w magazynie Veritas Enterprise. platforma rejestracji jednokrotnej w chmurze. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka rejestracji jednokrotnej w chmurze w panelu dostępu należy automatycznie zalogować się do magazynu Veritas Enterprise. w chmurze, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

