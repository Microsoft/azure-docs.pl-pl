---
title: 'Samouczek: integracja SSO usługi Azure AD z inteligentnym globalnym nadzorem'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i inteligentnym globalnym nadzorem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: jeedes
ms.openlocfilehash: 5a2169db47ca5a6adcddbcc9558161370b896a62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997073"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smart-global-governance"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Smart Global ładu

W tym samouczku dowiesz się, jak zintegrować inteligentne globalne zarządzanie z Azure Active Directory (Azure AD). W przypadku integracji inteligentnego globalnego ładu z usługą Azure AD można:

* Użyj usługi Azure AD, aby kontrolować, kto może uzyskać dostęp do inteligentnego globalnego ładu.
* Zezwól użytkownikom na automatyczne logowanie się do inteligentnego globalnego ładu przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Inteligentna subskrypcja globalnego ładu z włączonym logowaniem jednokrotnym (SSO).

## <a name="tutorial-description"></a>Opis samouczka

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

Inteligentne zarządzanie globalne obsługuje logowanie jednokrotne zainicjowane przez usługę SP i dostawcy tożsamości.

Po skonfigurowaniu inteligentnego ładu globalnego można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji wykraczają poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-smart-global-governance-from-the-gallery"></a>Dodawanie inteligentnego globalnego ładu z galerii

Aby skonfigurować integrację inteligentnych globalnego ładu z usługą Azure AD, należy dodać inteligentne globalne ładu z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W lewym okienku wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wprowadź **inteligentne globalne zarządzanie** w polu wyszukiwania.
1. Wybierz pozycję **inteligentne globalne zarządzanie** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-smart-global-governance"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla inteligentnego globalnego ładu

Skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD za pomocą inteligentnego globalnego ładu przy użyciu użytkownika testowego o nazwie B. Simon. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i odpowiednim użytkownikiem w usłudze inteligentnego zarządzania globalnego.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą inteligentnego globalnego ładu, należy wykonać następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD.
    1. **[Przyznaj użytkownikowi testowemu](#grant-access-to-the-test-user)** uprawnienia umożliwiające użytkownikowi korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj inteligentne globalne zarządzanie logowaniem jednokrotnym](#configure-smart-global-governance-sso)** na stronie aplikacji.
    1. **[Utwórz globalnego użytkownika testowego ładu inteligentnego](#create-a-smart-global-governance-test-user)** jako odpowiedni dla reprezentacji usługi Azure AD użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne w usłudze Azure AD w Azure Portal:

1. W [Azure Portal](https://portal.azure.com/)na stronie **Smart globalna** integracja aplikacji ładu w sekcji **Zarządzanie** wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz przycisk ołówek dla **podstawowej konfiguracji SAML** , aby edytować ustawienia:

   ![Przycisk ołówek dla podstawowej konfiguracji języka SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przy użyciu dostawcy tożsamości, w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności.

    a. W polu **Identyfikator** wprowadź jeden z następujących adresów URL:

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/metadata`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/metadata`

    b. W polu **adres URL odpowiedzi** wprowadź jeden z następujących adresów URL:

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/acs`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/acs`

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowania programu SP, wybierz opcję **Ustaw dodatkowe adresy URL** i wykonaj następujące czynności.

   - W polu **adres URL logowania** wprowadź jeden z następujących adresów URL:

    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo`
    - `https://eu-fr-south.console.smartglobalprivacy.com/platform`

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz link **pobierania** dla **certyfikatu (RAW)** , aby pobrać certyfikat i zapisać go na komputerze:

    ![Link do pobierania certyfikatu](common/certificateraw.png)

1. W sekcji **Konfigurowanie inteligentnego ładu globalnego** skopiuj odpowiedni adres URL lub adresy URL zgodnie z wymaganiami:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B. Simon w Azure Portal.

1. W lewym okienku Azure Portal wybierz pozycję **Azure Active Directory**. Wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące czynności:
   1. W polu **Nazwa** wprowadź wartość **B. Simon**.  
   1. W polu **Nazwa użytkownika** wprowadź \<username> @ \<companydomain> . \<extension> . Na przykład `B.Simon@contoso.com`.
   1. Wybierz pozycję **Pokaż hasło**, a następnie Zapisz wartość wyświetlaną w polu **hasło** .
   1. Wybierz przycisk **Utwórz**.

### <a name="grant-access-to-the-test-user"></a>Udzielanie dostępu użytkownikowi testowemu

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie temu użytkownikowi dostępu do inteligentnego globalnego ładu.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **inteligentne zarządzanie globalne**.
1. Na stronie Przegląd aplikacji w sekcji **Zarządzanie** wybierz pozycję **Użytkownicy i grupy**:

   ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** :

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** na liście **Użytkownicy** , a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-smart-global-governance-sso"></a>Skonfiguruj inteligentne globalne zarządzanie logowaniem jednokrotnym

Aby skonfigurować Logowanie jednokrotne na stronie inteligentnego globalnego ładu, należy wysłać pobrany certyfikat pierwotny i odpowiednie adresy URL skopiowane z Azure Portal do [zespołu pomocy technicznej inteligentnego globalnego zarządzania](mailto:support.tech@smartglobal.com). Konfigurują połączenie SSO protokołu SAML, aby były poprawne po obu stronach.

### <a name="create-a-smart-global-governance-test-user"></a>Tworzenie inteligentnego użytkownika testowego globalnego ładu

Aby dodać użytkownika o nazwie B. Simon w inteligentnym globalnym ładu, należy skontaktować się z [zespołem pomocy technicznej inteligentnego ładu globalnego](mailto:support.tech@smartglobal.com) . Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przedstawiono Testowanie konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu inteligentnego globalnego kafelka ładu w panelu dostępu należy automatycznie zalogować się do inteligentnego globalnego wystąpienia ładu, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczki dotyczące integracji aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj inteligentne globalne zarządzanie przy użyciu usługi Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chronić inteligentne globalne zarządzanie za pomocą zaawansowanego wglądu i kontroli](/cloud-app-security/proxy-intro-aad)