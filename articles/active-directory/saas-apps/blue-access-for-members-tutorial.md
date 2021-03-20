---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z dostępem do elementów członkowskich (BAM). Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i niebieskim dostępem dla elementów członkowskich (BAM).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.openlocfilehash: 38765fd6d740c7494cbf7e5a0a38f1d98aecf4a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456980"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-blue-access-for-members-bam"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z dostępem do elementów członkowskich (BAM)

W tym samouczku dowiesz się, jak zintegrować dostęp do elementów członkowskich (BAM) z usługą Azure Active Directory (Azure AD). W przypadku integrowania dostępu Blue (BAM) z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do dostępu Blue do elementów członkowskich (BAM).
* Zezwól użytkownikom na automatyczne logowanie do dostępu do konta usługi Azure AD (BAM) w kolorze niebieskim.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* W przypadku subskrypcji z włączonym logowaniem jednokrotnym (BAM).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.


* Niebieski dostęp dla elementów członkowskich (BAM) obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne




## <a name="adding-blue-access-for-members-bam-from-the-gallery"></a>Dodawanie dostępu Blue do elementów członkowskich (BAM) z galerii

Aby skonfigurować integrację dostępu Blue (BAM) z usługą Azure AD, musisz dodać niebieski dostęp do elementów członkowskich (BAM) z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **niebieską pozycję dostęp dla elementów członkowskich (BAM)** w polu wyszukiwania.
1. Wybierz pozycję **niebieski dostęp dla elementów członkowskich (BAM)** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-blue-access-for-members-bam"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD w celu uzyskania dostępu do elementów członkowskich na niebiesko (BAM)

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą niebieskiego dostępu dla elementów członkowskich (BAM) przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i pokrewnym użytkownikiem w kolorze niebieskim (BAM).

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD z dostępem Blue Access dla elementów członkowskich (BAM), wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj niebieski dostęp dla elementów członkowskich (BAM) logowanie](#configure-blue-access-for-members-bam-sso)** jednokrotne — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz niebieski dostęp dla elementów członkowskich (BAM) Test](#create-blue-access-for-members-bam-test-user)** , aby uzyskać odpowiedni odpowiednik B. Simon w kolorze niebieskim (BAM), który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Blue Access for Members (BAM)** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `<Custom Domain Value>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<CUSTOMURL>/affwebservices/public/saml2assertionconsumer`

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    d. W polu tekstowym **Stan przekaźnika** wpisz adres URL, korzystając z następującego wzorca: `https://<CUSTOMURL>/BAMSSOServlet/sso/BamInboundSsoServlet`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adresu URL odpowiedzi i stanu przekazywania. Skontaktuj się z [zespołem pomocy technicznej dla członków (BAM)](https://www.bcbstx.com/contact-us) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja Blue Access dla elementów członkowskich (BAM) oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, niebieska aplikacja dostępu do elementów członkowskich (BAM) oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa |  Atrybut źródłowy|
    | ------------ | --------- |
    | ClientID | `<ClientID>` |
    | UID | `<UID>` |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie dostępu Blue Access for Members (BAM)** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do dostępu Blue do elementów członkowskich (BAM).

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **niebieski dostęp dla elementów członkowskich (BAM)**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-blue-access-for-members-bam-sso"></a>Konfigurowanie dostępu Blue dla członków (BAM) SSO

Aby skonfigurować Logowanie jednokrotne na **niebiesko dostępie dla członków (BAM)** , musisz wysłać pobrany **XML metadanych Federacji** i odpowiednie skopiowane adresy URL z Azure Portal na [Niebiesky dostęp dla członków (BAM) zespołu pomocy technicznej](https://www.bcbstx.com/contact-us). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-blue-access-for-members-bam-test-user"></a>Utwórz niebieski dostęp dla elementów członkowskich (BAM) Test

W tej sekcji utworzysz użytkownika o nazwie B. Simon w kolorze Blue Access for Members (BAM). Pracuj z [zespołem pomocy technicznej dla członków (BAM)](https://www.bcbstx.com/contact-us) , aby dodać użytkowników na platformie Blue Access for Members (BAM). Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Blue Access for Members (BAM) w panelu dostępu należy automatycznie zalogować się do niebieskiego dostępu dla członków (BAM), dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj dostęp Blue (BAM) w usłudze Azure AD](https://aad.portal.azure.com/)