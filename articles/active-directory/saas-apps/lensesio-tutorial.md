---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Lenses.io | Microsoft Docs'
description: W tym samouczku dowiesz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Lenses.io.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 181d58baf128c4848a538e776aea0e43213994dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458612"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z portalem Lenses.io DataOps

W tym samouczku dowiesz się, jak zintegrować Portal [lenses.IO](https://lenses.io/) DataOps z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Lenses.io z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do portalu Lenses.io.
* Zezwól użytkownikom na automatyczne logowanie do soczewek z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji oprogramowania jako usługi (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne w usłudze Azure AD](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Wystąpienie portalu soczewek. Można wybrać jedną z wielu [opcji wdrażania](https://lenses.io/product/deployment/).
* [Licencja](https://lenses.io/product/pricing/) lenses.IO, która obsługuje logowanie jednokrotne (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Lenses.io obsługuje logowanie jednokrotne przez dostawcę usług (SP).

* Po skonfigurowaniu Lenses.io można wymusić kontrolę sesji. Kontrola sesji chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-lensesio-from-the-gallery"></a>Dodaj Lenses.io z galerii

Aby skonfigurować integrację Lenses.io z usługą Azure AD, Dodaj Lenses.io do listy zarządzanych aplikacji SaaS:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wprowadź **lenses.IO** w polu wyszukiwania.
1. W panelu wyników wybierz pozycję **lenses.IO**, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Lenses.io

Utworzysz użytkownika testowego o nazwie *B. Simon* , aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą portalu lenses.IO. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Lenses.io.

Wykonaj poniższe czynności:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Utwórz użytkownika i grupę testową usługi Azure AD](#create-an-azure-ad-test-user-and-group) w celu przetestowania logowania jednokrotnego usługi Azure AD za pomocą B. Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. [Skonfiguruj Logowanie jednokrotne](#configure-lensesio-sso) w usłudze lenses.IO, aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. [Utwórz uprawnienia grupy testowej lenses.IO](#create-lensesio-test-group-permissions) , aby kontrolować, co B. Simon może uzyskać dostęp w lenses.IO (autoryzacja).
1. [Przetestuj Logowanie jednokrotne](#test-sso) , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne w usłudze Azure AD w Azure Portal:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **lenses.IO** Znajdź sekcję **Zarządzanie** , a następnie wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Zrzut ekranu pokazujący ikonę edytowania podstawowej konfiguracji języka SAML.](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości w następujących polach tekstowych:

    a. **Adres URL logowania**: wprowadź adres URL, który ma następujący wzorzec: `https://<CUSTOMER_LENSES_BASE_URL>` . Może to być na przykład `https://lenses.my.company.com`.

    b. **Identyfikator (identyfikator jednostki)**: wprowadź adres URL, który ma następujący wzorzec: `https://<CUSTOMER_LENSES_BASE_URL>` . Może to być na przykład `https://lenses.my.company.com`.

    c. **Adres URL odpowiedzi**: wprowadź adres URL, który ma następujący wzorzec: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client` . Może to być na przykład `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`.

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je za pomocą rzeczywistego adresu URL logowania, adresu URL odpowiedzi i identyfikatora podstawowego adresu URL wystąpienia portalu soczewek. Aby uzyskać więcej informacji, zobacz [dokumentację usługi SSO lenses.IO](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0) .

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** przejdź do sekcji **certyfikat podpisywania SAML** . Znajdź **plik XML metadanych Federacji**, a następnie wybierz pozycję **Pobierz** , aby pobrać i zapisać certyfikat na komputerze.

    ![Zrzut ekranu przedstawiający link pobierania certyfikatu.](common/metadataxml.png)

1. W sekcji **konfiguracja lenses.IO** Użyj pobranego pliku XML w celu skonfigurowania soczewek dla logowania jednokrotnego na platformie Azure.

### <a name="create-an-azure-ad-test-user-and-group"></a>Tworzenie użytkownika i grupy testowej usługi Azure AD

W Azure Portal utworzysz użytkownika testowego o nazwie B. Simon. Następnie utworzysz grupę testową, która kontroluje dostęp B. Simon w soczewki.

Aby dowiedzieć się, jak soczewki korzysta z mapowania członkostwa w grupie na potrzeby autoryzacji w [dokumentacji logowania jednokrotnego](https://docs.lenses.io/install_setup/configuration/security.html#id3).

**Aby utworzyć użytkownika testowego:**

1. W lewym okienku Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. W górnej części ekranu wybierz pozycję **nowy użytkownik**.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość **B. Simon**.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension . Na przykład B.Simon@contoso.com.
   1. Zaznacz pole wyboru **Pokaż hasło** . Zapisz hasło, które jest wyświetlane w polu **hasło** .
   1. Wybierz przycisk **Utwórz**.

**Aby utworzyć grupę:**

1. Przejdź do **Azure Active Directory**, a następnie wybierz pozycję **grupy**.
1. W górnej części ekranu wybierz pozycję **Nowa grupa**.
1. We **właściwościach grupy** wykonaj następujące kroki:
   1. W polu **Typ grupy** wybierz pozycję **zabezpieczenia**.
   1. W polu **Nazwa grupy** wpisz **LensesUsers**.
   1. Wybierz przycisk **Utwórz**.
1. Wybierz grupę **LensesUsers** i skopiuj **Identyfikator obiektu** (na przykład f8b5c1ec-45de-4abd-af5c-e874091fb5f7). Ten identyfikator zostanie użyty w soczewek, aby zamapować użytkowników grupy na odpowiednie [uprawnienia](https://docs.lenses.io/install_setup/configuration/security.html#id3).  

**Aby przypisać grupę do użytkownika testowego:**

1. Przejdź do **Azure Active Directory**, a następnie wybierz pozycję **Użytkownicy**.
1. Wybierz użytkownika testowego **B. Simon**.
1. Wybierz pozycję **grupy**.
1. W górnej części ekranu wybierz pozycję **Dodaj członkostwa**.
1. Wyszukaj i wybierz pozycję **LensesUsers**.
1. Kliknij pozycję **Wybierz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Lenses.io.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **lenses.IO**.
1. Na stronie Przegląd aplikacji w sekcji **Zarządzanie** wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu pokazujący łącze "Użytkownicy i grupy".](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**.

   ![Zrzut ekranu przedstawiający link Dodaj użytkownika.](common/add-assign-user.png)

1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy. Następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że jakakolwiek wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz przycisk **Przypisz** .

## <a name="configure-lensesio-sso"></a>Konfigurowanie logowania jednokrotnego Lenses.io

Aby skonfigurować Logowanie jednokrotne w portalu **lenses.IO** , zainstaluj pobrany **XML metadanych Federacji** w wystąpieniu soczewki i [Skonfiguruj soczewki, aby włączyć logowanie jednokrotne](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses).

### <a name="create-lensesio-test-group-permissions"></a>Utwórz uprawnienia grupy testowej Lenses.io

1. Aby utworzyć grupę w soczewki, użyj **identyfikatora obiektu** grupy **LensesUsers** . Jest to identyfikator skopiowany w [sekcji Tworzenie](#create-an-azure-ad-test-user-and-group)użytkownika.
1. Przypisz odpowiednie uprawnienia dla B. Simon.

Aby uzyskać więcej informacji, zobacz [Mapowanie grup na platformie Azure](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji Przetestuj konfigurację rejestracji jednokrotnej usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka Lenses.io w panelu dostępu należy automatycznie zalogować się do portalu Lenses.io. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Konfigurowanie logowania jednokrotnego w wystąpieniu programu Lenses.io](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure AD](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne w usłudze Azure AD?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure AD?](../conditional-access/overview.md)

- [Wypróbuj Lenses.io z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chronić Lenses.io z zaawansowaną widocznością i kontrolkami](/cloud-app-security/proxy-intro-aad)