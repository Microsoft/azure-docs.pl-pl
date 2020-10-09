---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą ContractSafe SAML2 — Logowanie jednokrotne | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i ContractSafe SAML2 Logowanie jednokrotne.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.openlocfilehash: 9459d13e8943f7e9177d2ad493873257f46f6649
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88544395"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>Samouczek: integracja Azure Active Directory logowania jednokrotnego (SSO) z usługą ContractSafe SAML2 Logowanie jednokrotne

W tym samouczku dowiesz się, jak zintegrować Logowanie jednokrotne w usłudze ContractSafe SAML2 z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi ContractSafe SAML2 z logowaniem jednokrotnym w usłudze Azure AD można:

* Kontrolowanie, kto ma dostęp do usługi ContractSafe SAML2 SSO w usłudze Azure AD.
* Zezwól użytkownikom na automatyczne logowanie do usługi ContractSafe SAML2 Logowanie jednokrotne przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji oprogramowania jako usługi (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne będą następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* ContractSafe SAML2 rejestracji jednokrotnej z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. ContractSafe SAML2 Logowanie jednokrotne obsługuje logowanie jednokrotne zainicjowane przez usługę **dostawcy tożsamości**.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>Dodaj Logowanie jednokrotne do usługi ContractSafe SAML2 z galerii

Aby skonfigurować integrację logowania jednokrotnego w usłudze ContractSafe SAML2 z usługą Azure AD, musisz dodać Logowanie jednokrotne SAML2 ContractSafe z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **ContractSafe SAML2 SSO** w polu wyszukiwania.
1. Wybierz pozycję **ContractSafe SAML2 SSO** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla ContractSafe SAML2 Logowanie jednokrotne

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą ContractSafe SAML2 SSO przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze ContractSafe SAML2 SSO.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w usłudze ContractSafe SAML2, wykonaj następujące bloki konstrukcyjne:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.
   * [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) w celu przetestowania rejestracji jednokrotnej usługi Azure AD przy użyciu konta **B. Simon** .
   * [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić usłudze **B. Simon** korzystanie z logowania jednokrotnego w usłudze Azure AD.

1. [Skonfiguruj Logowanie jednokrotne w usłudze ContractSafe SAML2](#configure-contractsafe-saml2-sso) , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
   * [Utwórz użytkownika testowego logowania jednokrotnego usługi ContractSafe SAML2](#create-a-contractsafe-saml2-sso-test-user) w celu posiadania odpowiedników **B. Simon** w ContractSafe SAML2 SSO, które są połączone z reprezentacją usługi Azure AD.
2. [Przetestuj Logowanie jednokrotne](#test-sso) , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne w usłudze Azure AD w Azure Portal:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **ContractSafe SAML2 z logowaniem jednokrotnym** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz ikonę Edytuj (pióro), aby określić **podstawową konfigurację języka SAML** w celu edytowania ustawień.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wprowadź następujące wartości w odpowiednich polach:

    a. W polu tekstowym **Identyfikator** wprowadź adres URL, używając następującego formatu: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. W polu tekstowym **adres URL odpowiedzi** wprowadź adres URL, używając następującego formatu: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > To nie są rzeczywiste wartości. Należy je zastąpić rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem obsługi klienta rejestracji jednokrotnej ContractSafe SAML2](mailto:support@contractsafe.com) . Można również odnieść się do formatów przedstawionych w sekcji podstawowe informacje o **konfiguracji SAML** w Azure Portal.

1. ContractSafe SAML2 Logowanie jednokrotne oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Wspólne atrybuty domyślne](common/default-attributes.png)

1. Oprócz atrybutów domyślnych aplikacja ContractSafe SAML2 SSO oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML. Te atrybuty są wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami. Poniższa lista zawiera dodatkowe atrybuty.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | adres e-mail | user.userprincipalname |
    | poczta e-mail | User. onpremisesuserprincipalname |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji**. Wybierz pozycję **Pobierz** , aby pobrać certyfikat, a następnie zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie ContractSafe SAML2 SSO** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie **B. Simon**.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**. Wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź adres e-mail w `username@companydomain.extension` formacie. Może to być na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Wybierz przycisk **Utwórz**.

## <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz funkcję **B. Simon** do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi CONTRACTSAFE SAML2 SSO.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **ContractSafe SAML2 SSO**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** , a następnie wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

   ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy **Użytkownicy** . Następnie wybierz przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że jakakolwiek wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Następnie wybierz przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz przycisk **Przypisz** .

## <a name="configure-contractsafe-saml2-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze ContractSafe SAML2

Aby skonfigurować Logowanie jednokrotne na stronie logowania jednokrotnego w usłudze **ContractSafe SAML2** , należy wysłać pobrany **kod XML metadanych Federacji** i odpowiednie skopiowane adresy URL z Azure Portal do [zespołu pomocy technicznej rejestracji jednokrotnej ContractSafe SAML2](mailto:support@contractsafe.com). Zespół jest odpowiedzialny za prawidłowe skonfigurowanie połączenia SSO protokołu SAML na obu stronach.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>Tworzenie użytkownika testowego logowania jednokrotnego w usłudze ContractSafe SAML2

Utwórz użytkownika o nazwie B. Simon w usłudze ContractSafe SAML2 SSO. Współpracuj z [zespołem obsługi rejestracji jednokrotnej ContractSafe SAML2](mailto:support@contractsafe.com) , aby dodać użytkowników na platformie CONTRACTSAFE SAML2 SSO. Przed użyciem logowania jednokrotnego należy utworzyć i aktywować użytkowników.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

Przetestuj konfigurację rejestracji jednokrotnej usługi Azure AD za pomocą panelu dostępu. Po wybraniu kafelka SSO usługi ContractSafe SAML2 w panelu dostępu należy automatycznie zalogować się do usługi ContractSafe SAML2 SSO, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę ContractSafe SAML2 Logowanie jednokrotne w usłudze Azure AD](https://aad.portal.azure.com/)
