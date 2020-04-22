---
title: 'Samouczek: Integracja usługi Azure Active Directory z sap Qualtrics | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem SAP Qualtrics.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: 2e27d020bd25f234d084fba770e234bdccb40a99
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682389"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z sap Qualtrics

W tym samouczku dowiesz się, jak zintegrować sap Qualtrics z usługą Azure Active Directory (Azure AD). Po zintegrowaniu sap qualtrics z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do SAP Qualtrics.
* Włącz użytkownikom automatyczne logowanie się do sap Qualtrics za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: w witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji z usługą Azure AD z aplikacją —SaaS( SaaS), zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne będą następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja SAP Qualtrics włączona dla logowania jednokrotnego (logowanie jednokrotne).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* SAP Qualtrics obsługuje jednostki SSO inicjowane przez **sp** **idp.**
* SAP Qualtrics obsługuje **just in time** użytkownika inicjowania obsługi administracyjnej.
* Po skonfigurowaniu sap qualtrics, można wymusić kontrolę sesji, która chroni eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-sap-qualtrics-from-the-gallery"></a>Dodawanie sap qualtrics z galerii

Aby skonfigurować integrację sap qualtrics z usługą Azure AD, należy dodać SAP Qualtrics z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SAP Qualtrics** w polu wyszukiwania.
1. Wybierz **SAP Qualtrics** z wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla sap Qualtrics

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą SAP Qualtrics przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik syt/r działał, należy ustanowić połączony związek między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w sap Qualtrics.

Aby skonfigurować i przetestować sytą usługę Azure AD z sap Qualtrics, wykonaj następujące bloki konstrukcyjne:

1. [Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso) aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user) aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby włączyć B.Simon do korzystania z usługi Azure AD logowania jednokrotnego.
1. [Skonfiguruj logowanie jednokrotne SAP Qualtrics,](#configure-sap-qualtrics-sso) aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. [Utwórz użytkownika testowego SAP Qualtrics,](#create-sap-qualtrics-test-user) aby mieć odpowiednik B.Simon w SAP Qualtrics, połączony z reprezentacją użytkownika usługi Azure AD.
1. [Przetestuj sytą próbę sycącą,](#test-sso) aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **SAP Qualtrics** znajdź sekcję **Zarządzaj.** Wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający konfigurowanie logowania jednokrotnego ze stroną SAML z wyróżnioną ikoną ołówka](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:
    
    a. W polu tekstowym **Identyfikator** wpisz adres URL, który używa następującego wzorca:

    `https://< DATACENTER >.qualtrics.com`
   
    b. W polu tekstowym **Odpowiedz na adres URL** wpisz adres URL, który używa następującego wzorca:

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    d. W polu tekstowym **Stan przekazywania** wpisz adres URL, który używa następującego wzorca:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Wybierz **pozycję Ustaw dodatkowe adresy URL**i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu **tekstowym Logowania adres URL** wpisz adres URL, który używa następującego wzorca:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości za pomocą rzeczywistego adresu URL logowania, identyfikatora, adresu URL odpowiedzi i stanu przekazywania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej klienta qualtrics.](https://www.qualtrics.com/support/) Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** wybierz ikonę kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Zrzut ekranu przedstawiający certyfikat podpisywania SAML z wyróżnioną ikoną kopiowania](common/copy-metadataurl.png)

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

W tej sekcji można włączyć B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do SAP Qualtrics.

1. W portalu Azure wybierz pozycję **Aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **sap Qualtrics**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. Następnie w oknie dialogowym **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

    ![Zrzut ekranu przedstawiający stronę Użytkownicy i grupy z wyróżnioną pozycją Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy użytkowników. Następnie wybierz pozycję **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Następnie wybierz pozycję **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-sap-qualtrics-sso"></a>Konfigurowanie usługi SAP Qualtrics SSO

Aby skonfigurować logowanie jednokrotne po stronie SAP Qualtrics, wyślij skopiowany **adres URL metadanych federacji aplikacji** z portalu Azure do zespołu pomocy technicznej SAP [Qualtrics.](https://www.qualtrics.com/support/) Zespół pomocy technicznej zapewnia, że połączenie SSO SAML jest prawidłowo ustawione po obu stronach.

### <a name="create-sap-qualtrics-test-user"></a>Tworzenie użytkownika testowego SAP Qualtrics

SAP Qualtrics obsługuje just-in-time użytkownika inicjowania obsługi administracyjnej, która jest domyślnie włączona. Nie ma żadnych dodatkowych działań do podjęcia. Jeśli użytkownik jeszcze nie istnieje w SAP Qualtrics, nowy jest tworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Test SSO 

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka SAP Qualtrics w Panelu dostępu automatycznie zalogujesz się do sap Qualtrics, dla którego skonfigurowano logującą się logującą logację. Aby uzyskać więcej informacji, zobacz [Logowanie i uruchamianie aplikacji z portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj sap Qualtrics z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Chroń sap Qualtrics dzięki zaawansowanej widoczności i kontrolom](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

