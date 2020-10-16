---
title: 'Samouczek: integracja Azure Active Directory z logowaniem jednokrotnym w usłudze Rackspace | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Rackspace Logowanie jednokrotne.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 991201429bdc09a03ddc46c5beb9d0f4d6744ed5
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108630"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Samouczek: integracja Azure Active Directory z logowaniem jednokrotnym w usłudze Rackspace

W tym samouczku dowiesz się, jak zintegrować Logowanie jednokrotne w usłudze Rackspace z usługą Azure Active Directory (Azure AD).
Integracja z logowaniem jednokrotnym w usłudze Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do rejestracji jednokrotnej Rackspace.
* Możesz włączyć automatyczne logowanie użytkowników, aby Rackspace Logowanie jednokrotne (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z logowaniem jednokrotnym w usłudze Rackspace, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w usłudze Rackspace

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa SSO Rackspace obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-rackspace-sso-from-the-gallery"></a>Dodawanie logowania jednokrotnego Rackspace z galerii

Aby skonfigurować integrację logowania jednokrotnego Rackspace z usługą Azure AD, musisz dodać Logowanie jednokrotne Rackspace z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Logowanie jednokrotne Rackspace z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **RACKSPACE SSO**, wybierz pozycję **Rackspace SSO** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![Rackspace Logowanie jednokrotne na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą logowania jednoRackspaceowego na podstawie użytkownika testowego o nazwie **Britta Simon**.
W przypadku korzystania z logowania jednokrotnego za pomocą usługi Rackspace użytkownicy Rackspace zostaną automatycznie utworzeni przy pierwszym logowaniu się do portalu Rackspace. 

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Rackspace SSO, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie JEDNOkrotne w usłudze Rackspace](#configure-rackspace-sso-single-sign-on)** , aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj Mapowanie atrybutów w panelu sterowania Rackspace,](#set-up-attribute-mapping-in-the-rackspace-control-panel)** aby przypisać role Rackspace do użytkowników usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Rackspace SSO, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Rackspace SSO** wybierz pozycję **Logowanie**jednokrotne.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja języka SAML** Przekaż **plik metadanych dostawcy usług** , który można pobrać z [adresu URL](https://login.rackspace.com/federate/sp.xml) , i wykonaj następujące czynności:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML przy użyciu linku Przekaż plik metadanych.](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![Zrzut ekranu przedstawia okno dialogowe, w którym można wybrać i przekazać plik.](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych niezbędne adresy URL są automatycznie wypełniane.

    d. W polu tekstowym **adres URL logowania** wpisz adres URL: `https://login.rackspace.com/federate/`

    ![Rackspace informacje logowania jednokrotnego i adresów URL logowania jednokrotnego](common/sp-signonurl.png)   

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

Ten plik zostanie przekazany do Rackspace w celu wypełnienia wymaganych ustawień konfiguracji federacji tożsamości.

### <a name="configure-rackspace-sso-single-sign-on"></a>Skonfiguruj jednoSign-Onową rejestrację jednokrotną Rackspace

Aby skonfigurować Logowanie jednokrotne na stronie logowania **JEDNOkrotnego Rackspace** :

1. Zapoznaj się z dokumentacją w temacie [Dodawanie dostawcy tożsamości do panelu sterowania](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. Przeprowadzi Cię przez kroki, aby:
    1. Tworzenie nowego dostawcy tożsamości
    1. Określ domenę poczty e-mail, która będzie używana przez użytkowników do identyfikowania Twojej firmy podczas logowania.
    1. Przekaż **plik XML metadanych Federacji** , który został wcześniej pobrany z panelu sterowania platformy Azure.

Spowoduje to poprawne skonfigurowanie podstawowych ustawień logowania jednokrotnego wymaganych przez platformę Azure i Rackspace w celu nawiązania połączenia.

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

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Rackspace logowania jednokrotnego.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Rackspace Logowanie jednokrotne**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **RACKSPACE SSO**.

    ![Link rejestracji jednokrotnej Rackspace na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Konfigurowanie mapowania atrybutów w panelu sterowania Rackspace

Rackspace używa **zasad mapowania atrybutów** do przypisywania ról i grup Rackspace do użytkowników korzystających z logowania jednokrotnego. **Zasady mapowania atrybutów** tłumaczą oświadczenia SAML usługi Azure AD w pola konfiguracji użytkownika Rackspace wymagane. Więcej dokumentacji można znaleźć w dokumentacji dotyczącej [mapowania atrybutów](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)Rackspace. Kwestie do rozważenia:

* Jeśli chcesz przypisać różne poziomy dostępu Rackspace przy użyciu grup usługi Azure AD, musisz włączyć w ustawieniach logowania jednokrotnego usługi Azure **Rackspace** dane w obszarze Logowanie za pomocą logowania jednostronnego. **Zasady mapowania atrybutów** zostaną następnie użyte do dopasowania do tych grup do żądanych ról i grup Rackspace:

    ![Ustawienia roszczeń grup](common/sso-groups-claim.png)

* Domyślnie usługa Azure AD wysyła identyfikator UID grup usługi Azure AD w ramach żądania SAML, a nie nazwę grupy. Jednak w przypadku synchronizowania Active Directory lokalnego z usługą Azure AD istnieje możliwość wysyłania rzeczywistych nazw grup:

    ![Ustawienia nazw roszczeń grup](common/sso-groups-claims-names.png)

Następujące przykładowe **zasady mapowania atrybutów** przedstawiają:
1. Ustawianie nazwy użytkownika Rackspace na wartość Claim języka `user.name` SAML. Można użyć dowolnego z tych roszczeń, ale jest to najbardziej powszechne, aby ustawić to pole zawierające adres e-mail użytkownika.
1. Ustawianie ról Rackspace `admin` i `billing:admin` dla użytkownika przez dopasowanie grupy usługi Azure AD według nazwy grupy lub identyfikatora UID grupy. *Podstawienie* `"{0}"` w `roles` polu jest używane i zostanie zastąpione przez wyniki `remote` wyrażeń reguł.
1. Używanie `"{D}"` *podstawienia domyślnego* do zezwalania Rackspace na pobieranie dodatkowych pól SAML przez szukanie standardowych i dobrze znanych oświadczeń SAML w wymianie SAML.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> Upewnij się, że używasz edytora tekstów, który sprawdza składnię YAML podczas edytowania pliku zasad.

Więcej przykładów można znaleźć w dokumentacji dotyczącej [mapowania atrybutów](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/) Rackspace.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka rejestracji jednokrotnej Rackspace w panelu dostępu należy automatycznie zalogować się do logowania jednokrotnego Rackspace, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Możesz również użyć przycisku **Weryfikuj** w ustawieniach logowania jednokrotnego w usłudze **Rackspace** :

   ![Przycisk walidacji logowania jednokrotnego](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

