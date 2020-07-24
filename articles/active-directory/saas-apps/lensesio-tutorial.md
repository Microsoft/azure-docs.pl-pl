---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Lenses.io | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Lenses.io.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2a0d4a7c-a171-48c6-b1c1-f2bd728fb37f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2b630111261be8e3615ab45e95633040e799551
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050994"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z portalem Lenses.io DataOps.

W tym samouczku dowiesz się, jak zintegrować Portal [lenses.IO](https://lenses.io/) DataOps z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Lenses.io z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do portalu Lenses.io.
* Zezwól użytkownikom na automatyczne logowanie do soczewek z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Wystąpienie portalu soczewek. Portal soczewek można wdrożyć na [różne sposoby](https://lenses.io/product/deployment/).
* [Licencja](https://lenses.io/product/pricing/) lenses.IO, która obsługuje logowanie jednokrotne (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Lenses.io obsługuje logowanie jednokrotne w usłudze **SP**

* Po skonfigurowaniu Lenses.io można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-lensesio-from-the-gallery"></a>Dodawanie Lenses.io z galerii

Aby skonfigurować integrację programu Lenses.io z usługą Azure AD, musisz dodać Lenses.io z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **lenses.IO** w polu wyszukiwania.
1. Wybierz pozycję **lenses.IO** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Lenses.io

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą portalu Lenses.io przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Lenses.io.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Lenses.io, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD i grupę](#create-an-azure-ad-test-user-and-group)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-lensesio-sso)** w usłudze lenses.IO, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz uprawnienia grupy testowej lenses.IO](#create-lensesio-test-group-permissions)** — aby kontrolować, co B. Simon powinno mieć dostęp w lenses.IO (autoryzacja).
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **lenses.IO** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: na `https://<CUSTOMER_LENSES_BASE_URL>` przykład.`https://lenses.my.company.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: na `https://<CUSTOMER_LENSES_BASE_URL>` przykład.`https://lenses.my.company.com`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client`
    tj.`https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości z rzeczywistym adresem URL logowania, adresem URL i identyfikatorem odpowiedzi na podstawie podstawowego adresu URL wystąpienia portalu soczewek. Więcej informacji można znaleźć w [dokumentacji rejestracji jednokrotnej lenses.IO](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0).

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **konfigurowanie lenses.IO** Użyj pliku XML powyżej, aby skonfigurować soczewki dla logowania jednokrotnego na platformie Azure.

### <a name="create-an-azure-ad-test-user-and-group"></a>Tworzenie użytkownika i grupy testowej usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon. Utworzysz również grupę testową dla B. Simon, która będzie używana do kontrolowania, jaki jest dostęp B. Simon w soczewki.
Aby dowiedzieć się, jak soczewki używa mapowania członkostwa w grupie na potrzeby autoryzacji w [dokumentacji rejestracji jednokrotnej w soczewki](https://docs.lenses.io/install_setup/configuration/security.html#id3)

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

Aby utworzyć grupę:
1. Wróć do **Azure Active Directory**, wybierz **grupy**
1. Wybierz pozycję **Nowa grupa** w górnej części ekranu.
1. We **właściwościach grupy**wykonaj następujące kroki:
   1. W polu **Typ grupy** wybierz opcję `Security` .
   1. W polu **Nazwa grupy** wpisz`LensesUsers`
   1. Kliknij pozycję **Utwórz**.
1. Wybierz grupę `LensesUsers` i zanotuj **Identyfikator obiektu** (np. `f8b5c1ec-45de-4abd-af5c-e874091fb5f7` ). Ten identyfikator będzie używany w Soczewkiach w celu zamapowania użytkowników tej grupy na odpowiednie [uprawnienia](https://docs.lenses.io/install_setup/configuration/security.html#id3).  
   
Aby przypisać grupę do użytkownika testowego: 
1. Wróć do **Azure Active Directory**, wybierz pozycję **Użytkownicy**.
1. Wybierz użytkownika testowego `B.Simon` .
1. Wybierz pozycję **grupy**.
1. Wybierz pozycję **Dodaj członkostwa** w górnej części ekranu.
1. Wyszukaj `LensesUsers` i wybierz ją.
1. Kliknij pozycję **Wybierz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Lenses.io.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **lenses.IO**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-lensesio-sso"></a>Konfigurowanie logowania jednokrotnego Lenses.io

Aby skonfigurować Logowanie jednokrotne w portalu **lenses.IO** , należy zainstalować pobrany **XML metadanych Federacji** w wystąpieniu soczewki i [skonfigurować soczewki, aby włączyć logowanie jednokrotne](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses). 

### <a name="create-lensesio-test-group-permissions"></a>Utwórz uprawnienia grupy testowej Lenses.io

W tej sekcji utworzysz grupę w soczewki przy użyciu **identyfikatora obiektu** `LensesUsers` grupy zanotowanej w [sekcji Tworzenie](#create-an-azure-ad-test-user-and-group)użytkownika.
Przypisujesz odpowiednie uprawnienia, które `B.Simon` powinny mieć wbudowane soczewki.
Więcej informacji można znaleźć na stronie [Mapowanie grup soczewek platformy Azure](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Lenses.io w panelu dostępu należy automatycznie zalogować się do portalu usługi Lenses.io, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Konfigurowanie logowania jednokrotnego w wystąpieniu programu Lenses.io](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Lenses.io z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić Lenses.io z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
