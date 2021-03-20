---
title: 'Samouczek: integracja Azure Active Directory z usługą IBM Kenexa Survey Enterprise | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i IBM Kenexa Survey Enterprise.
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
ms.openlocfilehash: faea5fdc7fdab11c630f24dbb8527e5bf198765c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459156"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Samouczek: integracja Azure Active Directory z programem IBM Kenexa Survey Enterprise

W tym samouczku dowiesz się, jak zintegrować usługę IBM Kenexa Survey Enterprise z usługą Azure Active Directory (Azure AD).
Integracja programu IBM Kenexa Surveying z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do programu IBM Kenexa Survey Enterprise.
* Możesz umożliwić użytkownikom automatyczne logowanie do usługi IBM Kenexa Survey Enterprise (Logowanie jednokrotne) za pomocą swoich kont w usłudze Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z programem IBM Kenexa Survey Enterprise, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi IBM Kenexa Survey z włączoną obsługą logowania jednokrotnego w przedsiębiorstwie

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa IBM Kenexa Survey Enterprise obsługuje **dostawcy tożsamości** zainicjowane Logowanie jednokrotne

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Dodawanie programu IBM Kenexa Survey Enterprise z galerii

Aby skonfigurować integrację programu IBM Kenexa Survey Enterprise w usłudze Azure AD, musisz dodać do listy zarządzanych aplikacji SaaS firmę IBM Kenexa Survey Enterprise z galerii.

**Aby dodać firmę IBM Kenexa Survey Enterprise z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **IBM Kenexa Survey Enterprise**, wybierz pozycję **IBM Kenexa Survey Enterprise** z poziomu panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![IBM Kenexa Survey Enterprise na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą programu IBM Kenexa Survey Enterprise na podstawie testowego użytkownika o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie IBM Kenexa Survey Enterprise.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu IBM Kenexa Survey Enterprise, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. Konfigurowanie logowania jednokrotnego w **[przedsiębiorstwie IBM Kenexa](#configure-ibm-kenexa-survey-enterprise-single-sign-on)** — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego IBM Kenexa Survey Enterprise](#create-ibm-kenexa-survey-enterprise-test-user)** — Aby uzyskać odpowiednik Britta Simon w programie IBM Kenexa test Enterprise, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą programu IBM Kenexa Survey Enterprise, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracji z aplikacją **IBM Kenexa Survey Enterprise** aplikacja wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![IBM Kenexa — badanie domeny przedsiębiorstwa i adresów URL Logowanie jednokrotne](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://surveys.kenexa.com/<companycode>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej IBM Kenexa Survey Enterprise Client support](https://www.ibm.com/support/home/?lnk=fcw) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja firmy IBM Kenexa Survey Enterprise oczekuje, że w określonym formacie są wysyłane potwierdzenia języka SAML (Security Assertions Markup Language), co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Wartość żądania identyfikatora użytkownika w odpowiedzi musi być zgodna z IDENTYFIKATORem SSO skonfigurowanym w systemie Kenexa. Aby zamapować odpowiedni identyfikator użytkownika w organizacji jako protokół dostawcy tożsamości (SSO Internet Datagram Protocol), skontaktuj się z [zespołem pomocy technicznej IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

    Domyślnie usługa Azure AD ustawia identyfikator użytkownika jako wartość głównej nazwy użytkownika (UPN). Tę wartość można zmienić na karcie **atrybuty użytkownika** , jak pokazano na poniższym zrzucie ekranu. Integracja działa tylko po poprawnym zakończeniu mapowania.

    ![image (obraz)](common/edit-attribute.png)

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji **Konfigurowanie programu IBM Kenexa Survey Enterprise** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-ibm-kenexa-survey-enterprise-single-sign-on"></a>Konfigurowanie pojedynczej Sign-On w ramach badania IBM Kenexa

Aby skonfigurować Logowanie jednokrotne na stronie programu **IBM Kenexa Survey Enterprise** , musisz wysłać pobrany **certyfikat (base64)** i odpowiednie skopiowane adresy URL z Azure Portal do programu [IBM Kenexa Survey Enterprise Support Team](https://www.ibm.com/support/home/?lnk=fcw). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

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

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do programu IBM Kenexa Survey Enterprise.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **IBM Kenexa Survey Enterprise**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **IBM Kenexa Survey Enterprise**.

    ![Link IBM Kenexa Survey Enterprise na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>Tworzenie użytkownika testowego IBM Kenexa Survey Enterprise

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w programie IBM Kenexa Survey Enterprise.

Aby utworzyć użytkowników w systemie Enterprise Kenexa Surveyer i zmapować dla nich identyfikator logowania jednokrotnego, możesz współpracować z [zespołem pomocy technicznej IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Ta wartość identyfikatora logowania jednokrotnego powinna być również zamapowana na wartość identyfikatora użytkownika z usługi Azure AD. To ustawienie domyślne można zmienić na karcie **atrybut** .

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka IBM Kenexa Survey Enterprise w panelu dostępu należy automatycznie zalogować się do przedsiębiorstwa usługi IBM Kenexa Survey, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)