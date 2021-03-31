---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Firmex VDR | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Firmex VDR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.openlocfilehash: 6dbd39b5c56192ad2ca957c5500338b50e8c8963
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453388"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-firmex-vdr"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Firmex VDR

W tym samouczku dowiesz się, jak zintegrować usługę Firmex VDR z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Firmex VDR z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Firmex VDR.
* Zezwól użytkownikom na automatyczne logowanie do Firmex VDR przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO, Firmex VDR).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Firmex VDR obsługuje logowanie jednokrotne **z użyciem SP i dostawcy tożsamości**

* Po skonfigurowaniu Firmex można wymusić kontrolki sesji, które chronią eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji wykraczają poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-firmex-vdr-from-the-gallery"></a>Dodawanie Firmex VDR z galerii

Aby skonfigurować integrację usługi Firmex VDR w usłudze Azure AD, musisz dodać VDR Firmex z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Firmex VDR** w polu wyszukiwania.
1. Wybierz pozycję **FIRMEX VDR** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-firmex-vdr"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Firmex VDR

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Firmex VDR przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Firmex VDR.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Firmex VDR, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj logowanie JEDNOkrotne w usłudze FIRMEX VDR](#configure-firmex-vdr-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego programu FIRMEX VDR](#create-firmex-vdr-test-user)** , aby uzyskać odpowiednika B. Simon w Firmex VDR, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Firmex VDR** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://login.firmex.com`

1. Kliknij pozycję **Zapisz**.

1. Aplikacja Firmex VDR oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja Firmex VDR oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ------------ | --------- |
    | poczta e-mail | user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **set-up FIRMEX VDR** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Firmex VDR.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **FIRMEX VDR**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-firmex-vdr-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Firmex VDR

### <a name="before-you-get-started"></a>Przed rozpoczęciem

#### <a name="what-youll-need"></a>Czego potrzebujesz

-   Aktywna subskrypcja usługi Firmex
-   Azure AD jako usługa logowania jednokrotnego
-   Administrator IT, aby skonfigurować Logowanie jednokrotne
-   Po włączeniu logowania jednokrotnego wszyscy użytkownicy Twojej firmy muszą zalogować się do usługi Firmex przy użyciu logowania jednokrotnego, a nie przy użyciu hasła i hasło.

#### <a name="how-long-will-this-take"></a>Jak długo zajmiemy się tym?

Zaimplementowanie logowania jednokrotnego trwa kilka minut. Nie ma już czasu przestoju między obsługą usługi Firmex, która umożliwia logowanie jednokrotne w Twojej witrynie, a użytkownicy firmy uwierzytelniają się przy użyciu logowania jednokrotnego. Po prostu wykonaj poniższe kroki.

### <a name="step-1---identify-your-companys-domains"></a>Krok 1. identyfikowanie domen firmy

Zidentyfikuj domeny, z którymi użytkownicy firmy logują się.

Na przykład:

- @firmex.com
- @firmex.ca

### <a name="step-2---contact-firmex-support-with-your-domains"></a>Krok 2. kontakt z pomocą techniczną Firmex z domenami

Skontaktuj się z [zespołem pomocy technicznej Firmex](mailto:support@firmex.com) poczty e-mail lub wywołaj 1888 688 4042 x. 11, aby mówić do pomocy technicznej Firmex. Przekazuj informacje o domenie. Obsługa Firmex dodaje domeny do VDR jako **domeny z zastrzeżeniami**. Administrator musi teraz skonfigurować Logowanie jednokrotne.

Ostrzeżenie: do momentu, aż administrator lokacji skonfiguruje zastrzeżone domeny, użytkownicy Twojej firmy nie będą mogli zalogować się do VDR. Użytkownicy niebędący firmami (Użytkownicy-Goście) nadal mogą logować się przy użyciu poczty e-mail/hasła. Konfiguracja powinna potrwać kilka minut.

### <a name="step-3---configure-the-claimed-domains"></a>Krok 3 — Konfigurowanie domen przejętych

1. Zaloguj się do Firmex jako administrator lokacji.
1. W lewym górnym rogu kliknij logo firmy.
1. Wybierz kartę  **Logowanie jednokrotne**  . Następnie wybierz pozycję  **Konfiguracja logowania jednokrotnego**. Kliknij domenę, którą chcesz skonfigurować.

    ![Domeny przejęte](./media/firmex-vdr-tutorial/edit-sso.png)  

1. Skontaktuj się z administratorem IT, wypełniając następujące pola. Należy wykonać pola od dostawcy tożsamości:  

    ![Konfiguracja logowania jednokrotnego](./media/firmex-vdr-tutorial/SSO-config.png)

    a. W polu tekstowym **Identyfikator jednostki** wklej wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.

    b. W polu tekstowym **adres URL dostawcy tożsamości** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    c. **Certyfikat klucza publicznego**  — w celu uwierzytelnienia komunikat SAML może być podpisany cyfrowo przez wystawcy. Aby sprawdzić podpis komunikatu, odbiorca wiadomości używa klucza publicznego znanego do wystawcy. Podobnie, aby zaszyfrować komunikat, publiczny klucz szyfrowania należący do odbiorcy końcowego musi być znany wystawcy. W obu sytuacjach — podpisywanie i szyfrowanie — zaufane klucze publiczne muszą być udostępniane z wyprzedzeniem.  Jest to plik **x509** pochodzący z **metadanych Federacji** .

    d. Kliknij przycisk **Zapisz** , aby ukończyć konfigurację logowania jednokrotnego. Zmiany zaczynają obowiązywać natychmiast.

1. W tej chwili Logowanie jednokrotne jest włączone dla Twojej witryny.

### <a name="create-firmex-vdr-test-user"></a>Utwórz użytkownika testowego Firmex VDR

W tej sekcji utworzysz użytkownika o nazwie B. Simon w Firmex. Aby dodać użytkowników na platformie Firmex, Pracuj z [zespołem pomocy technicznej Firmex](mailto:support@firmex.com) . Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Firmex VDR w panelu dostępu należy automatycznie zalogować się do VDR Firmex, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj usługę Firmex VDR w usłudze Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chronić Firmex z zaawansowaną widocznością i kontrolkami](/cloud-app-security/proxy-intro-aad)