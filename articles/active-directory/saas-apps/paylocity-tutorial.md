---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Paylocity | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Paylocity.
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
ms.openlocfilehash: fcaaea387cab24772b9e6f31f4e7d93ff643b983
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515597"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-paylocity"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Paylocity

W tym samouczku dowiesz się, jak zintegrować usługę Paylocity z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Paylocity z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Paylocity.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Paylocity przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Paylocity.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Paylocity obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

* Po skonfigurowaniu Paylocity można wymusić kontrolki sesji, które chronią eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji wykraczają poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).

## <a name="adding-paylocity-from-the-gallery"></a>Dodawanie Paylocity z galerii

Aby skonfigurować integrację programu Paylocity z usługą Azure AD, musisz dodać Paylocity z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Paylocity** w polu wyszukiwania.
1. Wybierz pozycję **Paylocity** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-paylocity"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Paylocity

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Paylocity przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Paylocity.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Paylocity, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-paylocity-sso)** w usłudze Paylocity, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego Paylocity](#create-paylocity-test-user)** , aby dysponować odpowiednikiem B. Simon w Paylocity, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Paylocity** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wpisz adres URL:  `https://access.paylocity.com/`

1. Kliknij pozycję **Zapisz**.

1. Aplikacja Paylocity oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja Paylocity oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale trzeba zaktualizować te atrybuty wartościami rzeczywistymi.

    | Nazwa |  Atrybut źródłowy|
    | ---------------| --------------- |
    | PartnerID | `P8000010` |
    | PaylocityUser | `user.mail`|
    | PaylocityEntity | < `PaylocityEntity` > |

    > [!NOTE]
    > PaylocityEntity jest IDENTYFIKATORem firmy Paylocity.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk **Edytuj ikonę**.

    ![Zrzut ekranu pokazujący certyfikat podpisywania "S A M L" z akcją "Pobierz" dla "metadanych Federacji X M L".](./media/paylocity-tutorial/edit-samlassertion.png)

1. Wybierz **opcję podpisywania** , aby **podpisać odpowiedź i potwierdzenie SAML** , a następnie kliknij przycisk **Zapisz**.

    ![Edytowanie certyfikatu podpisywania SAML](./media/paylocity-tutorial/saml-assertion.png)

1. W sekcji **Konfigurowanie Paylocity** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Paylocity.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Paylocity**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-paylocity-sso"></a>Konfigurowanie logowania jednokrotnego Paylocity

Aby skonfigurować Logowanie jednokrotne na stronie **Paylocity** , musisz wysłać pobrany **XML metadanych Federacji** i odpowiednie skopiowane adresy URL z Azure Portal do [zespołu pomocy technicznej Paylocity](mailto:service@paylocity.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-paylocity-test-user"></a>Utwórz użytkownika testowego Paylocity

W tej sekcji utworzysz użytkownika o nazwie B. Simon w Paylocity. Aby dodać użytkowników na platformie Paylocity, Pracuj z [zespołem pomocy technicznej Paylocity](mailto:service@paylocity.com) . Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Paylocity w panelu dostępu należy automatycznie zalogować się do Paylocity, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj Paylocity z usługą Azure AD](https://aad.portal.azure.com/)

* [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

* [Jak chronić Paylocity z zaawansowaną widocznością i kontrolkami](/cloud-app-security/proxy-intro-aad)