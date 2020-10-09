---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z pakietem produktywności Nitro | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i pakietem produktywności nitro.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.openlocfilehash: b67e280824c5e2336aa79e14d3e09d3a670118a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88554340"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z pakietem produktywności Nitro

W tym samouczku dowiesz się, jak zintegrować pakiet Nitro produktywności z usługą Azure Active Directory (Azure AD). Gdy integrujesz pakiet Nitro produktywności z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do pakietu Nitro produktywności.
* Zezwól użytkownikom na automatyczne logowanie się do pakietu Nitro produktywności przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji oprogramowania jako usługi (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne będą następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja pakietu Nitro produktywności w [przedsiębiorstwie](https://www.gonitro.com/pricing).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Pakiet Nitro produktywności obsługuje usługę **SP** i **dostawcy tożsamości** zainicjowano Logowanie jednokrotne.
* Pakiet produktywności Nitro obsługuje Inicjowanie obsługi użytkowników **just in Time** .
* Po skonfigurowaniu pakietu Nitro produktywności można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Informacje o sposobie wymuszania kontroli sesji przy użyciu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>Dodaj pakiet Nitro produktywności z galerii

Aby skonfigurować integrację pakietu Nitro produktywności w usłudze Azure AD, musisz dodać pakiet Nitroing Suite z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Nitro produktywność** w polu wyszukiwania.
1. Wybierz z wyników **pakiet Nitro produktywność** , a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla pakietu Nitro produktywności

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą pakietu Nitro produktywności przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy nawiązać relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w pakiecie Nitro produktywności.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą pakietu Nitro produktywności, wykonaj następujące bloki konstrukcyjne:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. Skonfiguruj Logowanie jednokrotne w [pakiecie Nitro produktywności](#configure-nitro-productivity-suite-sso) , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. [Utwórz użytkownika testowego pakietu Nitro produktywności](#create-a-nitro-productivity-suite-test-user) , aby dysponować odpowiednikiem B. Simon w pakiecie Nitro produktywności, połączonym z reprezentacją usługi Azure AD.
1. [Przetestuj Logowanie jednokrotne](#test-sso) , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracji aplikacji **pakietu Nitro produktywności** Znajdź sekcję **Zarządzanie** . Wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. W sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)**. Wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Zrzut ekranu przedstawiający sekcję certyfikat podpisywania SAML z wyróżnionym linkiem pobierania](common/certificatebase64.png)
    
1. W sekcji **Skonfiguruj pakiet produktywności Nitro** wybierz ikonę kopiowania obok **adresu URL logowania**.
    
    ![Zrzut ekranu przedstawiający sekcję Konfigurowanie pakietu Nitro produktywności z wyróżnionymi adresami URL i ikonami kopiowania](common/copy-configuration-urls.png)
    
1. W [portalu administracyjnym Nitro](https://admin.gonitro.com/)na stronie **Ustawienia przedsiębiorstwa** Znajdź sekcję **Logowanie** jednokrotne. Wybierz kolejno pozycje **Konfiguracja Logowanie jednokrotne SAML**.

    a. Wklej **adres URL logowania** z poprzedniego kroku do pola **Zaloguj się adres URL** .
    
    b. Przekaż **certyfikat (base64)** z wcześniejszego kroku w polu **certyfikat podpisywania certyfikatu x509** .
    
    c. Wybierz pozycję **Prześlij**.
    
    d. Wybierz pozycję **Włącz logowanie jednokrotne**.


1. Wróć do witryny [Azure Portal](https://portal.azure.com/). Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający Konfigurowanie pojedynczej Sign-On ze stroną SAML z wyróżnioną ikoną ołówka](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** skopiuj i wklej pole **Identyfikator jednostki SAML** w [portalu administratora Nitro](https://admin.gonitro.com/). Powinien on mieć następujący wzorzec: `urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. W polu tekstowym **adres URL odpowiedzi** skopiuj i wklej pole **adres URL usługi ACS** w [portalu administratora Nitro](https://admin.gonitro.com/). Powinien on mieć następujący wzorzec: `https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Wybierz opcję **Ustaw dodatkowe adresy URL**i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://sso.gonitro.com/login`

1. Wybierz pozycję **Zapisz**.

1. Aplikacja pakietu Nitro produktywności oczekuje, że potwierdzenia SAML mają być w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Zrzut ekranu atrybutów domyślnych](common/default-attributes.png)

1. Oprócz powyższych atrybutów aplikacja Nitro Suite oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML. Te atrybuty są wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.
    
    | Nazwa  |  Atrybut źródłowy|
    | ---------------| --------------- |
    | employeeNumber |  user.objectid |


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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do pakietu Nitro produktywności.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Nitro produktywność**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**. Następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Zrzut ekranu strony użytkownicy i grupy z wyróżnioną pozycją Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy użytkowników. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. Jeśli oczekujesz, że jakakolwiek wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-nitro-productivity-suite-sso"></a>Konfigurowanie rejestracji jednokrotnej w pakiecie Nitro produktywności

Aby skonfigurować Logowanie jednokrotne na stronie zestawu Nitro produktywności, Wyślij pobrany **certyfikat (base64)** i odpowiednie skopiowane adresy url z Azure Portal do [zespołu pomocy technicznej Nitro Suite](https://www.gonitro.com/support). Zespół pomocy technicznej gwarantuje, że połączenie SSO protokołu SAML jest prawidłowo ustawione na obu stronach.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Tworzenie użytkownika testowego pakietu Nitro produktywności

Pakiet Nitro produktywności obsługuje funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. Nie ma żadnych dodatkowych akcji do wykonania. Jeśli użytkownik jeszcze nie istnieje w pakiecie Nitro produktywności, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka pakietu Nitro produktywności w panelu dostępu użytkownik zostanie automatycznie zalogowany do pakietu nitro, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji, zobacz artykuł [Logowanie i uruchamianie aplikacji z poziomu portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj pakiet Nitro produktywności w usłudze Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Chroń pakiet Nitro produktywność dzięki zaawansowanemu wglądowi i kontrolkom](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

