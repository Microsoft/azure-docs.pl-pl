---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z pulpitem nawigacyjnym Zliczanie do operacji | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i liczeniem na pulpicie nawigacyjnym operacji.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/30/2020
ms.author: jeedes
ms.openlocfilehash: 3339516193af6e1ff832ac586f4a81f8799c5b83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727681"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-count-me-in---operations-dashboard"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z pulpitem nawigacyjnym Count in Operations

W tym samouczku dowiesz się, jak zintegrować z pulpitem nawigacyjnym zliczanie w ramach operacji za pomocą Azure Active Directory (Azure AD). Gdy integruję się z pulpitem nawigacyjnym "zliczanie do operacji" przy użyciu usługi Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do zliczania na pulpicie nawigacyjnym operacji.
* Zezwól użytkownikom na automatyczne logowanie się, aby zalogować się do pulpitu nawigacyjnego w ramach operacji przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Zlicz subskrypcję z włączoną funkcją rejestracji jednokrotnej (SSO) pulpitu nawigacyjnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* **Obsługa logowania** jednokrotnego na pulpicie nawigacyjnym operacji

## <a name="adding-count-me-in---operations-dashboard-from-the-gallery"></a>Dodawanie do pulpitu nawigacyjnego Zliczanie do operacji z galerii

Aby skonfigurować integrację z pulpitem nawigacyjnym zliczanie danych w ramach operacji w usłudze Azure AD, musisz dodać pulpit nawigacyjny z obsługą liczby operacji z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **Zlicz ja** .
1. Wybierz pozycję **Zliczanie do usługi w obszarze Pulpit nawigacyjny operacje** , a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-count-me-in---operations-dashboard"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD na pulpicie nawigacyjnym zliczanie operacji

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą funkcji count Me na pulpicie nawigacyjnym z użyciem użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD a pokrewnym użytkownikiem w polu Zlicz mi na pulpicie nawigacyjnym.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą programu "Count Me" na pulpicie nawigacyjnym, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj funkcję Logowanie jednokrotne In-Operations pulpitu nawigacyjnego](#configure-count-me-in-operations-dashboard-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz usługę Count me In-Operations użytkownika testowego pulpitu nawigacyjnego](#create-count-me-in-operations-dashboard-test-user)** , aby uzyskać odpowiednik elementu B. Simon w ramach liczby operacji na pulpicie nawigacyjnym usługi Azure AD, który jest połączony z reprezentacją użytkownika w usłudze Active Directory.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie z integracją **z aplikacją pulpitu nawigacyjnego Count in Operations** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://api-us.localz.io/user/v1/saml/initsso?projectId=<PROJECT_ID>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `api-us.localz.io/<PROJECT_ID>`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://api-us.localz.io/user/v1/saml/initsso?projectId=<PROJECT_ID>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej do obsługi klienta na pulpicie nawigacyjnym w ramach operacji](mailto:support@localz.co) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja pulpitu nawigacyjnego "Count Me" oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, zliczanie do aplikacji pulpitów nawigacyjnych w operacjach oczekuje, że kilka atrybutów do przesłania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.
    
    | Nazwa |  Atrybut źródłowy|
    | ----------- | --------- |
    | przypisane role | user.assignedroles |

    > [!NOTE]
    > Aby uzyskać role dla użytkowników przypisanych do aplikacji, należy policzyć na pulpit nawigacyjny operacji. Skonfiguruj te role w usłudze Azure AD, aby użytkownicy mogli przypisywać odpowiednie role. Aby dowiedzieć się, jak skonfigurować role w usłudze Azure AD, zobacz [tutaj](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. Na stronie **Konfigurowanie pulpitu nawigacyjnego Zliczanie do operacji** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do usługi na pulpicie nawigacyjnym operacje.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Zlicz mi na pulpicie nawigacyjnym operacje**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-count-me-in-operations-dashboard-sso"></a>Skonfiguruj usługę Count Me In-Operations Logowanie jednokrotne pulpitu nawigacyjnego

Aby skonfigurować Logowanie jednokrotne na stronie z **pulpitem nawigacyjnym Zliczanie do operacji** , musisz wysłać pobrany **certyfikat (base64)** i odpowiednie skopiowane adresy URL z programu Azure Portal, aby [policzyć do zespołu pomocy technicznej pulpit nawigacyjny operacji](mailto:support@localz.co). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-count-me-in-operations-dashboard-test-user"></a>Utwórz użytkownika testowego In-Operations

W tej sekcji utworzysz użytkownika o nazwie Britta Simon na pulpicie nawigacyjnym Counting Me in-Operations. Współpraca z [zespołem pomocy technicznej na pulpicie nawigacyjnym operacje](mailto:support@localz.co) dodawania użytkowników do platformy pulpitu nawigacyjnego Count in Operations. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie na adres URL logowania pulpitu nawigacyjnego operacji, w którym można zainicjować przepływ logowania. 

* Przejdź do strony Zliczaj mnie w obszarze adres URL logowania na pulpicie nawigacyjnym, a następnie zainicjuj w tym miejscu przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu w obszarze Moje aplikacje kafelka pulpit nawigacyjny Zliczanie do operacji zostanie przekierowany na adres URL logowania na pulpicie nawigacyjnym operacji. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu na pulpicie nawigacyjnym Zliczanie do operacji można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
