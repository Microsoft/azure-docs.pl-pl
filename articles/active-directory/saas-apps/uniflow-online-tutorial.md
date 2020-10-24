---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą uniFLOW online | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i uniFLOW online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: jeedes
ms.openlocfilehash: fc1317ffce7c958f092ad01feff473e123581439
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521768"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą uniFLOW online

W tym samouczku dowiesz się, jak zintegrować usługę uniFLOW online z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi uniFLOW w trybie online z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi uniFLOW online.
* Zezwól użytkownikom na logowanie się w usłudze uniFLOW online przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* uniFLOW dzierżawy online.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa uniFLOW online obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-uniflow-online-from-the-gallery"></a>Dodawanie uniFLOW online z galerii

Aby skonfigurować integrację usługi uniFLOW w usłudze Azure AD, musisz dodać uniFLOW online z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **uniFLOW online** w polu wyszukiwania.
1. Wybierz pozycję **UniFLOW online** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-uniflow-online"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla usługi uniFLOW online

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą uniFLOW online przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze uniFLOW online.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD w usłudze uniFLOW online, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
   1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
   1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne w usłudze UniFLOW online](#configure-uniflow-online-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Zaloguj się do usługi UniFLOW online przy użyciu utworzonego użytkownika testowego,](#sign-in-to-uniflow-online-using-the-created-test-user)** aby przetestować Logowanie użytkownika po stronie aplikacji.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **uniFLOW online** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: 

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: 

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej usługi UniFLOW online](mailto:support@nt-ware.com) , aby uzyskać te wartości. Można również odwołać się do wzorców przedstawionych w sekcji podstawowe informacje o **konfiguracji SAML** w Azure Portal lub zajrzeć do adresu URL odpowiedzi wyświetlanego w dzierżawie usługi uniFLOW online.

1. aplikacja online uniFLOW oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych uniFLOW aplikacja online oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa |  Atrybut źródłowy|
    | -----------| --------------- |
    | displayname | user.displayname |
    | pseudonim | User. nazwy pospolitej onpremisessamaccountname |

   > [!NOTE]
   > Ten `user.onpremisessamaccountname` atrybut będzie zawierać wartość tylko wtedy, gdy użytkownicy usługi Azure AD są synchronizowani z lokalnego Active Directory systemu Windows.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi uniFLOW w trybie online.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **UniFLOW online**.
1. Na stronie Przegląd aplikacji przejdź do sekcji **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

   ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

> [!NOTE]
> Aby zezwolić wszystkim użytkownikom na dostęp do aplikacji bez przypisywania ręcznego, przejdź do sekcji **Zarządzanie** i wybierz pozycję **Właściwości**. Następnie zmień parametr **wymagany przez przypisanie użytkownika** na wartość **nie**.

## <a name="configure-uniflow-online-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze uniFLOW online

1. W innym oknie przeglądarki sieci Web Zaloguj się, aby uniFLOW witrynę internetową online jako administrator.

1. W okienku nawigacji po lewej stronie wybierz pozycję Karta **użytkownika** .

    ![Zrzut ekranu przedstawia użytkownika wybranego w witrynie uniFLOW online.](./media/uniflow-online-tutorial/configure1.png)

1. Kliknij pozycję **dostawcy tożsamości**.

    ![Zrzut ekranu przedstawia wybrane dostawcy tożsamości.](./media/uniflow-online-tutorial/configure2.png)

1. Kliknij pozycję **Dodaj dostawcę tożsamości**.

    ![Zrzut ekranu pokazuje wybrany dostawca Dodaj dostawcę tożsamości.](./media/uniflow-online-tutorial/configure3.png)

1. W sekcji **Dodawanie dostawcy tożsamości** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję Dodawanie dostawcy tożsamości, w której można wprowadzić podane wartości.](./media/uniflow-online-tutorial/configure4.png)

    a. Wprowadź nazwę wyświetlaną ex: *AZUREAD SSO*.

    b. W polu **Typ dostawcy**wybierz opcję **protokołu WS-pokarmowego** z listy rozwijanej.

    c. Dla **typu protokołu WS-karmionego**wybierz opcję **Azure Active Directory** z listy rozwijanej.

    d. Kliknij pozycję **Zapisz**.

1. Na karcie **Ogólne** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia kartę Ogólne, w której można wprowadzić podane wartości.](./media/uniflow-online-tutorial/configure5.png)

    a. Wprowadź nazwę wyświetlaną ex: *AZUREAD SSO*.

    b. Wybierz opcję **adres URL** dla **metadanych Federacji usług ADFS**.

    c. W polu tekstowym **adres URL metadanych Federacji** wklej wartość **adresu URL metadanych federacji aplikacji** , która została skopiowana z Azure Portal.

    d. Wybierz pozycję **dostawca tożsamości** jako **włączony**.

    e. Wybierz pozycję **Automatyczna rejestracja użytkowników** jako **aktywowana**.

    f. Kliknij pozycję **Zapisz**.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Zaloguj się do usługi uniFLOW online za pomocą utworzonego użytkownika testowego

1. W innym oknie przeglądarki sieci Web przejdź do adresu URL usługi uniFLOW online dla Twojej dzierżawy.

1. Wybierz wcześniej utworzonego dostawcę tożsamości, aby zalogować się za pomocą swojego wystąpienia usługi Azure AD.

1. Zaloguj się przy użyciu użytkownika testowego.

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj usługę uniFLOW online z usługą Azure AD](https://aad.portal.azure.com/)