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
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 3280c97e735e68aa36f018a8de59964ade9567b7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181921"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z pakietem produktywności Nitro

W tym samouczku dowiesz się, jak zintegrować pakiet Nitro produktywności z usługą Azure Active Directory (Azure AD). Gdy integrujesz pakiet Nitro produktywności z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do pakietu Nitro produktywności.
* Zezwól użytkownikom na automatyczne logowanie się do pakietu Nitro produktywności przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne będą następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja pakietu Nitro produktywności w [przedsiębiorstwie](https://www.gonitro.com/pricing).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Pakiet Nitro produktywności obsługuje usługę **SP** i **dostawcy tożsamości** zainicjowano Logowanie jednokrotne.
* Pakiet produktywności Nitro obsługuje Inicjowanie obsługi użytkowników **just in Time** .

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>Dodaj pakiet Nitro produktywności z galerii

Aby skonfigurować integrację pakietu Nitro produktywności w usłudze Azure AD, musisz dodać pakiet Nitroing Suite z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Nitro produktywność** w polu wyszukiwania.
1. Wybierz z wyników **pakiet Nitro produktywność** , a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla pakietu Nitro produktywności

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą pakietu Nitro produktywności przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy nawiązać relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w pakiecie Nitro produktywności.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą pakietu Nitro produktywności, wykonaj następujące bloki konstrukcyjne:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.

    a. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    
    b. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
    
2. [Utwórz użytkownika testowego pakietu Nitro produktywności](#create-a-nitro-productivity-suite-test-user) , aby dysponować odpowiednikiem B. Simon w pakiecie Nitro produktywności, połączonym z reprezentacją usługi Azure AD.
1. [Przetestuj Logowanie jednokrotne](#test-sso) , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracji aplikacji **pakietu Nitro produktywności** Znajdź sekcję **Zarządzanie** . Wybierz pozycję **Logowanie jednokrotne**.
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

1. Wybierz opcję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** :

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
1. Wybierz pozycję **Dodaj użytkownika**. Następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy użytkowników. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Tworzenie użytkownika testowego pakietu Nitro produktywności

Pakiet Nitro produktywności obsługuje funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. Nie ma żadnych dodatkowych akcji do wykonania. Jeśli użytkownik jeszcze nie istnieje w pakiecie Nitro produktywności, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

1. Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do pakietu Nitro produktywności, w którym można zainicjować przepływ logowania.  

2. Przejdź bezpośrednio do adresu URL logowania do pakietu Nitro produktywności i zainicjuj tam przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do pakietu Nitro produktywności, dla którego skonfigurowano Logowanie jednokrotne 

Możesz również użyć panelu dostępu programu Microsoft, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka Nitro produktywności w panelu dostępu, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do pakietu produktywności nitro, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu pakietu Nitro produktywności można wymusić kontrolki sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).