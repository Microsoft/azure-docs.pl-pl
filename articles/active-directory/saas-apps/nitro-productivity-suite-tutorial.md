---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z pakietem Nitro Productivity Suite | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a pakietem Nitro Productivity Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98c96c9f-18a6-4a20-919b-74fb113ee465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca675e43f4d20898d9f97c175da71f8d5c59e4f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676919"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z pakietem Nitro Productivity Suite

W tym samouczku dowiesz się, jak zintegrować pakiet Nitro Productivity Suite z usługą Azure Active Directory (Azure AD). Po zintegrowaniu pakietu Nitro Productivity Suite z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do pakietu Nitro Productivity Suite.
* Włącz automatyczne logowanie użytkowników do pakietu Nitro Productivity Suite za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: w witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji z usługą Azure AD z aplikacją —SaaS( SaaS), zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne będą następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* [Subskrypcja](https://www.gonitro.com/pricing)nitro Productivity Suite Enterprise .

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Nitro Productivity Suite obsługuje jednostki SSO inicjowane przez **SP** i **IDP.**
* Nitro Productivity Suite obsługuje **inicjowanie** obsługi administracyjnej użytkowników w czasie.
* Po skonfigurowaniu pakietu Nitro Productivity Suite można wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>Dodaj pakiet Nitro Productivity Suite z galerii

Aby skonfigurować integrację pakietu Nitro Productivity Suite z usługą Azure AD, należy dodać pakiet Nitro Productivity Suite z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Nitro Productivity Suite** w polu wyszukiwania.
1. Wybierz **Nitro Productivity Suite** z wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla pakietu Nitro Productivity Suite

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą pakietu Nitro Productivity Suite przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić połączony związek między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w pakiecie Nitro Productivity Suite.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą pakietu Nitro Productivity Suite, wykonaj następujące elementy konstrukcyjne:

1. [Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso) aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user) aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby włączyć B.Simon do korzystania z usługi Azure AD logowania jednokrotnego.
1. [Skonfiguruj logowanie jednokrotne pakietu Nitro Productivity Suite,](#configure-nitro-productivity-suite-sso) aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. [Utwórz użytkownika testowego Nitro Productivity Suite,](#create-a-nitro-productivity-suite-test-user) aby mieć odpowiednik B.Simon w pakiecie Nitro Productivity Suite, połączony z reprezentacją użytkownika usługi Azure AD.
1. [Przetestuj sytą próbę sycącą,](#test-sso) aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **Nitro Productivity Suite** znajdź sekcję **Zarządzanie.** Wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. W sekcji **Certyfikat podpisywania SAML** znajdź **certyfikat (Base64)**. Wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Zrzut ekranu przedstawiający sekcję Certyfikat podpisywania SAML z wyróżnionym łączem Pobierania](common/certificatebase64.png)
    
1. W sekcji **Konfigurowanie pakietu Nitro Productivity Suite** wybierz ikonę kopiowania obok **adresu URL logowania**.
    
    ![Zrzut ekranu przedstawiający sekcję Konfigurowanie pakietu Nitro Productivity Suite z wyróżnionymi adresami URL i ikonami kopiowania](common/copy-configuration-urls.png)
    
1. W [portalu administratora nitro](https://admin.gonitro.com/)na stronie **Ustawienia przedsiębiorstwa** znajdź sekcję **Logowanie jednokrotne.** Wybierz **opcję Setup SAML SSO**.

    a. Wklej **adres URL logowania** z poprzedniego kroku do pola **Adresu URL logowania.**
    
    b. Przekaż **certyfikat (Base64)** z wcześniejszego kroku w polu **Certyfikat podpisywania X509.**
    
    d. Wybierz pozycję **Prześlij**.
    
    d. Wybierz **włącz logowanie jednokrotne**.


1. Wróć do [portalu Azure](https://portal.azure.com/). Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający konfigurowanie logowania jednokrotnego ze stroną SAML z wyróżnioną ikoną ołówka](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** skopiuj i wklej pole **Identyfikator jednostki SAML** z [portalu administratora nitro](https://admin.gonitro.com/). Powinien mieć następujący wzór:`urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. W polu tekstowym **Odpowiedz na adres URL** skopiuj i wklej pole **ADRESU URL usługi ACS** z [portalu administratora nitro](https://admin.gonitro.com/). Powinien mieć następujący wzór:`https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Wybierz **pozycję Ustaw dodatkowe adresy URL**i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://sso.gonitro.com/login`

1. Wybierz pozycję **Zapisz**.

1. Aplikacja Nitro Productivity Suite oczekuje, że potwierdzenia SAML będą w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Zrzut ekranu przedstawiający atrybuty domyślne](common/default-attributes.png)

1. Oprócz poprzednich atrybutów aplikacja Nitro Productivity Suite oczekuje, że kilka innych atrybutów zostanie przekazanych z powrotem w odpowiedzi SAML. Te atrybuty są wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.
    
    | Nazwa  |  Atrybut źródłowy|
    | ---------------| --------------- |
    | liczba pracowników |  user.objectid |


### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory** > **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło,** a następnie zapisz hasło.
   1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć B.Simon do korzystania z usługi Azure logowania jednokrotnego, udzielając dostępu do Nitro Productivity Suite.

1. W portalu Azure wybierz pozycję **Aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Nitro Productivity Suite**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. Następnie w oknie dialogowym **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

    ![Zrzut ekranu przedstawiający stronę Użytkownicy i grupy z wyróżnioną pozycją Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy użytkowników. Następnie wybierz pozycję **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Następnie wybierz pozycję **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-nitro-productivity-suite-sso"></a>Konfigurowanie sygn.

Aby skonfigurować logowanie jednokrotne po stronie pakietu Nitro Productivity Suite, wyślij pobrany **certyfikat (Base64)** i odpowiednie skopiowane adresy URL z witryny Azure portal do [zespołu pomocy technicznej nitro productivity suite](https://www.gonitro.com/support). Zespół pomocy technicznej zapewnia, że połączenie SSO SAML jest prawidłowo ustawione po obu stronach.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Tworzenie użytkownika testowego pakietu Nitro Productivity Suite

Nitro Productivity Suite obsługuje inicjowanie obsługi administracyjnej tylko w czasie, która jest domyślnie włączona. Nie ma żadnych dodatkowych działań do podjęcia. Jeśli użytkownik jeszcze nie istnieje w pakiecie Nitro Productivity Suite, po uwierzytelnieniu zostanie utworzony nowy.

## <a name="test-sso"></a>Test SSO 

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka Nitro Productivity Suite w Panelu dostępu automatycznie zalogujesz się do pakietu Nitro Productivity Suite, dla którego skonfigurowano logującą się logującą logującą log. Aby uzyskać więcej informacji, zobacz [Logowanie i uruchamianie aplikacji z portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj pakiet Nitro Productivity Suite z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Chroń pakiet Nitro Productivity Suite dzięki zaawansowanej widoczności i sterowaniu](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

