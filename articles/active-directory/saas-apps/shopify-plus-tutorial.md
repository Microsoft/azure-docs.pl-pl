---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Shopify Plus | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Shopify Plus.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.openlocfilehash: d7a5d160fa8a0209478fe81d2880e8956828350d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993503"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-shopify-plus"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Shopify Plus

W tym samouczku dowiesz się, jak zintegrować usługę Shopify Plus z Azure Active Directory (Azure AD). Po zintegrowaniu usługi Shopify Plus z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Shopify Plus.
* Zezwól użytkownikom na automatyczne logowanie do Shopify plus przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (Shopify plus).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Shopify Plus obsługuje logowanie jednokrotne **z użyciem SP i dostawcy tożsamości**

* Po skonfigurowaniu Shopify plus można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-shopify-plus-from-the-gallery"></a>Dodawanie Shopify Plus z galerii

Aby skonfigurować integrację usługi Shopify Plus z usługą Azure AD, musisz dodać Shopify Plus z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Shopify Plus** w polu wyszukiwania.
1. Wybierz pozycję **Shopify Plus** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-shopify-plus"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Shopify Plus

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Shopify plus przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Shopify Plus.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Shopify Plus, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Shopify i logowanie JEDNOkrotne](#configure-shopify-plus-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Shopify Plus](#create-shopify-plus-test-user)** , aby uzyskać odpowiedni odpowiednik B. Simon w Shopify i połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **Shopify i** integracja aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:  `https://accounts.shopify.com/saml/consume/organization/<ORGANIZATION_ID>`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://shopify.plus/login`

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej Shopify Plus](mailto:plus-user-management@shopify.com) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja Shopify Plus oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, Shopify i aplikacja oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---- | --------------- |
    | poczta e-mail | user.mail |

1. Zmień format **identyfikatora nazwy** na **trwały**. Wybierz opcję **unikatowy identyfikator użytkownika (identyfikator nazwy)** , a następnie wybierz format **identyfikatora nazwy** . Wybierz opcję **persistent** dla tej opcji. Zapisz zmiany.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Shopify Plus.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Shopify Plus**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-shopify-plus-sso"></a>Konfigurowanie Shopify Plus Logowanie jednokrotne

Aby wyświetlić pełne kroki, zobacz [dokumentację programu Shopify na temat konfigurowania integracji protokołu SAML](https://help.shopify.com/en/manual/shopify-plus/saml).

Aby skonfigurować Logowanie jednokrotne na stronie **Shopify Plus** , skopiuj **adres URL metadanych federacji aplikacji** z Azure Active Directory. Następnie zaloguj się do [administratora organizacji](https://shopify.plus) i przejdź do pozycji **Użytkownicy**  >  **zabezpieczenia**. Wybierz pozycję **Skonfiguruj konfigurację**, a następnie wklej adres URL metadanych federacji aplikacji w sekcji **adres URL metadanych dostawcy tożsamości** . Wybierz pozycję **Dodaj** , aby ukończyć ten krok.

### <a name="create-shopify-plus-test-user"></a>Utwórz użytkownika testowego Shopify Plus

W tej sekcji utworzysz użytkownika o nazwie B. Simon w Shopify Plus. Wróć do sekcji **Użytkownicy** i Dodaj użytkownika, wprowadzając ich pocztę e-mail i uprawnienia. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="enforce-saml-authentication"></a>Wymuś uwierzytelnianie SAML

> [!NOTE]
> Zalecamy przetestowanie integracji przy użyciu poszczególnych użytkowników przed ich zastosowaniem.

Indywidualni użytkownicy:
1. Przejdź do strony pojedynczego użytkownika w Shopify Plus z domeną poczty e-mail, która jest zarządzana przez usługę Azure AD i zweryfikowaną w Shopify Plus.
1. W sekcji uwierzytelnianie SAML wybierz pozycję **Edytuj**, wybierz pozycję **wymagane**, a następnie wybierz pozycję **Zapisz**.
1. Sprawdź, czy ten użytkownik może pomyślnie zalogować się za pomocą przepływów inicjowanych przez dostawcy tożsamości i zainicjowanych przez program SP.

Dla wszystkich użytkowników w domenie poczty e-mail:
1. Wróć do strony **zabezpieczenia** .
1. Wybierz opcję **wymagane** dla ustawienia uwierzytelniania SAML. Wymusza to użycie protokołu SAML dla wszystkich użytkowników z tą domeną poczty e-mail w Shopify Plus.
1. Wybierz pozycję **Zapisz**.

> [!IMPORTANT]
> Włączenie protokołu SAML dla wszystkich użytkowników w domenie poczty e-mail ma wpływ na wszystkich użytkowników korzystających z tej aplikacji. Użytkownicy nie będą mogli się zalogować przy użyciu zwykłej strony logowania. Będą oni mogli uzyskiwać dostęp tylko do aplikacji za pomocą Azure Active Directory. Shopify nie udostępnia adresu URL logowania do kopii zapasowej, w którym użytkownicy mogą się logować przy użyciu swojej zwykłej nazwy użytkownika i hasła. W razie potrzeby można skontaktować się z pomocą techniczną Shopify.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Shopify Plus w panelu dostępu należy automatycznie zalogować się do Shopify Plus, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj usługę Shopify Plus z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chronić Shopify oraz zaawansowaną widoczność i kontrolki](/cloud-app-security/proxy-intro-aad)