---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą CyberSolutions MAILBASEΣ \ CMS | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i CyberSolutions MAILBASEΣ \ CMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: aa45494e9870daee079eed4fe92867f64c3c791e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90057383"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cybersolutions-mailbasecmss"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą CyberSolutions MAILBASEΣ \ CMS

W tym samouczku dowiesz się, jak zintegrować usługę CyberSolutions MAILBASEΣ \ CMS z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi CyberSolutions MAILBASEΣ \ CMS z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do CyberSolutions MAILBASEΣ \ CMS.
* Zezwól użytkownikom na automatyczne logowanie do CyberSolutions MAILBASEΣ \ CMS z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* CyberSolutions MAILBASEΣ \ CMS z włączoną subskrypcją logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* CyberSolutions MAILBASEΣ \ CMS obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-cybersolutions-mailbasecmss-from-the-gallery"></a>Dodawanie CyberSolutions MAILBASEΣ \ CMS z galerii

Aby skonfigurować integrację CyberSolutions MAILBASEΣ \ CMS z usługą Azure AD, musisz dodać CyberSolutions MAILBASEΣ \ CMS z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **CYBERSOLUTIONS MAILBASEΣ \ CMS** w polu wyszukiwania.
1. Wybierz pozycję **CYBERSOLUTIONS MAILBASEΣ \ CMS** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-cybersolutions-mailbasecmss"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla CyberSolutions MAILBASEΣ \ CMS

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą CyberSolutions MAILBASEΣ \ CMS przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w CyberSolutions MAILBASEΣ \ CMS.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą CyberSolutions MAILBASEΣ \ CMS, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj logowanie JEDNOkrotne w usłudze CYBERSOLUTIONS MAILBASE](#configure-cybersolutions-mailbase-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego programu CYBERSOLUTIONS MAILBASE](#create-cybersolutions-mailbase-test-user)** , aby uzyskać odpowiednika B. Simon w CyberSolutions MAILBASEΣ \ CMS, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **CYBERSOLUTIONS MAILBASEΣ \ CMS** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.cybercloud.jp/saml/module.php/saml/sp/metadata.php/mb_generic_sp`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.cybercloud.jp/cgi-bin/mbase/mblogin/saml2-acs/mb_generic_sp`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.cybercloud.jp/cgi-bin/mbase/mblogin?saml_domain=<domain>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej CYBERSOLUTIONS MAILBASEΣ \ CMS Client](mailto:tech@cybersolutions.co.jp) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **set up CYBERSOLUTIONS MAILBASEΣ \ CMS** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do CyberSolutions MAILBASEΣ \ CMS.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **CYBERSOLUTIONS MAILBASEΣ \ CMS**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-cybersolutions-mailbase-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze CyberSolutions MAILBASE

Aby skonfigurować Logowanie jednokrotne na **CYBERSOLUTIONS MAILBASEΣ \ CMS** , należy wysłać pobrany **XML metadanych Federacji** i odpowiednie skopiowane adresy URL z Azure Portal do [CyberSolutions MAILBASEΣ \ CMS zespołu pomocy technicznej](mailto:tech@cybersolutions.co.jp). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-cybersolutions-mailbase-test-user"></a>Utwórz użytkownika testowego CyberSolutions MAILBASE

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w CyberSolutions MAILBASEΣ \ CMS. Aby dodać użytkowników na platformie CyberSolutions MAILBASEΣ \ CMS, Pracuj z [zespołem pomocy technicznej CYBERSOLUTIONS MAILBASEΣ \ CMS](mailto:tech@cybersolutions.co.jp) . Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do CyberSolutions MAILBASEΣ \ CMS adresu URL, gdzie można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do CyberSolutions MAILBASEΣ \ CMS adres URL logowania i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do CyberSolutions MAILBASEΣ \ CMS, dla którego skonfigurowano Logowanie jednokrotne. 

Możesz również użyć panelu dostępu programu Microsoft, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka CyberSolutions MAILBASEΣ \ CMS w panelu dostępu, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do CyberSolutions MAILBASEΣ \ CMS, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu CyberSolutions MAILBASEΣ \ CMS można wymusić kontrolę sesji, co chroni w czasie rzeczywistym eksfiltracji i filtrowanie danych poufnych w organizacji. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

