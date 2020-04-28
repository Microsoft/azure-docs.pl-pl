---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą EasySSO dla JIRA | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i EasySSO dla JIRA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f50fb15f-db09-4a36-b89d-0f5444e2ddca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44a9009121c2dab0701d08f40de7c8f26777bc3a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187108"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-jira"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą EasySSO dla JIRA

W tym samouczku dowiesz się, jak zintegrować usługę EasySSO for JIRA z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi EasySSO for JIRA z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do EasySSO dla JIRA.
* Zezwól użytkownikom na automatyczne logowanie do usługi EasySSO dla JIRA przy użyciu swoich kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* EasySSO dla subskrypcji z obsługą logowania jednokrotnego (SSO) JIRA.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa EasySSO dla JIRA obsługuje usługi **SP i dostawcy tożsamości** zainicjowane przez usługę SSO
* EasySSO dla JIRA obsługuje inicjowanie aprowizacji użytkowników **just in Time**
* Po skonfigurowaniu usługi EasySSO dla JIRA można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych w organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-easysso-for-jira-from-the-gallery"></a>Dodawanie EasySSO dla JIRA z galerii

Aby skonfigurować integrację EasySSO dla JIRA z usługą Azure AD, musisz dodać EasySSO dla JIRA z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **EasySSO dla JIRA** w polu wyszukiwania.
1. Wybierz pozycję **EasySSO for JIRA** from the Results panel, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-jira"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla EasySSO dla JIRA

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą EasySSO dla JIRA przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w EasySSO dla JIRA.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą EasySSO dla JIRA, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj EasySSO dla logowania jednokrotnego](#configure-easysso-for-jira-sso)** w usłudze JIRA, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz EasySSO dla JIRA użytkownika testowego](#create-easysso-for-jira-test-user)** , aby uzyskać odpowiedni odpowiednik B. Simon w EasySSO dla JIRA, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **EasySSO for JIRA** Application Integration Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca:`https://<server-base-url>/plugins/servlet/easysso/saml`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/jirasso/login.jsp`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej EasySSO for JIRA](mailto:support@techtime.co.nz) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. EasySSO for JIRA oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższych, EasySSO for JIRA oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.
    
    | Nazwa |  |  Atrybut źródłowy|
    | ---------------| --------------- | --------- |
    | urn: identyfikator OID: 0.9.2342.19200300.100.1.1 | | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | | user.userprincipalname |
    | urn: identyfikator OID: 2.16.840.1.113730.3.1.241 | | user.displayname |
    | urn:oid:2.5.4.4 | | user.surname |
    | urn:oid:2.5.4.42 | | user.givenname |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)
### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do EasySSO dla JIRA.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **EasySSO dla JIRA**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-easysso-for-jira-sso"></a>Konfigurowanie EasySSO na potrzeby logowania jednokrotnego JIRA

Aby skonfigurować Logowanie jednokrotne na stronie **EasySSO dla JIRA** , musisz wysłać **adres URL metadanych federacji aplikacji** do [EasySSO dla zespołu pomocy technicznej JIRA](mailto:support@techtime.co.nz). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-easysso-for-jira-test-user"></a>Utwórz EasySSO dla użytkownika testowego JIRA

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w EasySSO dla JIRA. EasySSO dla JIRA obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze EasySSO dla JIRA, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka EasySSO dla JIRA w panelu dostępu należy automatycznie zalogować się do EasySSO dla JIRA, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj EasySSO dla JIRA z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić EasySSO dla JIRA z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

