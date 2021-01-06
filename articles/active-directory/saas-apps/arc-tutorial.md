---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z publikowaniem Arc — Logowanie jednokrotne | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Arc Publishing — SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: fe6f4a1c66b07a518c4bccad9027c4d16e3d18a6
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97913856"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-arc-publishing---sso"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z publikowaniem Arc — Logowanie jednokrotne

W tym samouczku dowiesz się, jak zintegrować publikowanie Arc — Logowanie jednokrotne w usłudze Azure Active Directory (Azure AD). Gdy integrujesz publikowanie Arc — Logowanie jednokrotne za pomocą usługi Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do publikowania łuku — Logowanie jednokrotne.
* Zezwól użytkownikom na automatyczne logowanie do usługi Arc Publishing — Logowanie jednokrotne przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Publikowanie Arc — subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Arc Publishing — SSO obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług oraz dostawcę tożsamości**
* Aplikacja Arc Publishing — SSO obsługuje aprowizowanie użytkowników typu **just in time**


## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Dodawanie aplikacji Arc Publishing — SSO z galerii

Aby skonfigurować integrację aplikacji Arc Publishing — SSO z usługą Azure AD, należy dodać tę aplikację z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz polecenie **Arc Publishing-SSO** w polu wyszukiwania.
1. Wybierz opcję **Publikowanie łuku — logowanie JEDNOkrotne** z poziomu panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-arc-publishing---sso"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD na potrzeby publikowania w usłudze Arc-SSO

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD przy użyciu publikowania Arc — Logowanie jednokrotne przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w obszarze publikowanie Arc — Logowanie jednokrotne.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD przy użyciu publikowania Arc-SSO, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Konfigurowanie publikowania Arc — logowanie JEDNOkrotne](#configure-arc-publishing---sso-sso)** — Rejestracja jednokrotna — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Tworzenie typu ARC Publishing — Użytkownik testowy logowania jednokrotnego](#create-arc-publishing---sso-test-user)** ma odpowiedni odpowiednik B. Simon w programie Arc Publishing — Logowanie jednokrotne jest połączone z reprezentacją użytkownika usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie do **publikowania łuku — integracja aplikacji z logowaniem JEDNOkrotnym** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://www.okta.com/saml2/service-provider/<Unique ID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Arc Publishing — SSO](mailto:inf@washpost.com). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Publikowanie Arc — aplikacja SSO oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/edit-attribute.png)

1. Oprócz powyżej, aplikacja Arc Publishing-SSO oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.


    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |    
    | firstName | user.givenname |
    | lastName | user.surname |
    | poczta e-mail | user.mail |
    | grupy | user.assignedroles |

    > [!NOTE]
    > W tym miejscu atrybut **groups** jest zamapowany na atrybut **user.assignedroles**. Są to role niestandardowe utworzone w usłudze Azure AD do mapowania nazw grup w aplikacji. Więcej wskazówek na temat tworzenia niestandardowych ról w usłudze Azure AD można znaleźć [tutaj](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui).

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie publikowania łuku — logowanie JEDNOkrotne** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do publikowania Arc-SSO.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Arc Publishing — SSO**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli skonfigurowano role zgodnie z opisem w powyższej tabeli, można wybrać ją z listy rozwijanej **Wybierz rolę** .
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-arc-publishing---sso-sso"></a>Konfigurowanie publikowania Arc — Logowanie jednokrotne SSO

Aby skonfigurować logowanie jednokrotne po stronie aplikacji **Arc Publishing — SSO**, należy wysłać pobrany **certyfikat (Base64)** i odpowiednie adresy URL skopiowane z witryny Azure Portal do [zespołu pomocy technicznej aplikacji Arc Publishing — SSO](mailto:inf@washpost.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-arc-publishing---sso-test-user"></a>Tworzenie użytkownika testowego w aplikacji Arc Publishing — SSO

W tej sekcji w aplikacji Arc Publishing — SSO jest tworzony użytkownik o nazwie Britta Simon. Aplikacja Arc Publishing — SSO obsługuje aprowizację użytkowników just-in-time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Arc Publishing — SSO, po uwierzytelnieniu zostanie dla niego utworzone nowe konto.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [publikowaniem w centrum pomocy technicznej](mailto:inf@washpost.com).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do publikowania Arc — adres URL logowania jednokrotnego, w którym można zainicjować przepływ logowania.  

* Przejdź do strony publikowanie Arc — adres URL logowania jednokrotnego (SSO), a następnie zainicjuj w tym miejscu przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do programu Arc Publishing — Logowanie jednokrotne, dla którego skonfigurowano Logowanie jednokrotne 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka publikowanie-Logowanie jednokrotne w obszarze Moje aplikacje, jeśli skonfigurowano w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do programu Arc Publishing-SSO, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu publikowania Arc — Logowanie jednokrotne umożliwia wymuszenie kontroli sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
