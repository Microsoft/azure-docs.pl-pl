---
title: 'Samouczek: Integracja usługi Azure Active Directory z sytuałem wstępnym w obszarze rackspace | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a logaciem sytuacowym w obszarze rackspace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 36b398be-2f7e-4ce8-9031-53587299bc4a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 98a160b361e316c87e61855825c2cc36fdbb06d1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870300"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Samouczek: Integracja usługi Azure Active Directory z sytuałem wstępnym w obszarze rackspace

W tym samouczku dowiesz się, jak zintegrować sygnowanie typu SSO w obszarze rackspace z usługą Azure Active Directory (Azure AD).
Integracja sytuowania sytuowania sytuowania w obszarze rackspace z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do rackspace SSO.
* Można włączyć użytkowników do automatycznego logowania do rackspace logowania jednokrotnego (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z sytuałem SSO usługi Rackspace, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego rackspace

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rackspace SSO **obsługuje** sp inicjowane SSO

## <a name="adding-rackspace-sso-from-the-gallery"></a>Dodawanie sso rackspace z galerii

Aby skonfigurować integrację sytuaj sytuatora sytuszowego w obszarze rackspace z usługą Azure AD, należy dodać sytua SSO programu Rackspace z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać sygosk SSO rackspace z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SSO rackspace**, wybierz **opcję SSO rackspace** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![SSO rackspace na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą rackspace logowania na podstawie użytkownika testowego o nazwie **Britta Simon**.
Podczas logowania jednokrotnego w rackspace użytkownicy Rackspace zostaną automatycznie utworzeni przy pierwszym logucie się do portalu Rackspace. 

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w programie Rackspace, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne SSO rackspace](#configure-rackspace-sso-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj mapowanie atrybutów w Panelu sterowania rackspace](#set-up-attribute-mapping-in-the-rackspace-control-panel)** — aby przypisać role rackspace użytkownikom usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą logowania jednokrotnego w programie Rackspace, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **logowania jednokrotnego w obszarze rackspace** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML** przekaż **plik metadanych usługodawcy,** który można pobrać z [adresu URL](https://login.rackspace.com/federate/sp.xml) i wykonaj następujące czynności:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![image](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![image](common/browse-upload-metadata.png)

    d. Po pomyślnym przesłaniu pliku metadanych niezbędne adresy URL są automatycznie wypełniane automatycznie.

    d. W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://login.rackspace.com/federate/`

    ![Informacje o logach SSO i adresach URL rackspace](common/sp-signonurl.png)   

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

Ten plik zostanie przekazany do rackspace, aby wypełnić wymagane ustawienia konfiguracji Federacji tożsamości.

### <a name="configure-rackspace-sso-single-sign-on"></a>Konfigurowanie logowania jednokrotnego logowania sytłego w programie Rackspace

Aby skonfigurować logowanie jednokrotne po stronie **logowania jednokrotnego w obszarze rackspace:**

1. Zobacz dokumentację w [: Dodaj dostawcę tożsamości do Panelu sterowania](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. Doprowadzi Cię przez kroki do:
    1. Tworzenie nowego dostawcy tożsamości
    1. Określ domenę poczty e-mail, której użytkownicy będą używać do identyfikowania firmy podczas logowania.
    1. Przekaż **kod XML metadanych federacji** pobrany wcześniej z panelu sterowania platformy Azure.

Spowoduje to poprawne skonfigurowanie podstawowych ustawień logowania przyłącza sytego wymaganych do nawiązania połączenia z platformą Azure i rackspace.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa** `brittasimon@yourcompanydomain.extension`użytkownika wpisz . Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do logowania jednokrotnego rackspace.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz opcję **SSO obszaru rackspace**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz opcję **SSO rackspace**.

    ![Łącze SSO programu Rackspace na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Konfigurowanie mapowania atrybutów w panelu sterowania Rackspace

Rackspace używa **zasad mapowania atrybutów** do przypisywania ról i grup rackspace do użytkowników jednokrotnego logowania. **Zasady mapowania atrybutów** tłumaczy oświadczenia saml usługi Azure AD na pola konfiguracji użytkownika, którego wymaga rackspace. Więcej dokumentacji można znaleźć w dokumentacji Podstaw [mapowania atrybutów](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)rackspace. Niektóre zagadnienia:

* Jeśli chcesz przypisać różne poziomy dostępu rackspace przy użyciu grup usługi Azure AD, należy włączyć oświadczenia grup w ustawieniach logowania jednokrotnego logowania usługi Azure **Rackspace.** **Zasady mapowania atrybutów** będą następnie używane do dopasowania tych grup do żądanych ról i grup rackspace:

    ![Ustawienia oświadczeń grup](common/sso-groups-claim.png)

* Domyślnie usługa Azure AD wysyła uid grup usługi Azure AD w oświadczeń SAML, w porównaniu do nazwy grupy. Jeśli jednak synchronizujesz lokalną usługę Active Directory z usługą Azure AD, możesz wysłać rzeczywiste nazwy grup:

    ![Ustawienia nazwy oświadczenia grup](common/sso-groups-claims-names.png)

W poniższym przykładzie **zasady mapowania atrybutów** przedstawiono:
1. Ustawianie nazwy użytkownika Rackspace `user.name` na oświadczenie SAML. Można użyć dowolnego oświadczenia, ale najczęściej można ustawić je na pole zawierające adres e-mail użytkownika.
1. Ustawianie ról `admin` rackspace i `billing:admin` użytkownika przez dopasowanie grupy usługi Azure AD, przez nazwę grupy lub uid grupy. *Zasymnajona* `"{0}"` w `roles` polu jest używana i zostanie `remote` zastąpiona wynikami wyrażeń reguły.
1. Używanie `"{D}"` *domyślnego podstawiania* pozwala rackspace pobierać dodatkowe pola SAML, szukając standardowych i dobrze znanych oświadczeń SAML w wymianie SAML.

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
> Upewnij się, że używasz edytora tekstu, który sprawdza poprawność składni YAML podczas edytowania pliku zasad.

Więcej przykładów można znaleźć w [dokumentacji Podstaw mapowania atrybutów rackspace.](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka logującego się do sytua w panelu dostępu należy automatycznie zalogować się do loga logującego jednośliżanie w obszarze rackspace, dla którego skonfigurowano logującą jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Przycisku Sprawdź **poprawność** można również użyć w ustawieniach logowania jednokrotnego logowania **sygmowego w obszarze rackspace:**

   ![Przycisk sprawdzania poprawności sycyny](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

