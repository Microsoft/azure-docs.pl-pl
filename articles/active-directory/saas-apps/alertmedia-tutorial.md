---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą AlertMedia | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługami AlertMedia.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c77f6fc-807c-47e8-8e49-eece5ebd7f09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/17/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6aaaa24535c95e0a0d3f2b67709374c083ab738
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726249"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alertmedia"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą AlertMedia

W tym samouczku dowiesz się, jak zintegrować program AlertMedia z usługą Azure Active Directory (Azure AD). Podczas integracji alertmedia z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do alertmedia.
* Włącz użytkownikom automatyczne logowanie do alertów za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego alertmedia.AlertMedia single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* AlertMedia obsługuje zainicjowane **przez protokół** SSO inicjowane przez protokół IDP
* AlertMedia obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time
* Po skonfigurowaniu alertmedia można wymusić kontrolę sesji, które chronią eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-alertmedia-from-the-gallery"></a>Dodawanie alertmedia z galerii

Aby skonfigurować integrację alertumedia z usługą Azure AD, należy dodać alertmedia z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **AlertMedia** w polu wyszukiwania.
1. Wybierz **alertMedia** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-alertmedia"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi AlertMedia

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą alertmedia przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik syt/r działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w alertmedia.

Aby skonfigurować i przetestować przychylić do usługi Azure AD sytuować za pomocą alertmedia, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne alertmedia](#configure-alertmedia-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego AlertMedia](#create-alertmedia-test-user)** — aby mieć odpowiednik B.Simon w AlertMedia, który jest połączony z reprezentacji usługi Azure AD użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **AlertMedia** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<SUBDOMAIN>.alertmedia.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.alertmedia.com/api/sso/saml/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta alertmedia,](mailto:support@alertmedia.com) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja AlertMedia oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej aplikacji AlertMedia oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.
    
    | Nazwa |   |  Atrybut źródłowy|
    | ---------------| --------------- | --------- |
    | email |  | user.userprincipalname |
    | firstname |  | user.givenname |
    | lastname |  | user.surname |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do AlertMedia.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **alertMedia**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-alertmedia-sso"></a>Konfigurowanie wpisu SSO alertmedia

1. W nowym oknie przeglądarki internetowej zaloguj się do witryny firmy AlertMedia jako administrator.
1. Przejdź do **pozycji Firma** i wybierz **opcję Logowanie jednokrotne**.

    ![Przycisk Account (Konto)](./media/alertmedia-tutorial/Configure1.png)
1. W **metodzie uwierzytelniania**wybierz **pozycję Zdalne metadane SAML**
1. Przełączanie na **żądaniu znaku**
1. Przełączanie na **żądaniach pasywnych Zezwalaj**
1. W polu tekstowym **adres URL metadane** wklej wartość **adresu URL metadanych federacji aplikacji,** która została skopiowana z portalu Azure.
1. **Wybierz porównanie** **kontekstu żądanego uwierzytelniania** jako dokładne
1. W polu tekstowym **adresu URL logowania do** protokołu tożsamości wklej wartość adresu URL **logowania,** która została skopiowana z witryny Azure portal.
1. Kliknij **przycisk Zapisz**

### <a name="create-alertmedia-test-user"></a>Utwórz użytkownika testowego AlertMedia

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w AlertMedia. AlertMedia obsługuje just-in-time użytkownika inicjowania obsługi administracyjnej, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w alertmedia, nowy jest tworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka AlertMedia w Panelu dostępu należy automatycznie zalogować się do alertmedia, dla którego skonfigurowano logowanie jednośmiętne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj alertmedia w usłudze Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić AlertMedia dzięki zaawansowanej widoczności i kontrolom](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

