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
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 9fdcd8a82b901e00e28f0ddd89ba53d9a2e3fbae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952694"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą uniFLOW online

W tym samouczku dowiesz się, jak zintegrować usługę uniFLOW online z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi uniFLOW w trybie online z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi uniFLOW online.
* Zezwól użytkownikom na logowanie się w usłudze uniFLOW online przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* uniFLOW dzierżawy online.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa uniFLOW online obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="add-uniflow-online-from-the-gallery"></a>Dodaj uniFLOW online z galerii

Aby skonfigurować integrację usługi uniFLOW w usłudze Azure AD, musisz dodać uniFLOW online z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **uniFLOW online** w polu wyszukiwania.
1. Wybierz pozycję **UniFLOW online** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-uniflow-online"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla usługi uniFLOW online

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą uniFLOW online przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze uniFLOW online.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD w usłudze uniFLOW online, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
   1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
   1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne w usłudze UniFLOW online](#configure-uniflow-online-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Zaloguj się do usługi UniFLOW online przy użyciu utworzonego użytkownika testowego,](#sign-in-to-uniflow-online-using-the-created-test-user)** aby przetestować Logowanie użytkownika po stronie aplikacji.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **UniFLOW online** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL przy użyciu jednego z następujących wzorców:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL przy użyciu jednego z następujących wzorców:

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
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi uniFLOW w trybie online.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **UniFLOW online**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

> [!NOTE]
> Aby zezwolić wszystkim użytkownikom na dostęp do aplikacji bez przypisywania ręcznego, przejdź do sekcji **Zarządzanie** i wybierz pozycję **Właściwości**. Następnie zmień parametr **wymagany przez przypisanie użytkownika** na wartość **nie**.

## <a name="configure-uniflow-online-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze uniFLOW online

1. W innym oknie przeglądarki sieci Web Zaloguj się, aby uniFLOW witrynę internetową online jako administrator.

1. W okienku nawigacji po lewej stronie wybierz pozycję Karta **użytkownika** .

    ![Zrzut ekranu przedstawia użytkownika wybranego w witrynie uniFLOW online.](./media/uniflow-online-tutorial/configure-1.png)

1. Kliknij pozycję **dostawcy tożsamości**.

    ![Zrzut ekranu przedstawia wybrane dostawcy tożsamości.](./media/uniflow-online-tutorial/configure-2.png)

1. Kliknij pozycję **Dodaj dostawcę tożsamości**.

    ![Zrzut ekranu pokazuje wybrany dostawca Dodaj dostawcę tożsamości.](./media/uniflow-online-tutorial/configure-3.png)

1. W sekcji **Dodawanie dostawcy tożsamości** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję Dodawanie dostawcy tożsamości, w której można wprowadzić podane wartości.](./media/uniflow-online-tutorial/configure-4.png)

    a. Wprowadź nazwę wyświetlaną ex: *AZUREAD SSO*.

    b. W polu **Typ dostawcy** wybierz opcję **protokołu WS-pokarmowego** z listy rozwijanej.

    c. Dla **typu protokołu WS-karmionego** wybierz opcję **Azure Active Directory** z listy rozwijanej.

    d. Kliknij pozycję **Zapisz**.

1. Na karcie **Ogólne** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia kartę Ogólne, w której można wprowadzić podane wartości.](./media/uniflow-online-tutorial/configure-5.png)

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

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do usługi uniFLOW online, na którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania do usługi uniFLOW online i zainicjuj tam przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka uniFLOW online w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania do usługi uniFLOW online. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi uniFLOW online można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).