---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą SiteIntel | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i SiteIntel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: jeedes
ms.openlocfilehash: 0c8224bf84a2235086d941df7d02ab6f458f3f16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92510002"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-siteintel"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą SiteIntel

W tym samouczku dowiesz się, jak zintegrować usługę SiteIntel z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi SiteIntel z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do SiteIntel.
* Zezwól użytkownikom na automatyczne logowanie się do usługi SiteIntel przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji, Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji oprogramowania jako usługi (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO) SiteIntel.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa SiteIntel obsługuje logowanie jednokrotne z użyciem zainicjowanych przez usługę SP i dostawcy tożsamości.
* Po skonfigurowaniu SiteIntel można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-siteintel-from-the-gallery"></a>Dodaj SiteIntel z galerii

Aby skonfigurować integrację programu SiteIntel z usługą Azure AD, musisz dodać SiteIntel z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W polu **Dodaj z galerii** wprowadź **SiteIntel**.
1. Na liście wyników wybierz pozycję **SiteIntel**, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-siteintel"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla SiteIntel

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą SiteIntel przy użyciu użytkownika testowego o nazwie *B. Simon*. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w SiteIntel.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą SiteIntel, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.  

    a. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą użytkownika B. Simon.  

    b. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić użytkownikowi B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.

1. **[Skonfiguruj Logowanie jednokrotne](#configure-siteintel-sso)** w usłudze SiteIntel, aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.

    * **[Utwórz użytkownika testowego SiteIntel](#create-a-siteintel-test-user)** , aby dysponować odpowiednikiem użytkownika B. Simon w SiteIntel, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.

1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal, wykonaj następujące czynności:

1. Na [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **SiteIntel** przejdź do sekcji **Zarządzanie** , a następnie wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** obok pozycji **Podstawowa konfiguracja SAML** wybierz pozycję **Edytuj** (ikona pióra).

   ![Zrzut ekranu przedstawiający okienko "Konfigurowanie logowania jednokrotnego przy użyciu języka SAML"](common/edit-urls.png)

1. Aby skonfigurować aplikację w trybie inicjowanym przez dostawcy tożsamości, w sekcji **Podstawowa konfiguracja języka SAML** wykonaj następujące czynności:

    a. W polu **Identyfikator** wpisz adres URL w następującym formacie: `urn:amazon:cognito:sp:<REGION>_<USERPOOLID>`

    b. W polu **adres URL odpowiedzi** wpisz adres URL w następującym formacie: `https://<CLIENT>.auth.siteintel.com/saml2/idpresponse`

    c. W polu **Stan przekazywania** wpisz adres URL w następującym formacie: `https://<CLIENT>.siteintel.com`

1. Aby skonfigurować aplikację w trybie inicjowania programu SP, wybierz opcję **Ustaw dodatkowe adresy URL**, a następnie wykonaj następujące czynności:

   * W polu **adres URL logowania** wpisz adres URL w następującym formacie: `https://<CLIENT>.siteintel.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je za pomocą rzeczywistego identyfikatora, adresu URL odpowiedzi, adresu URL logowania i stanu przekazywania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej SiteIntel Client](mailto:support@intalytics.com). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** wybierz przycisk **Kopiuj** , aby skopiować adres URL w polu **adres URL metadanych federacji aplikacji** .

    ![Zrzut ekranu przedstawiający przycisk kopiowania adresu URL metadanych federacji aplikacji](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W okienku po lewej stronie Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części okienka.
1. We właściwościach **użytkownika** wykonaj następujące czynności:

   a. W polu **Nazwa** wprowadź wartość **B. Simon**.  

   b. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika w następującym formacie: `username@companydomain.extension` (na przykład `B.Simon@contoso.com` ).

   c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.

   d. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji zostanie włączona funkcja User B. Simon, która umożliwia logowanie jednokrotne na platformie Azure przez przyznanie dostępu do SiteIntel.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście **aplikacje** wybierz pozycję **SiteIntel**.
1. Na stronie Przegląd aplikacji w sekcji **Zarządzanie** wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika** , a następnie w okienku **Dodaj przypisanie** wybierz pozycję **Użytkownicy i grupy**.

    ![Zrzut ekranu przedstawiający przycisk "Dodaj użytkownika"](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** wybierz pozycję **B. Simon**, a następnie wybierz przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w okienku **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie wybierz przycisk **Wybierz** .
1. W okienku **Dodaj przypisanie** wybierz przycisk **Przypisz** .

## <a name="configure-siteintel-sso"></a>Konfigurowanie logowania jednokrotnego SiteIntel

Aby skonfigurować Logowanie jednokrotne po stronie SiteIntel, Wyślij adres URL skopiowany z pola **adres URL metadanych federacji aplikacji** do [zespołu pomocy technicznej SiteIntel](mailto:support@intalytics.com). Ustawili tę wartość, aby ustanowić połączenie SSO protokołu SAML prawidłowo po obu stronach.

### <a name="create-a-siteintel-test-user"></a>Tworzenie użytkownika testowego SiteIntel

W tej sekcji utworzysz użytkownika o nazwie *Britta Simon* w SiteIntel. Aby dodać użytkowników na platformie SiteIntel, Pracuj z [zespołem pomocy technicznej SiteIntel](mailto:support@intalytics.com) . Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka **SiteIntel** w panelu dostępu należy automatycznie zalogować się do SiteIntel, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)
- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)
- [Wypróbuj SiteIntel z usługą Azure AD](https://aad.portal.azure.com/)
- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
- [Jak chronić SiteIntel z zaawansowaną widocznością i kontrolkami](/cloud-app-security/proxy-intro-aad)