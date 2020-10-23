---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu rozwiązania Cisco AnyConnect | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Cisco AnyConnect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: a89ab7f2304fa51d3e8c7a968d445c9b40a457a3
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456092"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-anyconnect"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Cisco AnyConnect

W tym samouczku dowiesz się, jak zintegrować Cisco AnyConnect z usługą Azure Active Directory (Azure AD). W przypadku integrowania programu Cisco AnyConnect z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do firmy Cisco AnyConnect.
* Zezwól użytkownikom na automatyczne logowanie do firmy Cisco AnyConnect przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Cisco AnyConnect.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Cisco AnyConnect obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne

## <a name="adding-cisco-anyconnect-from-the-gallery"></a>Dodawanie Cisco AnyConnect z galerii

Aby skonfigurować integrację programu Cisco AnyConnect z usługą Azure AD, musisz dodać Cisco AnyConnect z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** w polu wyszukiwania wpisz ciąg **Cisco AnyConnect** .
1. Wybierz pozycję **Cisco AnyConnect** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-cisco-anyconnect"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu Cisco AnyConnect

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu Cisco AnyConnect przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Cisco AnyConnect.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu Cisco AnyConnect, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne Cisco AnyConnect](#configure-cisco-anyconnect-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Cisco AnyConnect](#create-cisco-anyconnect-test-user)** , aby dysponować odpowiednikiem B. Simon w Cisco AnyConnect, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Cisco AnyConnect** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `< YOUR CISCO ANYCONNECT VPN VALUE >`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `< YOUR CISCO ANYCONNECT VPN VALUE >`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej Cisco AnyConnect Client](https://www.cisco.com/c/en/us/support/index.html) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać plik certyfikatu i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie programu Cisco AnyConnect** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

> [!NOTE]
> Jeśli chcesz umieścić wiele TGT serwera, musisz dodać wiele wystąpień aplikacji Cisco AnyConnect z galerii. Ponadto możesz wybrać opcję przekazania własnego certyfikatu w usłudze Azure AD dla wszystkich tych wystąpień aplikacji. Dzięki temu można mieć ten sam certyfikat dla aplikacji, ale można skonfigurować różne identyfikatory i adresy URL odpowiedzi dla każdej aplikacji.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do programu Cisco AnyConnect.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Cisco AnyConnect**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-cisco-anyconnect-sso"></a>Konfigurowanie logowania jednokrotnego Cisco AnyConnect

1. Należy najpierw wykonać tę czynność w interfejsie wiersza polecenia, aby wrócić do korzystania z ASDM w innym czasie.

1. Nawiąż połączenie z urządzeniem sieci VPN, korzystając z usługi ASA z systemem 9,8, a klienci sieci VPN będą mieć program 4.6 +.

1. Najpierw utworzysz Trustpoint i zaimportujmy certyfikat SAML.

   ```
    config t

    crypto ca trustpoint AzureAD-AC-SAML
      revocation-check none
      no id-usage
      enrollment terminal
      no ca-check
    crypto ca authenticate AzureAD-AC-SAML
    -----BEGIN CERTIFICATE-----
    …
    PEM Certificate Text from download goes here
    …
    -----END CERTIFICATE-----
    quit
   ```

1. Następujące polecenia zainicjują obsługę języka SAML dostawcy tożsamości.

   ```
    webvpn
    saml idp https://sts.windows.net/xxxxxxxxxxxxx/ (This is your Azure AD Identifier from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-in https://login.microsoftonline.com/xxxxxxxxxxxxxxxxxxxxxx/saml2 (This is your Login URL from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-out https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0 (This is Logout URL from the Set up Cisco AnyConnect section in the Azure portal)
    trustpoint idp AzureAD-AC-SAML
    trustpoint sp (Trustpoint for SAML Requests - you can use your existing external cert here)
    no force re-authentication
    no signature
    base-url https://my.asa.com
    ```

1. Teraz można zastosować uwierzytelnianie SAML do konfiguracji tunelu sieci VPN.

    ```
    tunnel-group AC-SAML webvpn-attributes
      saml identity-provider https://sts.windows.net/xxxxxxxxxxxxx/
      authentication saml
    end

    write mem
    ```

    > [!NOTE]
    > Istnieje funkcja z konfiguracją SAML dostawcy tożsamości — Jeśli wprowadzisz zmiany w konfiguracji dostawcy tożsamości, musisz usunąć konfigurację dostawcy tożsamości SAML z grupy tuneli i ponownie ją zastosować, aby zmiany zaczęły obowiązywać.

### <a name="create-cisco-anyconnect-test-user"></a>Utwórz użytkownika testowego Cisco AnyConnect

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w programie Cisco AnyConnect. Aby dodać użytkowników na platformie Cisco AnyConnect, Pracuj z [zespołem pomocy technicznej Cisco AnyConnect](https://www.cisco.com/c/en/us/support/index.html) . Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do programu Cisco AnyConnect, dla którego skonfigurowano Logowanie jednokrotne
* Możesz użyć panelu programu Microsoft Access. Po kliknięciu kafelka Cisco AnyConnect w panelu dostępu należy automatycznie zalogować się do programu Cisco AnyConnect, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu programu Cisco AnyConnect można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).