---
title: 'Samouczek: integracja Azure Active Directory z usługą EverBridge | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i EverBridge.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 6e281931eb4646e09bb9aa3226ed7d0735c84e3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101643783"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Samouczek: integracja Azure Active Directory z usługą EverBridge

W tym samouczku dowiesz się, jak zintegrować usługę EverBridge z usługą Azure Active Directory (Azure AD).
Po zintegrowaniu usługi EverBridge z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do EverBridge.
* Zezwól użytkownikom na automatyczne logowanie do EverBridge przy użyciu kont usługi Azure AD. Ta kontrola dostępu jest nazywana logowaniem jednokrotnym (SSO).
* Zarządzaj kontami w jednej centralnej lokalizacji przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą EverBridge, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja EverBridge, która korzysta z rejestracji jednokrotnej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa EverBridge obsługuje logowanie jednokrotne inicjowane przez usługę dostawcy tożsamości.

## <a name="add-everbridge-from-the-gallery"></a>Dodaj EverBridge z galerii

Aby skonfigurować integrację programu EverBridge z usługą Azure AD, musisz dodać EverBridge z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **EverBridge** w polu wyszukiwania.
1. Wybierz pozycję **EverBridge** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-everbridge"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla EverBridge

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą EverBridge przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w EverBridge.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą EverBridge, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-everbridge-sso)** w usłudze EverBridge, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego EverBridge](#create-everbridge-test-user)** , aby dysponować odpowiednikiem B. Simon w EverBridge, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **EverBridge** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

    >[!NOTE]
    >Skonfiguruj aplikację jako portal Menedżera *lub* jako portal elementu członkowskiego na Azure Portal i w portalu EverBridge.

4. Aby skonfigurować aplikację **EverBridge** jako **Portal Menedżera EverBridge**, w sekcji **podstawowe konfiguracje języka SAML** wykonaj następujące czynności:

    ![EverBridge domenę i adresy URL Logowanie jednokrotne](common/idp-intiated.png)

    a. W polu **Identyfikator** wprowadź adres URL, który następuje po wzorcu.
    `https://sso.everbridge.net/<API_Name>`

    b. W polu **adres URL odpowiedzi** wprowadź adres URL zgodny ze wzorcem.
    `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > To nie są rzeczywiste wartości. Zaktualizuj te wartości za pomocą wartości rzeczywiste identyfikator i adres URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej EverBridge](mailto:support@everbridge.com). Można również odwołać się do wzorców przedstawionych w sekcji podstawowe informacje o **konfiguracji SAML** w Azure Portal.

5. Aby skonfigurować aplikację **EverBridge** jako **Portal elementu członkowskiego EverBridge**, w sekcji **podstawowe konfiguracje języka SAML** wykonaj następujące czynności:

  * Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez dostawcy tożsamości, wykonaj następujące kroki:

     ![EverBridge domenę i adresy URL logowania jednokrotnego dla trybu zainicjowane przez dostawcy tożsamości](common/idp-intiated.png)

    a. W polu **Identyfikator** wprowadź adres URL, który następuje po wzorcu `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. W polu **adres URL odpowiedzi** wprowadź adres URL zgodny ze wzorcem. `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Jeśli chcesz skonfigurować aplikację w trybie inicjowania programu SP, wybierz opcję **Ustaw dodatkowe adresy URL** i wykonaj następujące czynności:

     ![EverBridge domenę i adresy URL logowania jednokrotnego w trybie zainicjowanym przez program SP](common/both-signonurl.png)

     a. W polu **adres URL logowania** wprowadź adres URL zgodny ze wzorcem `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > To nie są rzeczywiste wartości. Zaktualizuj te wartości przy użyciu wartości rzeczywiste identyfikator, adres URL odpowiedzi i adres URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej EverBridge](mailto:support@everbridge.com). Można również odwołać się do wzorców przedstawionych w sekcji podstawowe informacje o **konfiguracji SAML** w Azure Portal.

6. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** , aby pobrać **plik XML metadanych Federacji**. Zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfiguracja EverBridge** Skopiuj adresy URL, które są potrzebne do spełnienia wymagań:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi EverBridge.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **EverBridge**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-everbridge-sso"></a>Konfigurowanie logowania jednokrotnego EverBridge

Aby skonfigurować Logowanie jednokrotne w usłudze **EverBridge** jako aplikację **portalu EverBridge Manager** , wykonaj następujące kroki.
 
1. W innym oknie przeglądarki sieci Web Zaloguj się do EverBridge jako administrator.

1. W menu u góry wybierz kartę **Ustawienia** . W obszarze **zabezpieczenia** wybierz pozycję **Logowanie jednokrotne**.
   
     ![Konfigurowanie logowania jednokrotnego](./media/everbridge-tutorial/sso.png)
   
     a. W polu **Nazwa** wprowadź nazwę dostawcy identyfikatora. Przykładem jest nazwa firmy.
   
     b. W polu **nazwa interfejsu API** wprowadź nazwę interfejsu API.
   
     c. Wybierz pozycję **Wybierz plik** , aby przekazać plik metadanych pobrany z Azure Portal.
   
     d. W polu **Lokalizacja tożsamości SAML** zaznacz pozycję **tożsamość w elemencie NameIdentifier instrukcji subject**.
   
     e. W polu **adres URL logowania dostawcy tożsamości** wklej wartość **adresu URL logowania** skopiowaną z Azure Portal.
   
     f. W przypadku **powiązania żądania zainicjowane przez dostawcę usług** wybierz opcję **przekierowywanie http**.

     przykład Wybierz pozycję **Zapisz**.

### <a name="configure-everbridge-as-everbridge-member-portal-sso"></a>Konfigurowanie EverBridge jako logowanie jednokrotne w portalu elementu członkowskiego EverBridge

Aby skonfigurować Logowanie jednokrotne na **EverBridge** jako **Portal elementu członkowskiego EverBridge**, Wyślij pobrany **XML metadanych Federacji** do [zespołu pomocy technicznej EverBridge](mailto:support@everbridge.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-everbridge-test-user"></a>Utwórz użytkownika testowego EverBridge

W tej sekcji utworzysz test User Britta Simon w programie EverBridge. Aby dodać użytkowników na platformie EverBridge, należy skontaktować się z [zespołem pomocy technicznej EverBridge](mailto:support@everbridge.com). Przed użyciem logowania jednokrotnego należy utworzyć i aktywować użytkowników w usłudze EverBridge. 

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do EverBridge, dla którego skonfigurowano Logowanie jednokrotne.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka EverBridge w obszarze Moje aplikacje należy automatycznie zalogować się do EverBridge, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu EverBridge można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).