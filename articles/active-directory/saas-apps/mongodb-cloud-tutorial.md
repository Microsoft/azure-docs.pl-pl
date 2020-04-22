---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z usługą MongoDB Cloud | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą MongoDB Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ef392044-235b-4d80-8a33-eeba9b142849
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02cc78f7e786e97062cce6d402c972e66fa56860
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81688113"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z usługą MongoDB Cloud

W tym samouczku dowiesz się, jak zintegrować mongodb cloud z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi MongoDB Cloud z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do usługi MongoDB Cloud, MongoDB Atlas, społeczności MongoDB, Uniwersytetu MongoDB i pomocy technicznej MongoDB.
* Włącz użytkownikom automatyczne logowanie się do usługi MongoDB Cloud za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: w witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji z usługą Azure AD z aplikacją —SaaS( SaaS), zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne będą następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja mongodb cloud, która jest włączona dla logowania jednokrotnego (Logowanie jednokrotne).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* MongoDB Cloud obsługuje **jednostki** SSO inicjowane przez SP i **IDP.**
* MongoDB Cloud obsługuje **just in time** użytkownika inicjowania obsługi administracyjnej.
* Po skonfigurowaniu mongodb cloud, można wymusić kontrolę sesji, która chroni eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mongodb-cloud-from-the-gallery"></a>Dodaj MongoDB Cloud z galerii

Aby skonfigurować integrację usługi MongoDB Cloud z usługą Azure AD, należy dodać mongodb cloud z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **mongodb cloud** w polu wyszukiwania.
1. Wybierz **mongodb cloud** z wyników, a następnie dodać aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-mongodb-cloud"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi MongoDB Cloud

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą usługi MongoDB Cloud przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sygnali działał, należy ustanowić połączony związek między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w chmurze MongoDB Cloud.

Aby skonfigurować i przetestować usługę Azure AD SSO w chmurze MongoDB Cloud, wykonaj następujące bloki konstrukcyjne:

1. [Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso) aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user) aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby włączyć B.Simon do korzystania z usługi Azure AD logowania jednokrotnego.
1. [Skonfiguruj logowanie jednokrotne w chmurze MongoDB,](#configure-mongodb-cloud-sso) aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. [Utwórz użytkownika testowego mongodb cloud,](#create-a-mongodb-cloud-test-user) aby mieć odpowiednik B.Simon w mongodb cloud, połączone z reprezentacji usługi Azure AD użytkownika.
1. [Przetestuj sytą próbę sycącą,](#test-sso) aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **mongodb cloud** znajdź sekcję **Zarządzanie.** Wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający konfigurowanie logowania jednokrotnego ze stroną SAML z wyróżnioną ikoną ołówka](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, który używa następującego wzorca:`https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. W polu tekstowym **Odpowiedz na adres URL** wpisz adres URL, który używa następującego wzorca:`https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Wybierz **pozycję Ustaw dodatkowe adresy URL**i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, który używa następującego wzorca:`https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości za pomocą rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej klienta mongodb cloud.](https://support.mongodb.com/) Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja MongoDB Cloud oczekuje, że potwierdzenia SAML będą w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Zrzut ekranu przedstawiający atrybuty domyślne](common/default-attributes.png)

1. Oprócz poprzednich atrybutów aplikacja MongoDB Cloud oczekuje, że kilka więcej atrybutów zostanie przekazanych z powrotem w odpowiedzi SAML. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.
    
    | Nazwa | |  Atrybut źródłowy|
    | ---------------| --------------- | --------- |
    | email | | user.userprincipalname |
    | firstName | | user.givenname |
    | lastName | | user.surname |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź identyfikator **XML metadanych federacji**. Wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Zrzut ekranu przedstawiający sekcję Certyfikat podpisywania SAML z wyróżnionym łączem Pobierania](common/metadataxml.png)

1. W sekcji **Konfigurowanie usługi MongoDB Cloud** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Zrzut ekranu przedstawiający sekcję Konfigurowanie usługi Mongo DB Cloud z wyróżnionymi adresami URL](common/copy-configuration-urls.png)
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

W tej sekcji można włączyć B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do usługi MongoDB Cloud.

1. W portalu Azure wybierz pozycję **Aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **MongoDB Cloud**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. Następnie w oknie dialogowym **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

    ![Zrzut ekranu przedstawiający stronę Użytkownicy i grupy z wyróżnioną pozycją Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy użytkowników. Następnie wybierz pozycję **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Następnie wybierz pozycję **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-mongodb-cloud-sso"></a>Konfigurowanie usługi SSO usługi MongoDB Cloud

Aby skonfigurować logowanie jednokrotne po stronie mongodb cloud, potrzebujesz odpowiednich adresów URL skopiowanych z witryny Azure portal. Należy również skonfigurować aplikację federacji dla organizacji mongodb cloud. Postępuj zgodnie z instrukcjami zawartymi w [dokumentacji mongodb cloud](https://docs.atlas.mongodb.com/security/federated-authentication/index.html). Jeśli masz problem, skontaktuj się z [zespołem pomocy technicznej MongoDB Cloud](https://support.mongodb.com/).

### <a name="create-a-mongodb-cloud-test-user"></a>Tworzenie użytkownika testowego mongodb cloud

MongoDB Cloud obsługuje just-in-time inicjowania obsługi administracyjnej użytkowników, który jest domyślnie włączony. Nie ma żadnych dodatkowych działań do podjęcia. Jeśli użytkownik jeszcze nie istnieje w mongodb cloud, nowy jest tworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Test SSO 

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka MongoDB Cloud w Panelu dostępu automatycznie zalogujesz się do chmury MongoDB Cloud, dla której skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji, zobacz [Logowanie i uruchamianie aplikacji z portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Zarejestruj się w usłudze MongoDB Atlas na platformie Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/mongodb.mongodb_atlas_azure_08082019?tab=Overview)

- [Wypróbuj mongodb cloud z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Chroń chmurę MongoDB dzięki zaawansowanej widoczności i kontroli](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

