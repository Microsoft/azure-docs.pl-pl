---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą typu float | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i zmiennoprzecinkowe.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/23/2020
ms.author: jeedes
ms.openlocfilehash: e14537da31b9418c1295ee5d764d882557a53760
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453046"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-float"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą typu float

W tym samouczku dowiesz się, jak zintegrować program float z Azure Active Directory (Azure AD). Po zintegrowaniu usługi z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do tabeli zmiennoprzecinkowej.
* Zezwól użytkownikom na automatyczne logowanie się w celu przestawiania ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja zmiennoprzecinkowa. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://app.float.com/join?).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Float obsługuje funkcję SSO zainicjowaną przez usługę **SP i dostawcy tożsamości**
* Po skonfigurowaniu ustawień zmiennoprzecinkowych można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-float-from-the-gallery"></a>Dodawanie wartości zmiennoprzecinkowej z galerii

Aby skonfigurować integrację elementów zmiennoprzecinkowych z usługą Azure AD, musisz dodać wartość zmiennoprzecinkową z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz wartość **zmiennoprzecinkową** w polu wyszukiwania.
1. Wybierz pozycję **przestaw** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-float"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla celów zmiennoprzecinkowych

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą funkcji float przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w sieci zmiennoprzecinkowej.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD z użyciem wartości zmiennoprzecinkowej, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj bezpośrednie Logowanie jednokrotne](#configure-float-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego zmiennoprzecinkowego](#create-float-test-user)** , aby dysponować odpowiednikiem B. Simon w wartości zmiennoprzecinkowej, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **zmiennoprzecinkowej** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz następujący adres URL: `https://app.float.com/sso/metadata` .

    b. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL przy użyciu wzorca `https://<hostname>.float.com/sso/azuread` .

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wpisz adres URL we wzorcu `https://<hostname>.float.com/login` .

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Zamień <hostname> na nazwę hosta zmiennoprzecinkowego. Jeśli nie masz pewności, skontaktuj się z [zespołem pomocy technicznej dla klientów zmiennoprzecinkowych](mailto:support@float.com) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja zmiennoprzecinkowa oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja zmiennoprzecinkowa oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.
    
    | Nazwa | Atrybut źródłowy|
    | ---------------| --------- |
    | poczta e-mail | user.userprincipalname |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Skonfiguruj ustawienia zmiennoprzecinkowe** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do elementów zmiennoprzecinkowych.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **zmiennoprzecinkowe**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-float-sso"></a>Konfigurowanie typu Logowanie jednokrotne

Aby skonfigurować Logowanie jednokrotne po stronie sieci **zmiennoprzecinkowej** , przejdź do sekcji ustawienia zespołu zmiennoprzecinkowego i wybierz pozycję Konfiguruj z modułu uwierzytelniania. Wklej adres URL logowania do usługi Azure AD w polu adres URL punktu końcowego protokołu SAML 2,0, wklej tekst usługi Azure AD identyfikatora w polu adres URL wystawcy dostawcy tożsamości, wklej pełny tekstu z pobranego **certyfikatu (base64)** w polu Certyfikat X. 509 i Zapisz.

### <a name="create-float-test-user"></a>Utwórz użytkownika testowego zmiennoprzecinkowego

W tej sekcji Utwórz użytkownika o nazwie Britta Simon w obszarze float. Dodaj użytkownika z sekcji osoby lub gość ustawienia zespołu i Udziel im prawa dostępu. Przed skorzystaniem z logowania jednokrotnego należy utworzyć i zaakceptować zaproszenie.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka float w panelu dostępu należy automatycznie zalogować się do elementu zmiennoprzecinkowego, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj pływak z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chronić pływak przy użyciu zaawansowanej widoczności i kontrolek](/cloud-app-security/proxy-intro-aad)