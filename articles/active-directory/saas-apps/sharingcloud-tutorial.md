---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą SharingCloud | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a pakietem niestandardowym.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 7ae447a9577feba8b43b5b03a757ec4095ee2cb4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177967"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharingcloud"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą SharingCloud

W tym samouczku dowiesz się, jak zintegrować usługę SharingCloud z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi SharingCloud z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do SharingCloud.
* Zezwól użytkownikom na automatyczne logowanie się do usługi SharingCloud przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Jeśli chcesz dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej [bezpłatnej wersji](https://azure.microsoft.com/pricing/free-trial/)próbnej.
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) SharingCloud.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* SharingCloud obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* SharingCloud obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-sharingcloud-from-the-gallery"></a>Dodawanie SharingCloud z galerii

Aby skonfigurować integrację programu SharingCloud z usługą Azure AD, musisz dodać SharingCloud z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .

    ![Przycisk Azure Active Directory](common/select-azuread.png)
    
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)
    
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)
    
1. W sekcji **Dodaj z galerii** wpisz **SharingCloud** w polu wyszukiwania.

    ![SharingCloud na liście wyników](common/search-new-app.png)
    
1. Wybierz pozycję **SharingCloud** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sharingcloud"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla SharingCloud

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą SharingCloud przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w SharingCloud.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą SharingCloud, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-sharingcloud-sso)** w usłudze SharingCloud, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego SharingCloud](#create-sharingcloud-test-user)** , aby dysponować odpowiednikiem B. Simon w SharingCloud, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **SharingCloud** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
    
    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)
    
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę **edycji** dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    Przekaż plik metadanych przy użyciu pliku XML dostarczonego przez SharingCloud. Aby pobrać plik, skontaktuj się z [zespołem obsługi klienta SharingCloud](mailto:support@sharingcloud.com) .

    ![image (obraz)](common/upload-metadata.png)
    
    Wybierz podany plik metadanych i kliknij przycisk **Przekaż**.

    ![image (obraz)](common/browse-upload-metadata.png)

1. Aplikacja SharingCloud oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/edit_attribute.png)

1. Oprócz powyższych, aplikacja SharingCloud oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------- |
    | urn: sharingcloud: SSO: FirstName | user.givenname |
    | urn: sharingcloud: SSO: LastName | user.surname |
    | urn: sharingcloud: SSO: email | user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** kliknij ikonę **Kopiuj** , aby skopiować **adres URL metadanych Federacji** z określonych opcji zgodnie z wymaganiami.

    ![Adres URL metadanych do skopiowania](common/copy_metadataurl.png)

## <a name="configure-sharingcloud-sso"></a>Konfigurowanie logowania jednokrotnego SharingCloud

Aby skonfigurować Logowanie jednokrotne na stronie **SharingCloud** , musisz wysłać skopiowany **adres URL metadanych Federacji** z Azure Portal do [zespołu pomocy technicznej SharingCloud](mailto:support@sharingcloud.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi SharingCloud.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SharingCloud**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

   ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-sharingcloud-test-user"></a>Utwórz użytkownika testowego SharingCloud

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w SharingCloud. SharingCloud obsługuje funkcję inicjowania obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze SharingCloud, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

* Bezpośrednio przejdź do adresu URL usługi SharingCloud i zainicjuj w nim przepływ logowania.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu SharingCloud można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

