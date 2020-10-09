---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z logowaniem jednokrotnym dla Skytap | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i logowanie jednokrotne dla Skytap.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.openlocfilehash: 8d34ca8ed01144ee282f6411640894807a09ef08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88527880"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z logowaniem jednokrotnym dla Skytap

W tym samouczku dowiesz się, jak zintegrować Logowanie jednokrotne dla Skytap z usługą Azure Active Directory (Azure AD). Po zintegrowaniu logowania jednokrotnego w usłudze Skytap z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do logowania jednokrotnego dla Skytap.
* Zezwól użytkownikom na automatyczne logowanie do rejestracji Jednokrotnej dla Skytap przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji, Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji oprogramowania jako usługi (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Logowanie jednokrotne dla subskrypcji z włączonym logowaniem jednokrotnym (SSO) usługi Skytap.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Logowanie jednokrotne do usługi Skytap obsługuje usługę SP i dostawcy tożsamości zainicjowano Logowanie jednokrotne.
* Po skonfigurowaniu logowania jednokrotnego dla Skytap można wymusić kontrolę sesji. Chroni to eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>Dodaj Logowanie jednokrotne do Skytap z galerii

Aby skonfigurować integrację logowania jednokrotnego dla Skytap w usłudze Azure AD, musisz dodać Logowanie jednokrotne do Skytap z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz Logowanie jednokrotne **dla Skytap** w polu wyszukiwania.
1. Wybierz pozycję **Logowanie jednokrotne dla Skytap** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby logowania jednokrotnego dla Skytap

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego dla Skytap przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić połączoną relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ramach rejestracji Jednokrotnej dla Skytap.

Poniżej przedstawiono ogólne kroki konfigurowania i testowania logowania jednokrotnego w usłudze Azure AD za pomocą logowania jednokrotnego dla Skytap:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.

    a. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.

    b. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie](#configure-single-sign-on-for-skytap-sso)** jednokrotne na potrzeby logowania jednokrotnego w usłudze Skytap, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.

    a. **[Utwórz Logowanie jednokrotne dla użytkownika testowego Skytap](#create-single-sign-on-for-skytap-test-user)** , aby dysponować odpowiednikiem B. Simon w przypadku rejestracji Jednokrotnej dla Skytap. Ten odpowiednik jest połączony z reprezentacją usługi Azure AD użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie "Logowanie jednokrotne na potrzeby integracji aplikacji **Skytap"** Znajdź sekcję **Zarządzanie** . Wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający Konfigurowanie logowania jednokrotnego przy użyciu strony SAML z wyróżnioną ikoną ołówka](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, który używa następującego wzorca: `http://pingone.com/<custom EntityID>`

    b. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, który używa następującego wzorca: `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Wybierz opcję **Ustaw dodatkowe adresy URL**i wykonaj następujące kroki, aby skonfigurować aplikację w trybie zainicjowania programu **SP** :

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, który używa następującego wzorca: `https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. W polu tekstowym **stan przekaźnika** wpisz adres URL, który używa następującego wzorca: `https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adresu URL odpowiedzi, adresu URL logowania i stanu przekazywania. Aby uzyskać te wartości, skontaktuj się z [zespołem obsługi klienta usługi Skytap](mailto:support@skytap.com) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji**. Wybierz pozycję **Pobierz** , aby pobrać plik metadanych i zapisać go na komputerze.

    ![Zrzut ekranu przedstawiający link pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie logowania jednokrotnego dla Skytap** skopiuj odpowiedni adres URL lub adresy URL na podstawie wymagań.

    ![Zrzut ekranu przedstawiający adresy URL konfiguracji kopiowania](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** , a następnie Zapisz wartość, która jest wyświetlana w polu **hasło** .
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do logowania jednokrotnego dla Skytap.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Logowanie jednokrotne dla Skytap**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Zrzut ekranu strony użytkownicy i grupy z wyróżnioną pozycją Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy. Następnie wybierz przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że jakakolwiek wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Następnie wybierz przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-single-sign-on-for-skytap-sso"></a>Konfigurowanie logowania jednokrotnego na potrzeby logowania jednokrotnego w usłudze Skytap

Aby skonfigurować Logowanie jednokrotne przy użyciu logowania jednokrotnego dla usługi Skytap, musisz wysłać pobrany **XML metadanych Federacji**oraz odpowiednie skopiowane adresy url z Azure Portal do rejestracji jednokrotnej [dla zespołu obsługi klienta Skytap](mailto:support@skytap.com). Ustawi on to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Utwórz Logowanie jednokrotne dla użytkownika testowego Skytap

W tej sekcji utworzysz użytkownika o nazwie B. Simon w przypadku logowania jednokrotnego dla Skytap. Aby dodać użytkowników w ramach rejestracji Jednokrotnej dla platformy Skytap, należy skontaktować się z [zespołem obsługi klienta Skytap](mailto:support@skytap.com) . Nie można korzystać z logowania jednokrotnego do momentu utworzenia i aktywowania użytkowników.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka Logowanie jednokrotne do Skytap w panelu dostępu należy automatycznie zalogować się do logowania jednokrotnego dla Skytap, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Azure AD](https://aad.portal.azure.com/)

