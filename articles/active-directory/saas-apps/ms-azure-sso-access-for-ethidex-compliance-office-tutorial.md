---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą MS Azure SSO Access dla pakietu Office Ethidex zgodności™ | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i dostępem SSO MS Azure dla usługi Ethidex zgodności z pakietem Office™.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: jeedes
ms.openlocfilehash: 7ef219ca147fe96fc65f14bbf3ba6a565adc95ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92520918"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z dostępem jednokrotnym do usługi MS Azure dla pakietu Office Ethidexe™

W ramach tego samouczka nauczysz się zintegrować z usługą Microsoft Azure Logowanie jednokrotne w usłudze Ethidex, aby uzyskać™ zgodność z usługą Azure Active Directory (Azure AD). W przypadku integrowania dostępu SSO MS Azure dla usługi Ethidex o™ zgodności z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi MS Azure SSO Access for Ethidex, Office™.
* Umożliwienie użytkownikom automatycznego logowania się do usługi MS Azure SSO Access for Ethidex w™ pakiecie Office z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Dostęp do logowania jednokrotnego na platformie Microsoft Azure dla pakietu™ Office Ethidex zgodności z włączoną obsługą logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Dostęp do usługi MS Azure SSO dla programu Ethidex w pakiecie Office™ obsługuje **dostawcy tożsamości** zainicjowane Logowanie jednokrotne

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>Dodawanie dostępu SSO MS Azure do usługi Ethidex zgodności z pakietem Office™ z galerii

Aby skonfigurować integrację dostępu SSO MS Azure dla usługi Ethidex zgodności z pakietem™ Office w usłudze Azure AD, musisz dodać usługę MS Azure SSO Access dla pakietu Office Ethidex™ z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **MS Azure SSO Access for Ethidex — zgodność z pakietem Office™** w polu wyszukiwania.
1. Wybierz pozycję **MS Azure SSO Access for Ethidex,™** z poziomu panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla dostępu SSO MS Azure dla usługi Ethidex, aby uzyskać zgodność z pakietem Office™

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD przy użyciu logowania jednokrotnego MS Azure dla programu™ Ethidex, używając użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze MS Azure SSO Access for™ Ethidex.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu logowania jednokrotnego MS Azure dla programu™ Ethidex, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Konfigurowanie dostępu SSO MS Azure dla usługi Ethidex zgodności z logowaniem](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** jednokrotnym Office — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz dostęp do usługi MS Azure SSO dla użytkownika testowego Ethidex zgodności](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** , aby uzyskać odpowiedni odpowiednik B. Simon w usłudze MS Azure SSO Access for Ethidex Office™, która jest połączona z reprezentacją usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z **usługą MS Azure logowania jednokrotnego dla programu™ Ethidex** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `com.ethidex.prod.<CLIENTID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://www.ethidex.com/saml2/sp/acs/<CLIENTID>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [techniczną usługi MS Azure logowania jednokrotnego dla programu™ Ethidex](mailto:support@ethidex.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja Microsoft Azure SSO Access dla programu Ethidex zgodność aplikacji pakietu™ Office oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Usługa MS Azure SSO Access for Ethidex — aplikacja pakietu™ Office oczekuje na zamapowanie **NameIdentifier** z **użytkownikiem. mail**, dlatego należy edytować Mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutu.

    ![image (obraz)](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (RAW)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

1. W sekcji **konfigurowanie™ pakietu Office Logowanie jednokrotne w usłudze MS Azure dla usługi Ethidex** należy skopiować odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi MS Azure SSO dostęp dla™ pakietu Office dla zgodności Ethidex.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **MS Azure SSO Access for Ethidex, aby uzyskać zgodność z pakietem Office™**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>Konfigurowanie dostępu SSO MS Azure dla usługi Ethidex zgodności z logowaniem jednokrotnym pakietu Office

Aby skonfigurować Logowanie jednokrotne w usłudze **MS Azure SSO Access for™ Ethidex** , należy wysłać pobrany **certyfikat (RAW)** i odpowiednie skopiowane adresy URL z Azure Portal do [usługi MS Azure SSO Access dla programu Ethidex zgodność z usługą Office™ pomoc techniczna](mailto:support@ethidex.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>Utwórz dostęp do usługi MS Azure SSO dla użytkownika testowego Ethidex zgodności z pakietem Office

W tej sekcji utworzysz użytkownika o nazwie B. Simon w usłudze MS Azure SSO Access for Ethidex, aby uzyskać zgodność z pakietem Office™. Współdziałanie z usługą [MS Azure SSO Access for Ethidex — zespół pomocy technicznej™ pakietu Office](mailto:support@ethidex.com) , aby dodać użytkowników w usłudze MS Azure SSO Access for Ethidex™ platformę Office. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi Microsoft Azure SSO dostęp dla programu Ethidex dla™ pakietu Office w panelu dostępu należy automatycznie zalogować się do usługi MS Azure SSO dostęp do programu Ethidex™, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj usługę Microsoft Azure SSO Access dla programu Ethidex, aby uzyskać zgodność z™ pakietem Office w usłudze Azure AD](https://aad.portal.azure.com/)