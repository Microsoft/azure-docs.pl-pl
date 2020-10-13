---
title: 'Samouczek: integracja Azure Active Directory z raportami wydatków N2F | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i raportami wydatków N2F.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 9eee16c1ba207eb35633d7ec1a7167300d40d662
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2020
ms.locfileid: "91941041"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Samouczek: integracja Azure Active Directory z raportami wydatków N2F

W tym samouczku dowiesz się, jak zintegrować raporty wydatków N2F z usługą Azure Active Directory (Azure AD).
Integracja raportów wydatków N2F z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do raportów wydatków N2F.
* Możesz umożliwić użytkownikom automatyczne logowanie do raportów wydatków N2F (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z raportami wydatków N2F, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* N2F — subskrypcja z włączoną obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* N2F — raporty wydatków obsługują Logowanie jednokrotne z użyciem **SP** i **dostawcy tożsamości**

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Dodawanie raportów wydatków N2F z galerii

Aby skonfigurować integrację raportów wydatków N2F z usługą Azure AD, należy dodać raporty wydatków N2F z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać raporty wydatków N2F z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **N2F-Reports**, wybierz pozycję **raporty wydatków N2F** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Raporty wydatków N2F na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą raportów wydatków N2F w oparciu o użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w raportach wydatków N2F.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą raportów wydatków N2F, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. Konfigurowanie rejestracji jednokrotnej w **[raportach wydatków N2F](#configure-n2f---expense-reports-single-sign-on)** — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie raportów wydatków N2F użytkownika testowego](#create-n2f---expense-reports-test-user)** — Aby uzyskać odpowiednik Britta Simon w raportach wydatków N2F, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą raportów wydatków N2F, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **N2Fe raporty dotyczące wydatków** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie zainicjowanym przy użyciu usługi **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja języka SAML** użytkownik nie musi wykonywać żadnych czynności, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![Zrzut ekranu przedstawia stronę logowania opartą na protokole SAML z podstawową konfiguracją SAML.](common/preintegrated.png)

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![Zrzut ekranu przedstawia stronę integracji, na której można dodać Logowanie jednokrotne w usłudze Azure A D.](common/metadata-upload-additional-signon.png)

    W polu tekstowym **adres URL logowania** wpisz adres URL:  `https://www.n2f.com/app/`

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

7. W sekcji **Konfigurowanie zasad moje zasady** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-n2f---expense-reports-single-sign-on"></a>Konfigurowanie N2Fch raportów wydatków — pojedyncze Sign-On

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy raporty wydatków N2F jako administrator.

2. Kliknij pozycję **Ustawienia** , a następnie wybierz pozycję **Ustawienia zaawansowane** z listy rozwijanej.

    ![Zrzut ekranu przedstawia wybrane ustawienia zaawansowane.](./media/n2f-expensereports-tutorial/configure1.png)

3. Wybierz kartę **Ustawienia konta** .

    ![Zrzut ekranu przedstawia wybrane ustawienia konta.](./media/n2f-expensereports-tutorial/configure2.png)

4. Wybierz pozycję **uwierzytelnianie** , a następnie wybierz pozycję **+ Dodaj kartę Metoda uwierzytelniania** .

    ![Zrzut ekranu przedstawia uwierzytelnianie ustawienia konta, w którym można dodać metodę uwierzytelniania.](./media/n2f-expensereports-tutorial/configure3.png)

5. Wybierz pozycję **SAML Microsoft Office 365** jako metodę uwierzytelniania.

    ![Zrzut ekranu przedstawia metodę uwierzytelniania z zaznaczoną opcją SAML Microsoft Office 365.](./media/n2f-expensereports-tutorial/configure4.png)

6. W sekcji **Metoda uwierzytelniania** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia metodę uwierzytelniania, w której można wprowadzić podane wartości.](./media/n2f-expensereports-tutorial/configure5.png)

    a. W polu tekstowym **Identyfikator jednostki** wklej wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.

    b. W polu tekstowym **adres URL metadanych** wklej wartość **adresu URL metadanych federacji aplikacji** , która została skopiowana z Azure Portal.

    c. Kliknij przycisk **Zapisz**.

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

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do raportów wydatków N2F.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **raporty z wydatków N2F**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **raporty dotyczące wydatków N2F**.

    ![Łącze raporty wydatków N2F na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-n2f---expense-reports-test-user"></a>Utwórz użytkownika testowego raportów wydatków N2F

Aby umożliwić użytkownikom usługi Azure AD logowanie się do raportów wydatków N2F, muszą one być obsługiwane w raportach dotyczących wydatków N2F. W przypadku raportów wydatków na N2F, Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy raporty dotyczącej wydatków N2F jako administrator.

2. Kliknij pozycję **Ustawienia** , a następnie wybierz pozycję **Ustawienia zaawansowane** z listy rozwijanej.

    ![Zrzut ekranu przedstawia wybrane ustawienia zaawansowane.](./media/n2f-expensereports-tutorial/configure1.png)

3. Wybierz kartę **Użytkownicy** w lewym panelu nawigacyjnym.

    ![Zrzut ekranu przedstawia wybrane użytkowników.](./media/n2f-expensereports-tutorial/user1.png)

4. Wybierz pozycję **+ nowy użytkownik** karta.

    ![Zrzut ekranu przedstawia opcję Nowy użytkownik.](./media/n2f-expensereports-tutorial/user2.png)

5. W sekcji **użytkownik** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję, w której można wprowadzić podane wartości.](./media/n2f-expensereports-tutorial/user3.png)

    a. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, np. **brittasimon \@ contoso.com**.

    b. W polu tekstowym **imię i nazwisko** Wprowadź imię użytkownika, np. **Britta**.

    c. W polu tekstowym **Name** (Nazwa) wprowadź nazwę użytkownika, na przykład **BrittaSimon**.

    d. Wybierz **rolę, Menedżera bezpośredniego (N + 1)** i **podzielenie** zgodnie z wymaganiami organizacji.

    e. Kliknij przycisk **Weryfikuj i Wyślij zaproszenie**.

    > [!NOTE]
    > Jeśli podczas dodawania użytkownika występują jakieś problemy, skontaktuj się z [zespołem pomocy technicznej N2F — raporty wydatków](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka raporty z wydatków N2F w panelu dostępu należy automatycznie zalogować się do raportów wydatków N2F, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

