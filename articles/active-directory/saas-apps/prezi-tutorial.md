---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Prezi | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Prezi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 2f3e540174643f20c87396b8568f6e5b0a1ab16d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89658943"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-prezi"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym za pomocą Prezi

W tym samouczku dowiesz się, jak zintegrować usługę Prezi z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Prezi z usługą Azure AD można:

* Kontroluj, kto ma dostęp do usługi Prezi w usłudze Azure AD.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Prezi przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji oprogramowania jako usługi (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja Prezi włączona z logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Prezi obsługuje logowanie jednokrotne z użyciem dodatków SP-i dostawcy tożsamości.
* Prezi obsługuje Inicjowanie obsługi użytkowników just in Time.
* Po skonfigurowaniu Prezi można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Informacje o sposobie wymuszania kontroli sesji przy użyciu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-prezi-from-the-gallery"></a>Dodaj Prezi z galerii

Aby skonfigurować integrację programu Prezi z usługą Azure AD, musisz dodać Prezi z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wprowadź **Prezi** w polu wyszukiwania.
1. Na panelu Wyniki wybierz pozycję **Prezi** , a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-prezi"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Prezi

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Prezi przy użyciu użytkownika testowego o nazwie B. Simon. Aby logowanie jednokrotne działało, należy nawiązać relację między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Prezi.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Prezi, wykonaj następujące bloki konstrukcyjne:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) w celu przetestowania logowania jednokrotnego usługi Azure AD za pomocą B. Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. [Skonfiguruj Logowanie jednokrotne](#configure-prezi-sso) w usłudze Prezi, aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. [Utwórz użytkownika testowego Prezi](#create-a-prezi-test-user) , aby miał odpowiednik B. Simon w Prezi, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. [Przetestuj Logowanie jednokrotne](#test-sso) , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Aby włączyć logowanie jednokrotne w usłudze Azure AD w Azure Portal:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Prezi** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę **edycji** , aby edytować ustawienia w **podstawowej konfiguracji protokołu SAML**.

   ![Edytuj podstawowe ustawienia konfiguracji SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** użytkownik nie musi wykonywać żadnych czynności, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

1. Wybierz opcję **Ustaw dodatkowe adresy URL**i wykonaj następujące czynności, jeśli chcesz skonfigurować aplikację w trybie zainicjowanym przez program **SP**:

    W polu **adres URL logowania** wprowadź adres URL: `https://prezi.com/login/sso/` .

1. Wybierz pozycję **Zapisz**.

1. Aplikacja Prezi oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Atrybuty użytkownika & oświadczenia](common/default-attributes.png)

1. Aplikacja Prezi oczekuje również, że kilka atrybutów zostanie przekazana z powrotem w odpowiedzi SAML, jak pokazano tutaj. Te atrybuty są również wstępnie wypełniane, ale można je przejrzeć zgodnie z wymaganiami.
    
    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | given_name | user.givenname |
    | family_name | user.surname |

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź pozycję **certyfikat (base64)**. Wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfiguracja Prezi** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W Azure Portal, w okienku po lewej stronie wybierz pozycję **Azure Active Directory**. Przejdź do pozycji **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach użytkownika wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość **B. Simon**.
   1. W polu **Nazwa użytkownika** wprowadź `username@companydomain.extension` na przykład `B.Simon@contoso.com` .
   1. Zaznacz pole wyboru **Pokaż hasło** . Zapisz wartość, która pojawia się w polu **hasło** .
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji należy włączyć usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Prezi.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Prezi**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link Użytkownicy i grupy](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij pozycję **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-prezi-sso"></a>Konfigurowanie logowania jednokrotnego Prezi

1. W innym oknie przeglądarki sieci Web Zaloguj się do Prezi przy użyciu konta zespołu i przejdź do [konsoli administracyjnej](https://prezi.com/organizations/manage).

1. W **konsoli administracyjnej**wybierz kartę **Ustawienia** .

    ![Karta Ustawienia](./media/prezi-tutorial/settings-image.png)

1. Przejdź do sekcji **Single Sign-On (SSO)** i włącz przełącznik, aby włączyć logowanie jednokrotne.
    
    ![Przełącznik pojedynczego Sign-On (SSO)](./media/prezi-tutorial/single-signon.png)

1. W sekcji Logowanie jednokrotne **(SSO)** wykonaj następujące kroki:

    ![Sekcja Logowanie jednokrotne (SSO)](./media/prezi-tutorial/configuration.png)

    1. W polu **Identyfikator lub adres URL wystawcy** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    1. W polu **punkt końcowy SAML 2,0 (http)** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    1. Otwórz pobrany **certyfikat (base64)** z Azure Portal do Notatnika. Skopiuj zawartość certyfikatu i wklej zawartość do pola **certyfikat (X. 509)** .

    1. Wybierz pozycję **Zapisz**.

### <a name="create-a-prezi-test-user"></a>Tworzenie użytkownika testowego Prezi

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w Prezi. Prezi obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie ma elementu Action. Jeśli użytkownik nie istnieje jeszcze w usłudze Prezi, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację rejestracji jednokrotnej usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka Prezi w panelu dostępu należy automatycznie zalogować się do konta Prezi, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Wypróbuj Prezi z usługą Azure AD](https://aad.portal.azure.com/)
- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Jak chronić Prezi z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

