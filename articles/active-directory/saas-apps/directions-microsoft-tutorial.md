---
title: 'Samouczek: integracja Azure Active Directory z instrukcjami w firmie Microsoft | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją Directions on Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e0c8986f-2acd-418d-a306-437abc44b640
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e6928a54692e52960c80e5279c2868d9b7026c4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73158422"
---
# <a name="tutorial-azure-active-directory-integration-with-directions-on-microsoft"></a>Samouczek: integracja Azure Active Directory z instrukcjami w firmie Microsoft

Z tego samouczka dowiesz się, jak zintegrować aplikację Directions on Microsoft z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Directions on Microsoft z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować dostęp do aplikacji Directions on Microsoft za pomocą usługi Azure AD.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Directions on Microsoft (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Directions on Microsoft są potrzebne następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Directions on Microsoft z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Directions on Microsoft obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

## <a name="adding-directions-on-microsoft-from-the-gallery"></a>Dodawanie aplikacji Directions on Microsoft z galerii

Aby skonfigurować integrację aplikacji Directions on Microsoft z usługą Azure AD, należy dodać aplikację Directions on Microsoft z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Directions on Microsoft z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Directions on Microsoft**, wybierz pozycję **Directions on Microsoft** na panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Directions on Microsoft na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Directions on Microsoft, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Directions on Microsoft.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Directions on Microsoft, należy utworzyć poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Directions on Microsoft](#configure-directions-on-microsoft-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Directions on Microsoft](#create-directions-on-microsoft-test-user)** — aby mieć w aplikacji Directions on Microsoft odpowiednik użytkownika Britta Simon, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Directions on Microsoft, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Directions on Microsoft** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji Directions on Microsoft](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: 

    |  |
    | --- |
    | `https://www.directionsonmicrosoft.com/user/login` |
    | `https://<subdomain>.devcloud.acquia-sites.com/<companyname>` |

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: 
    
    |  |
    | --- |
    | `https://rhelmdirectionsonmicrosoftcomtest.devcloud.acquia-sites.com/simplesaml/<companyname>` |
    | `https://www.directionsonmicrosoft.com/simplesaml/<companyname>` |

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Directions on Microsoft](mailto:service@DirectionsOnMicrosoft.com). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie aplikacji Directions on Microsoft** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-directions-on-microsoft-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji Directions on Microsoft

Aby skonfigurować logowanie jednokrotne po stronie aplikacji **Directions on Microsoft**, musisz wysłać pobrany **kod XML metadanych** do [zespołu pomocy technicznej aplikacji Directions on Microsoft](mailto:service@DirectionsOnMicrosoft.com). Aby umożliwić zespołowi pomocy technicznej aplikacji Directions on Microsoft zlokalizowanie Twojego członkostwa w witrynie federacyjnej, dołącz do wiadomości e-mail informacje o firmie.
    
>[!NOTE]
>Logowanie jednokrotne dla aplikacji Directions on Microsoft musi zostać włączone przez [zespół pomocy technicznej klienta aplikacji Directions on Microsoft](mailto:service@DirectionsOnMicrosoft.com). Kiedy logowanie jednokrotne zostanie włączone, otrzymasz powiadomienie.

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Directions on Microsoft.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **Directions on Microsoft**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Directions on Microsoft**.

    ![Link aplikacji Directions on Microsoft na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-directions-on-microsoft-test-user"></a>Tworzenie użytkownika testowego aplikacji Directions on Microsoft

Aby skonfigurować aprowizację użytkownika w aplikacji Directions on Microsoft, nie musisz wykonywać żadnej akcji.  

Kiedy przypisany użytkownik spróbuje zalogować się do aplikacji Directions on Microsoft za pomocą panelu dostępu, aplikacja Directions on Microsoft sprawdzi, czy ten użytkownik istnieje. Jeśli konto użytkownika nie będzie jeszcze dostępne, zostanie utworzone automatycznie przez aplikację Directions on Microsoft.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Directions on Microsoft na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Directions on Microsoft, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

