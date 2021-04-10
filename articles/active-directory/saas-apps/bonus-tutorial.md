---
title: 'Samouczek: integracja Azure Active Directory z dodatkowymi usługami | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i dodatkową.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: e93d03ac9ec404a18b480a0cd1bfe289944b60d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689117"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Samouczek: integracja Azure Active Directory z dodatkowymi

W tym samouczku dowiesz się, jak zintegrować się z usługą Azure Active Directory (Azure AD).
Integracja z usługą Azure AD zapewnia dodatkowe korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do dodatkowych uprawnień.
* Możesz umożliwić użytkownikom automatyczne logowanie się, aby uzyskać dodatkową możliwość (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z dodatkowymi potrzebami, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Zapewnia dodatkową obsługę **dostawcy tożsamości** zainicjowane Logowanie jednokrotne

## <a name="adding-bonusly-from-the-gallery"></a>Dodatkowe Dodawanie z galerii

Aby skonfigurować integrację z usługą Azure AD, należy dodać ją z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać dodatkową z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **premię** **, wybierz pozycję** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Dodatkowa lista wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD za pomocą użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie samoobsługowego logowania](#configure-bonusly-single-sign-on)** jednokrotnego — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. Utwórz nieaktualnego **[użytkownika testowego](#create-bonusly-test-user)** , aby uzyskać premię za usługę Britta Simon, która jest powiązana z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD Logowanie jednokrotne, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z **aplikacjami wybierz** opcję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Dodatkowe informacje o rejestracji jednokrotnej w domenach i adresach URL](common/idp-reply.png)

    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:  `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Skontaktuj się z [dodatkowym zespołem pomocy technicznej](https://bonus.ly/contact) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

6. W sekcji **Certyfikat podpisywania SAML** skopiuj wartość **ODCISK PALCA** i zapisz go na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

7. W sekcji **Skonfiguruj premię** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-bonusly-single-sign-on&quot;></a>Konfigurowanie bezSign-Onych pojedynczych

1. W innym oknie przeglądarki Zaloguj się do dzierżawy z **premią** .

1. Na pasku narzędzi u góry kliknij pozycję **Ustawienia** , a następnie wybierz pozycję **integracje i aplikacje**.

    ![Sekcja z dodatkowymi kwestiami społecznościowymi](./media/bonus-tutorial/ic773686.png &quot;Bonusly")
1. W obszarze **Logowanie jednokrotne** wybierz pozycję **SAML**.

1. Na stronie okno dialogowe **SAML** wykonaj następujące czynności:

    ![Strona okna dialogowego z dodatkowymi językiem SAML](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. W polu tekstowym **adres URL elementu docelowego logowania jednokrotnego dostawcy tożsamości** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.

    b. W polu tekstowym **adres URL logowania dostawcy tożsamości** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.

    c. W polu tekstowym **wystawcy dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD**, która została skopiowana z Azure Portal.
    
    d. Wklej wartość **odcisku palca** skopiowaną z Azure Portal do pola tekstowego **odcisku palca certyfikatu** .
    
    e. Kliknij pozycję **Zapisz**.

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

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **dodatkowe**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz opcję **dodatkowe**.

    ![Link dodatkowy na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-bonusly-test-user&quot;></a>Utwórz użytkownika z dodatkowym testem

Aby umożliwić użytkownikom usługi Azure AD logowanie się w celu uzyskania dodatkowych potrzeb, muszą one zostać zainicjowane w sposób dodatkowy. W przypadku zainicjowania obsługi administracyjnej jest to zadanie ręczne.

> [!NOTE]
> Można korzystać z innych narzędzi do tworzenia kont użytkowników i interfejsów API zapewniających dodatkową obsługę kont użytkowników usługi Azure AD. 

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. W oknie przeglądarki sieci Web Zaloguj się do dzierżawy z premią.

1. Kliknij polecenie **Ustawienia**.

    ![Ustawienia](./media/bonus-tutorial/ic781041.png &quot;Ustawienia")

1. Kliknij kartę **Użytkownicy i premie** .

    ![Użytkownicy i premie](./media/bonus-tutorial/ic781042.png "Użytkownicy i premie")

1. Kliknij pozycję **Zarządzaj użytkownikami**.

    ![Zarządzanie użytkownikami](./media/bonus-tutorial/ic781043.png "Zarządzanie użytkownikami")

1. Kliknij pozycję **Dodaj użytkownika**.

    ![Zrzut ekranu przedstawia zarządzanie użytkownikami, w których można wybrać opcję Dodaj użytkownika.](./media/bonus-tutorial/ic781044.png "Dodaj użytkownika")

1. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia okno dialogowe Dodawanie użytkownika, w którym można wprowadzić te informacje.](./media/bonus-tutorial/ic781045.png "Dodaj użytkownika")  

    a. W polu tekstowym **imię i nazwisko** Wprowadź imię użytkownika, np. **Britta**.

    b. W polu tekstowym **Last name (Nazwisko)** wprowadź nazwisko użytkownika, na przykład **Simon**.

    c. W polu tekstowym **E-mail** wprowadź adres e-mail użytkownika, na przykład `brittasimon@contoso.com`.

    d. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Posiadacz konta usługi Azure AD otrzymuje wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim staną się aktywne.  

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka bonus w panelu dostępu należy automatycznie zalogować się do programu, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)