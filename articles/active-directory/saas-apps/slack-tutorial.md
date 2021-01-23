---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z zapasem czasu | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Slack.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 6e2428967b8e3b4c677752955ea743c5b7d144e5
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98729644"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO, Single Sign-on) z zapasem czasu

W tym samouczku dowiesz się, jak zintegrować zapasy czasu z Azure Active Directory (Azure AD). Podczas integrowania zapasu czasu z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do zapasu czasu.
* Zezwól użytkownikom na automatyczne logowanie do zapasu czasu przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO).

> [!NOTE]
> Jeśli musisz zintegrować z więcej niż jednym wystąpieniem zapasowym w jednej dzierżawie, identyfikator dla każdej aplikacji może być zmienną.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Slack obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**
* Usługa Slack obsługuje aprowizację użytkownika typu **just in time**
* Usługa Slack obsługuje [**zautomatyzowaną** aprowizację użytkowników](./slack-provisioning-tutorial.md)

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-slack-from-the-gallery"></a>Dodawanie usługi Slack z galerii

Aby skonfigurować integrację usługi Slack z usługą Azure AD, należy dodać usługę Slack z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Zapasy czasu** w polu wyszukiwania.
1. Wybierz pozycję **zapasowy** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-slack"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla zapasu czasu

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą zapasowego użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ramach zapasu czasu.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą zapasu czasu, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj zapasowe Logowanie jednokrotne](#configure-slack-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego zapasowego](#create-slack-test-user)** — Aby uzyskać odpowiednik elementu B. Simon w zapasach czasu, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja z aplikacją **zapasową** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<DOMAIN NAME>.slack.com/sso/saml/start`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://slack.com`
    
    c. W polu **adres URL odpowiedzi** wprowadź jeden z następujących wzorców URL:
    
    | Adres URL odpowiedzi|
    |----------|
    | `https://<DOMAIN NAME>.slack.com/sso/saml` |
    | `https://<DOMAIN NAME>.enterprise.slack.com/sso/saml` |

    > [!NOTE]
    > To nie są rzeczywiste wartości. Należy zaktualizować te wartości przy użyciu rzeczywistego adresu URL logowania i adresu URL odpowiedzi. W celu uzyskania tej wartości skontaktuj się z [zespołem pomocy technicznej klienta usługi Slack](https://slack.com/help/contact). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    > [!NOTE]
    > Wartość **identyfikatora (identyfikator jednostki)** może być zmienną, jeśli masz więcej niż jedno wystąpienie zapasowe, które należy zintegrować z dzierżawcą. Użyj wzorca `https://<DOMAIN NAME>.slack.com` . W tym scenariuszu należy również sparować z innym ustawieniem w zapasach czasu, używając tej samej wartości.

1. Aplikacja do Autowypełniania oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/edit-attribute.png)

1. Oprócz powyższych, aplikacja Autowypełniania oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami. Należy również dodać `email` atrybut. Jeśli użytkownik nie ma adresu e-mail, Mapuj **EmailAddress** na **User. userPrincipalName** i Mapuj **wiadomości e-mail** na **User. userPrincipalName**.

    | Nazwa | Atrybut źródłowy |
    | -----|---------|
    | emailaddress | user.userprincipalname |
    | poczta e-mail | user.userprincipalname |

   > [!NOTE]
   > Aby skonfigurować dostawcę usług (SP), należy kliknąć przycisk **Rozszerz** obok **opcji Opcje zaawansowane** na stronie Konfiguracja protokołu SAML. W polu **wystawca dostawcy usług** wprowadź adres URL obszaru roboczego. Wartość domyślna to slack.com. 

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie zapasu czasu** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do zapasu czasu.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Slack**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-slack-sso"></a>Skonfiguruj zapasowe Logowanie jednokrotne

1. Aby zautomatyzować konfigurację w ramach zapasu czasu, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Konfiguracja zapasu** czasu spowoduje przekierowanie do aplikacji zapasowej. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do zapasu czasu. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować zapas czasu, w innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy dla zapasu czasu jako administrator.

2. Przejdź do sekcji **Microsoft Azure AD**, a następnie przejdź do sekcji **Team Settings** (Ustawienia zespołu).

     ![Skonfiguruj Logowanie jednokrotne na Microsoft Azure AD](./media/slack-tutorial/tutorial-slack-team-settings.png)

3. W sekcji **Team Settings** (Ustawienia zespołu) kliknij kartę **Authentication** (Uwierzytelnianie), a następnie kliknij przycisk **Change Settings** (Zmień ustawienia).

    ![Konfiguruj logowanie jednokrotne w ustawieniach zespołu](./media/slack-tutorial/tutorial-slack-authentication.png)

4. W oknie dialogowym **SAML Authentication Settings** (Ustawienia uwierzytelniania SAML) wykonaj następujące czynności:

    ![Skonfiguruj Logowanie jednokrotne w ustawieniach uwierzytelniania SAML](./media/slack-tutorial/tutorial-slack-save-authentication.png)

    a.  W polu tekstowym **SAML 2.0 Endpoint (HTTP)** (Punkt końcowy SAML 2.0 — HTTP) wklej wartość pola **Adres URL logowania** skopiowaną z witryny Azure Portal.

    b.  W polu tekstowym **Identity Provider Issuer** (Wystawca dostawcy tożsamości) wklej wartość pola **Identyfikator usługi Azure AD** skopiowaną z witryny Azure Portal.

    c.  Otwórz pobrany plik certyfikatu w Notatniku, skopiuj jego zawartość do schowka, a następnie wklej go do pola tekstowego **certyfikatu publicznego** .

    d. Skonfiguruj trzy powyższe ustawienia odpowiednio dla Twojego zespołu usługi Slack. Więcej informacji na temat ustawień możesz znaleźć w **przewodniku po konfiguracji logowania jednokrotnego w usłudze Slack** znajdującym się pod następującym adresem. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    ![Skonfiguruj Logowanie jednokrotne na stronie aplikacji](./media/slack-tutorial/tutorial-slack-expand.png)

    e. Kliknij przycisk **Rozwiń** i wprowadź `https://slack.com` w polu tekstowym **wystawca dostawcy usług** .

    f.  Kliknij pozycję **Save Configuration** (Zapisz konfigurację).
    
    > [!NOTE]
    > Jeśli masz więcej niż jedno wystąpienie zapasowe, które chcesz zintegrować z usługą Azure AD, ustaw `https://<DOMAIN NAME>.slack.com` jako **wystawcę dostawcy usług** , aby można było sparować się z ustawieniem **identyfikatora** aplikacji platformy Azure.

### <a name="create-slack-test-user"></a>Tworzenie użytkownika testowego usługi Slack

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w zapasie czasu. Usługa Slack obsługuje aprowizację typu just in time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby uzyskania dostępu do usługi Slack, jeśli jeszcze nie istnieje. Usługa Slack obsługuje także automatyczną aprowizację użytkowników. Więcej informacji na temat konfigurowania automatycznej aprowizacji użytkowników możesz znaleźć [tutaj](slack-provisioning-tutorial.md).

> [!NOTE]
> Jeśli konieczne jest ręczne utworzenie użytkownika, skontaktuj się z [zespołem pomocy technicznej usługi Slack](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect to narzędzie do synchronizacji, które może synchronizować lokalne tożsamości Active Directory z usługą Azure AD, a następnie te synchronizowane użytkownicy mogą również używać aplikacji jako innych użytkowników w chmurze.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania zapasowego, na którym można zainicjować przepływ logowania.

* Bezpośrednio przejdź do adresu URL logowania zapasowego i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka zapasowego w obszarze Moje aplikacje zostanie przekierowany na adres URL logowania zapasowego. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu zapasowego czasu można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)