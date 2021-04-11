---
title: 'Samouczek: integracja Azure Active Directory z usługą MVISION w chmurze Azure AD Logowanie jednokrotne | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i MVISION konfigurację logowania jednokrotnego w chmurze usługi Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: eaf6b2526125b13eec67680c292ed1dbae6fcfee
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284417"
---
# <a name="tutorial-integrate-mvision-cloud-azure-ad-sso-configuration-with-azure-active-directory"></a>Samouczek: Integrowanie konfiguracji rejestracji jednokrotnej usługi Azure AD w chmurze MVISION z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować konfigurację SSO usługi Azure AD MVISION w chmurze z usługą Azure Active Directory (Azure AD). W przypadku integrowania konfiguracji usługi Azure AD SSO w chmurze MVISION z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do konfiguracji rejestracji jednokrotnej usługi Azure AD w chmurze MVISION.
* Zezwól użytkownikom na automatyczne logowanie się w celu MVISION konfiguracji rejestracji jednokrotnej w chmurze usługi Azure AD przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* MVISION chmurową subskrypcję usługi Azure AD Logowanie jednokrotne (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Konfiguracja logowania jednokrotnego w usłudze Azure AD w chmurze MVISION obsługuje usługi **SP i dostawcy tożsamości** zainicjowane przez usługę SSO.

## <a name="add-mvision-cloud-azure-ad-sso-configuration-from-the-gallery"></a>Dodawanie konfiguracji usługi Azure AD SSO w chmurze MVISION z galerii

Aby skonfigurować integrację konfiguracji rejestracji jednokrotnej usługi Azure AD w chmurze MVISION w usłudze Azure AD, musisz dodać konfigurację rejestracji jednokrotnej w chmurze MVISION usługi Azure AD z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **MVISION Cloud Azure AD SSO Configuration** w polu wyszukiwania.
1. Wybierz pozycję **MVISION w chmurze konfiguracja logowania jednokrotnego usługi Azure AD** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-mvision-cloud-azure-ad-sso-configuration"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD dla MVISION w chmurze Azure AD Logowanie jednokrotne

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą konfiguracji usługi Azure AD SSO w chmurze MVISION przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze MVISION Cloud Azure AD SSO Configuration.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą konfiguracji logowania jednokrotnego w usłudze MVISION w chmurze, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj konfigurację logowania jednokrotnego w chmurze usługi Azure AD](#configure-mvision-cloud-azure-ad-sso-configuration-sso)** w usłudze MVISION, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Utwórz użytkownika testowego konfiguracji logowania jednokrotnego w usłudze Azure AD MVISION w chmurze](#create-mvision-cloud-azure-ad-sso-configuration-test-user)** , aby uzyskać odpowiednik Britta Simon w usłudze MVISION w chmurze konfiguracja logowania jednokrotnego usługi Azure AD, która jest połączona z reprezentacją użytkownika usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **usługi Datadog** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej usługi Azure AD MVISION Cloud Configuration Client](mailto:support@skyhighnetworks.com) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji Konfiguracja **usługi MVISION w chmurze Azure AD SSO** należy skopiować odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon do korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi MVISION w chmurze Azure AD SSO Configuration.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **MVISION Cloud Azure AD SSO Configuration**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-mvision-cloud-azure-ad-sso-configuration-sso"></a>Konfigurowanie usługi MVISION w chmurze Azure AD SSO konfiguracja logowania jednokrotnego

Aby skonfigurować Logowanie jednokrotne na stronie **Konfiguracja logowania jednokrotnego w usłudze Azure AD w chmurze MVISION** , należy wysłać pobrany **certyfikat (base64)** i odpowiednie skopiowane adresy URL z Azure Portal do [MVISION Cloud usługi Azure AD SSO Configuration support](mailto:support@skyhighnetworks.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-mvision-cloud-azure-ad-sso-configuration-test-user"></a>Tworzenie użytkownika testowego konfiguracji logowania jednokrotnego w usłudze Azure AD w chmurze MVISION

W tej sekcji utworzysz użytkownika o nazwie B. Simon w usłudze MVISION Cloud Azure AD SSO Configuration. Współpracuj z [zespołem pomocy technicznej usługi Azure AD MVISION w chmurze](mailto:support@skyhighnetworks.com) , aby dodać użytkowników z platformy konfiguracji usługi Azure AD rejestracji jednokrotnej w chmurze MVISION. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie na adres URL logowania do usługi Azure AD SSO w chmurze, w którym można zainicjować przepływ logowania.  

* Przejdź do MVISION w chmurze adres URL logowania do usługi Azure AD SSO, a następnie zainicjuj przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do konfiguracji usługi Azure AD SSO w chmurze MVISION, dla której skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka konfiguracja logowania jednokrotnego usługi Azure AD w chmurze MVISION w obszarze Moje aplikacje, jeśli skonfigurowano w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do konfiguracji rejestracji jednokrotnej w chmurze usługi Azure AD, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu konfiguracji usługi Azure AD SSO w chmurze MVISION można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
