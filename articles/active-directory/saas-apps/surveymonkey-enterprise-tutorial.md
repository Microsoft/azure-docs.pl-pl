---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą SurveyMonkey Enterprise | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i SurveyMonkey Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 8c14d7166beb3fd94a0cf20c94038c8e973a4942
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648360"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-surveymonkey-enterprise"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą SurveyMonkey Enterprise

W tym samouczku dowiesz się, jak zintegrować usługę SurveyMonkey Enterprise z usługą Azure Active Directory (Azure AD). W przypadku integracji usługi SurveyMonkey Enterprise z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do SurveyMonkey Enterprise.
* Zezwól użytkownikom na automatyczne logowanie do SurveyMonkey przedsiębiorstwa przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym w przedsiębiorstwie SurveyMonkey.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* SurveyMonkey Enterprise obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne.

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-surveymonkey-enterprise-from-the-gallery"></a>Dodaj SurveyMonkey Enterprise z galerii

Aby skonfigurować integrację usługi SurveyMonkey Enterprise w usłudze Azure AD, musisz dodać SurveyMonkey Enterprise z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SurveyMonkey Enterprise** w polu wyszukiwania.
1. Wybierz pozycję **SurveyMonkey Enterprise** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-surveymonkey-enterprise"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla usługi SurveyMonkey Enterprise

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą SurveyMonkey przedsiębiorstwa przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w SurveyMonkey Enterprise.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą SurveyMonkey Enterprise, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne w przedsiębiorstwie SurveyMonkey](#configure-surveymonkey-enterprise-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego SurveyMonkey Enterprise](#create-surveymonkey-enterprise-test-user)** , aby dysponować odpowiednikiem B. Simon w SurveyMonkey Enterprise, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja z aplikacją **SurveyMonkey Enterprise** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** aplikacja została wstępnie skonfigurowana i wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz** .

1. Aplikacja SurveyMonkey Enterprise oczekuje potwierdzenia SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/edit-attribute.png)

6. Oprócz powyższych SurveyMonkey aplikacja dla przedsiębiorstw oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | E-mail | user.mail |
    | FirstName (Imię) | user.givenname |
    | LastName (Nazwisko) | user.surname |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie programu SurveyMonkey Enterprise** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do przedsiębiorstwa SurveyMonkey.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SurveyMonkey Enterprise**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-surveymonkey-enterprise-sso"></a>Konfigurowanie logowania jednokrotnego w przedsiębiorstwie SurveyMonkey

Aby skonfigurować Logowanie jednokrotne na **SurveyMonkey** po stronie przedsiębiorstwa, musisz wysłać pobrany **XML metadanych Federacji** i odpowiednie skopiowane adresy URL z Azure Portal do [zespołu pomocy technicznej SurveyMonkey Enterprise](mailto:support@selerix.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-surveymonkey-enterprise-test-user"></a>Utwórz użytkownika testowego SurveyMonkey Enterprise

Nie jest konieczne tworzenie użytkownika testowego w SurveyMonkey Enterprise. Konta użytkowników będą inicjowane, jeśli użytkownik zdecyduje się na utworzenie nowego konta na podstawie potwierdzenia SAML. Menedżer sukcesu dla klienta w przedsiębiorstwie SurveyMonkey będzie udostępniał kroki umożliwiające wykonanie tego procesu po dodaniu metadanych platformy Azure do konfiguracji przedsiębiorstwa SurveyMonkey i gotowe do zweryfikowania.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do przedsiębiorstwa SurveyMonkey, dla którego skonfigurowano Logowanie jednokrotne.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka SurveyMonkey Enterprise w obszarze Moje aplikacje należy automatycznie zalogować się do przedsiębiorstwa SurveyMonkey, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu SurveyMonkey Enterprise można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).