---
title: 'Samouczek: integracja Azure Active Directory z usługą Hornbill | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Hornbill.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 4131f1d0ef36eb49641ac184cd3f72fb719ea90e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92443429"
---
# <a name="tutorial-azure-active-directory-integration-with-hornbill"></a>Samouczek: integracja Azure Active Directory z usługą Hornbill

Z tego samouczka dowiesz się, jak zintegrować aplikację Hornbill z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Hornbill z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Hornbill.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Hornbill (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Hornbill, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Hornbill z obsługą logowania jednokrotnego

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Hornbill obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**
* Aplikacja Hornbill obsługuje aprowizację użytkowników **Just In Time**

## <a name="adding-hornbill-from-the-gallery"></a>Dodawanie aplikacji Hornbill z galerii

Aby skonfigurować integrację aplikacji Hornbill z usługą Azure AD, musisz dodać aplikację Hornbill z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Hornbill z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Hornbill**, wybierz pozycję **Hornbill** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Hornbill na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Hornbill, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Hornbill.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Hornbill, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Hornbill](#configure-hornbill-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego w aplikacji Hornbill](#create-hornbill-test-user)** — aby w aplikacji Hornbill utworzyć odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Hornbill, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Hornbill** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena i adresy URL aplikacji Hornbill — informacje dotyczące logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/lib/saml/auth/simplesaml/module.php/saml/sp/metadata.php/saml`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Hornbill](https://www.hornbill.com/support/?request/), aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-hornbill-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji Hornbill

1. W innym oknie przeglądarki internetowej zaloguj się do aplikacji Hornbill jako administrator zabezpieczeń.

2. Na stronie głównej kliknij pozycję **System**.

    ![System aplikacji Hornbill](./media/hornbill-tutorial/tutorial_hornbill_system.png)

3. Przejdź do obszaru **Security** (Zabezpieczenia).

    ![Zabezpieczenia aplikacji Hornbill](./media/hornbill-tutorial/tutorial_hornbill_security.png)

4. Kliknij pozycję **SSO Profiles** (Profile logowania jednokrotnego).

    ![Logowanie jednokrotne aplikacji Hornbill](./media/hornbill-tutorial/tutorial_hornbill_sso.png)

5. W prawej części strony kliknij pozycję **Add logo** (Dodaj logo).

    ![Dodawanie w aplikacji Hornbill](./media/hornbill-tutorial/tutorial_hornbill_addlogo.png)

6. Na pasku **Profile Details** (Szczegóły profilu) kliknij pozycję **Import SAML Meta logo** (Importuj meta logo SAML).

    ![Logo aplikacji Hornbill](./media/hornbill-tutorial/tutorial_hornbill_logo.png)

7. Na wyskakującej stronie w polu tekstowym **URL** wklej **adres URL metadanych federacji aplikacji** skopiowany z witryny Azure Portal, a następnie kliknij pozycję **Process** (Przetwórz).

    ![Przetwarzanie w aplikacji Hornbill](./media/hornbill-tutorial/tutorial_hornbill_process.png)

8. Po kliknięciu przycisku przetwarzania wartości w sekcji **Profile Details** (Szczegóły profilu) zostaną wypełnione automatycznie.

    ![Aplikacja Hornbill — strona 1](./media/hornbill-tutorial/tutorial_hornbill_ssopage.png)

    ![Aplikacja Hornbill — strona 2](./media/hornbill-tutorial/tutorial_hornbill_ssopage1.png)

    ![Aplikacja Hornbill — strona 3](./media/hornbill-tutorial/tutorial_hornbill_ssopage2.png)

9. Kliknij przycisk **Zapisz zmiany**.

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Hornbill.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **Hornbill**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Hornbill**.

    ![Link Hornbill na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-hornbill-test-user"></a>Tworzenie użytkownika testowego aplikacji Hornbill

W tej sekcji w aplikacji Hornbill jest tworzony użytkownik o nazwie Britta Simon. Aplikacja Hornbill obsługuje aprowizację użytkowników typu just-in-time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Hornbill, zostanie utworzony po uwierzytelnieniu.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem obsługi klienta Hornbill](https://www.hornbill.com/support/?request/).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Hornbill w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Hornbill, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)