---
title: 'Samouczek: integracja Azure Active Directory z oprogramowaniem do zarządzania prywatnością OneTrust | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i oprogramowaniem do zarządzania prywatnością OneTrust.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 77604cdeca76bea847fa6b81c5f0f1865025e96f
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98623183"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Samouczek: integracja Azure Active Directory z oprogramowaniem do zarządzania prywatnością OneTrust

W tym samouczku dowiesz się, jak zintegrować oprogramowanie do zarządzania prywatnością OneTrust z usługą Azure Active Directory (Azure AD). Po zintegrowaniu oprogramowania do zarządzania prywatnością OneTrust z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do oprogramowania do zarządzania prywatnością OneTrust.
* Zezwól użytkownikom na automatyczne logowanie się w celu OneTrust oprogramowania do zarządzania prywatnością przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem do zarządzania prywatnością OneTrust, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz w [tym miejscu](https://azure.microsoft.com/pricing/free-trial/)uzyskać miesięczną wersję próbną.
* Subskrypcja z włączonym logowaniem jednokrotnym w programie OneTrust.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Oprogramowanie do zarządzania prywatnością OneTrust obsługuje logowanie jednokrotne w ramach programu **SP** i **dostawcy tożsamości**

* Oprogramowanie do zarządzania prywatnością OneTrust obsługuje funkcję aprowizacji użytkowników **just in Time**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-onetrust-privacy-management-software-from-the-gallery"></a>Dodawanie oprogramowania do zarządzania prywatnością OneTrust z galerii

Aby skonfigurować integrację oprogramowania do zarządzania prywatnością OneTrust z usługą Azure AD, musisz dodać oprogramowanie do zarządzania prywatnością OneTrust z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **OneTrust oprogramowanie do zarządzania prywatnością** w polu wyszukiwania.
1. Wybierz pozycję OneTrust, aby **zarządzać prywatność oprogramowania** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-onetrust-privacy-management-software"></a>Konfigurowanie i testowanie oprogramowania usługi Azure AD SSO do zarządzania prywatnością OneTrust

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą oprogramowania do zarządzania prywatnością OneTrust za pomocą użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w oprogramowaniu do zarządzania prywatnością OneTrust.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą oprogramowania do zarządzania prywatnością OneTrust, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj oprogramowanie do zarządzania prywatnością OneTrust](#configure-onetrust-privacy-management-software-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego oprogramowania do zarządzania prywatnością OneTrust](#create-onetrust-privacy-management-software-test-user)** , aby dysponować odpowiednikiem oprogramowania do zarządzania prywatnością B. Simon inOneTrust, które jest połączone z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji włączasz Logowanie jednokrotne usługi Azure AD w Azure Portal.
 
1. W Azure Portal na stronie integracja aplikacji do **zarządzania prywatność OneTrust** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz pojedynczą Sign-On metodę** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `https://www.onetrust.com/saml2`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.onetrust.com/auth/consumerservice`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

     W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.onetrust.com/auth/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami adresu URL odpowiedzi i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej OneTrust do zarządzania prywatnością oprogramowania](mailto:support@onetrust.com) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfigurowanie oprogramowania do zarządzania prywatnością OneTrust** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.
1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** , a następnie wpisz hasło.
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej części należy włączyć usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do oprogramowania do zarządzania prywatnością OneTrust.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **OneTrust oprogramowanie do zarządzania prywatnością**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**. Następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy użytkowników. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="configure-onetrust-privacy-management-software-sso"></a>Konfigurowanie logowania jednokrotnego do zarządzania prywatnością OneTrust

Aby skonfigurować Logowanie jednokrotne na stronie **oprogramowania do zarządzania prywatnością OneTrust** , należy wysłać pobrany **kod XML metadanych Federacji** i odpowiednie skopiowane adresy URL z Azure Portal do [zespołu pomocy technicznej zarządzania prywatności OneTrust](mailto:support@onetrust.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-onetrust-privacy-management-software-test-user"></a>Tworzenie użytkownika testowego dla oprogramowania do zarządzania prywatnością OneTrust

W tej sekcji użytkownik o nazwie Britta Simon został utworzony w oprogramowaniu do zarządzania ochroną prywatności OneTrust. Oprogramowanie do zarządzania prywatnością OneTrust obsługuje funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w oprogramowaniu OneTrust do zarządzania prywatnością, po uwierzytelnieniu zostanie utworzone nowe konto.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej OneTrust Management](mailto:support@onetrust.com).

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do OneTrust zarządzania ochroną prywatności w celu zainicjowania przepływu logowania.  
 
* Przejdź do adresu URL logowania oprogramowania do zarządzania prywatnością OneTrust bezpośrednio i zainicjuj tam przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do oprogramowania do zarządzania prywatnością OneTrust, dla którego skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka oprogramowanie do zarządzania prywatnością OneTrust w obszarze Moje aplikacje, jeśli skonfigurowano w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania i skonfigurowania w trybie dostawcy tożsamości, należy automatycznie zalogować się do oprogramowania do zarządzania prywatnością OneTrust, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu oprogramowania do zarządzania prywatnością OneTrust można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).