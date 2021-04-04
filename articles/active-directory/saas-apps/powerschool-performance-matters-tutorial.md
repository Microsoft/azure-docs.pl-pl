---
title: 'Samouczek: integracja Azure Active Directory z zagadnieniami dotyczącymi wydajności Powerschool | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i wydajnością Powerschool.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: jeedes
ms.openlocfilehash: 1312a995589b0408edc56a0883cc9fd899fc9e6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515444"
---
# <a name="tutorial-azure-active-directory-integration-with-powerschool-performance-matters"></a>Samouczek: integracja Azure Active Directory z zagadnieniami dotyczącymi wydajności Powerschool

W tym samouczku dowiesz się, jak zintegrować zagadnienia dotyczące wydajności Powerschool z Azure Active Directory (Azure AD).
Integracja zagadnień dotyczących wydajności Powerschool z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do zagadnień dotyczących wydajności Powerschool.
* Możesz pozwolić użytkownikom na automatyczne logowanie się do Powerschool związanych z wydajnością (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z zagadnieniami wydajności Powerschool, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Powerschool wydajność — subskrypcja z włączonym logowaniem jednokrotnym

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Zagadnienia dotyczące wydajności Powerschool obsługują Logowanie jednokrotne w usłudze **SP**

## <a name="adding-powerschool-performance-matters-from-the-gallery"></a>Dodawanie zagadnień dotyczących wydajności Powerschool z galerii

Aby skonfigurować integrację zagadnień dotyczących wydajności Powerschool w usłudze Azure AD, musisz dodać zagadnienia dotyczące wydajności Powerschool z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać zagadnienia dotyczące wydajności Powerschool z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Powerschool wydajność**, wybierz pozycję **Powerschoole zagadnienia dotyczące wydajności** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Zagadnienia dotyczące wydajności Powerschool na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD z zagadnieniami dotyczącymi wydajności Powerschool na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Powerschool wydajności.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD z zagadnieniami dotyczącymi wydajności Powerschool, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania](#configure-powerschool-performance-matters-single-sign-on)** jednokrotnego dotyczącego wydajności Powerschool — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Twórz zagadnienia dotyczące wydajności Powerschool użytkownika testowego](#create-powerschool-performance-matters-test-user)** , aby dysponować odpowiednikiem Britta Simon w kwestiach dotyczących wydajności, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD z zagadnieniami dotyczącymi wydajności Powerschool, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z **zagadnieniami dotyczącymi wydajności Powerschool** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujący krok:

    ![Informacje o domenie i adresach URL aplikacji [Nazwa aplikacji] na potrzeby logowania jednokrotnego](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: 
    
    ```https
        https://ola.performancematters.com/ola/?clientcode=<Client Code>
        https://unify.performancematters.com/?idp=<IDP>
    ```

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej Powerschool](mailto:pmsupport@powerschoo.com) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie zagadnień dotyczących wydajności Powerschool** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-powerschool-performance-matters-single-sign-on"></a>Konfigurowanie Sign-Onych zagadnień dotyczących wydajności Powerschool

Aby skonfigurować Logowanie jednokrotne po stronie **Powerschool z zagadnieniami** dotyczącymi wydajności, należy wysłać pobrany **kod XML metadanych Federacji** i odpowiednie skopiowane adresy URL z Azure Portal do usługi [Powerschoole zagadnienia dotyczące wydajności](mailto:pmsupport@powerschoo.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do zagadnień związanych z wydajnością Powerschool.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **zagadnienia dotyczące wydajności Powerschool**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **zagadnienia dotyczące wydajności Powerschool**.

    ![Łącze Powerschool wydajności na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-powerschool-performance-matters-test-user"></a>Utwórz użytkownika testowego zagadnień dotyczących wydajności Powerschool

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w kwestiach dotyczących wydajności Powerschool. Współpraca z [zespołem pomocy technicznej w zakresie wydajności Powerschool](mailto:pmsupport@powerschoo.com) do dodawania użytkowników na platformie zagadnień dotyczących wydajności Powerschool. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka zagadnienia dotyczącego wydajności Powerschool w panelu dostępu należy automatycznie zalogować się do zagadnień dotyczących wydajności Powerschool, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)