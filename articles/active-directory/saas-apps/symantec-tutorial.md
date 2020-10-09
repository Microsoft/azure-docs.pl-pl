---
title: 'Samouczek: integracja Azure Active Directory z usługą zabezpieczeń internetowych firmy Symantec | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Symantec Web Security Service (WSS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: 7e2087fdd5568156a96645b25cb105cc5f3ca6b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88544948"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Samouczek: integracja Azure Active Directory z usługą zabezpieczeń internetowych firmy Symantec (WSS)

Z tego samouczka dowiesz się, jak zintegrować konto usługi Symantec Web Security Service (WSS) z kontem usługi Azure Active Directory (Azure AD), tak aby usługa WSS mogła uwierzytelnić użytkownika końcowego aprowizowanego w usłudze Azure AD przy użyciu uwierzytelniania SAML i wymuszać reguły zasad na poziomie użytkownika lub grupy.

Integracja usługi Symantec Web Security Service (WSS) z usługą Azure AD zapewnia następujące korzyści:

- Zarządzanie wszystkimi użytkownikami końcowymi i grupami używanymi przez konto usługi WSS z poziomu portalu usługi Azure AD.

- Zezwolenie użytkownikom końcowym na uwierzytelnianie w usłudze WSS przy użyciu ich poświadczeń usługi Azure AD.

- Włączenie wymuszania reguł zasad na poziomie użytkownika i grupy na koncie usługi WSS.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Symantec Web Security Service (WSS), potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi Symantec Web Security Service (WSS) z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Symantec Web Security Service (WSS) obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Dodawanie usługi Symantec Web Security Service (WSS) z galerii

Aby skonfigurować integrację usługi Symantec Web Security Service (WSS) z usługą Azure AD, musisz dodać usługę Symantec Web Security Service (WSS) z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać usługę Symantec Web Security Service (WSS) z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Symantec Web Security Service (WSS)**, wybierz pozycję **Symantec Web Security Service (WSS)** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Usługa Symantec Web Security Service (WSS) na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z usługą Symantec Web Security Service (WSS), korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem usługi Symantec Web Security Service (WSS).

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z usługą Symantec Web Security Service (WSS), należy ukończyć poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **Konfigurowanie logowania jednokrotnego w usłudze Symantec Web Security Service (WSS)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego usługi Symantec Web Security Service (WSS)](#create-symantec-web-security-service-wss-test-user)** — aby mieć w usłudze Symantec Web Security Service (WSS) odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z usługą Symantec Web Security Service (WSS), wykonaj następujące kroki:

1. W [witrynie Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Symantec Web Security Service (WSS)** wybierz opcję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W oknie dialogowym **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Domena i adresy URL usługi Symantec Web Security Service (WSS) — informacje dotyczące logowania jednokrotnego](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Skontaktuj się z [zespołem pomocy technicznej klienta usługi Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us), jeśli wartości **identyfikatora** i **adresu URL odpowiedzi** z jakiegoś powodu nie działają. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w usłudze Symantec Web Security Service (WSS)

Aby skonfigurować logowanie jednokrotne po stronie usługi Symantec Web Security Service (WSS), zapoznaj się z dokumentacją online usługi WSS. Pobrany **plik XML metadanych federacji** musi zostać zaimportowany do portalu usługi WSS. Jeśli potrzebujesz pomocy z konfiguracją w portalu usługi WSS, skontaktuj się z [zespołem pomocy technicznej usługi Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us).

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

W tej sekcji włączysz możliwość logowania jednokrotnego na platformie Azure dla użytkownika Britta Simon, udzielając dostępu do usługi Symantec Web Security Service (WSS).

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Symantec Web Security Service (WSS)**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz **Symantec Web Security Service (WSS)** i wybierz odpowiednią pozycję.

    ![Link usługi Symantec Web Security Service (WSS) na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Tworzenie użytkownika testowego usługi Symantec Web Security Service (WSS)

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usłudze Symantec Web Security Service (WSS). Odpowiednią nazwę użytkownika końcowego można utworzyć ręcznie w portalu usługi WSS lub poczekać, aż użytkownicy/grupy aprowizowani w usłudze Azure AD zostaną zsynchronizowani z portalem usługi WSS (około 15 minut). Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. Publiczny adres IP komputera użytkownika końcowego, który będzie używany do przeglądania witryn internetowych, musi być także aprowizowany w portalu usługi Symantec Web Security Service (WSS).

> [!NOTE]
> Kliknij [tutaj](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1), aby uzyskać publiczny adres IP swojego komputera.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz funkcję logowania jednokrotnego, mając skonfigurowane konto usługi WSS, aby używać usługi Azure AD na potrzeby uwierzytelniania SAML.

Po skonfigurowaniu przeglądarki internetowej w celu kierowania za pośrednictwem serwera proxy ruchu do usługi WSS, kiedy otworzysz przeglądarkę internetową i spróbujesz przejść do witryny, nastąpi przekierowanie do strony logowania platformy Azure. Wprowadź poświadczenia testowego użytkownika końcowego, który został aprowizowany w usłudze Azure AD (czyli BrittaSimon), oraz skojarzone hasło. Po uwierzytelnieniu będzie można przejść do wybranej witryny internetowej. Jeśli utworzysz regułę zasad po stronie usługi WSS, aby uniemożliwić użytkownikowi BrittaSimon przejście do określonej witryny, to gdy spróbujesz przejść do tej witryny jako użytkownik BrittaSimon, powinna zostać wyświetlona strona blokowania usługi WSS.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

