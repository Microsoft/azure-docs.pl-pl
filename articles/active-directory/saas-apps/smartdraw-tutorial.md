---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą SmartDraw | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i SmartDraw.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: 46b4387252be7e0b76f6e0733d6fd4f95d39e2f0
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125956"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smartdraw"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą SmartDraw

W tym samouczku dowiesz się, jak zintegrować usługę SmartDraw z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi SmartDraw z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do SmartDraw.
* Zezwól użytkownikom na automatyczne logowanie się do usługi SmartDraw przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) SmartDraw.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* SmartDraw obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* SmartDraw obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-smartdraw-from-the-gallery"></a>Dodawanie SmartDraw z galerii

Aby skonfigurować integrację programu SmartDraw z usługą Azure AD, musisz dodać SmartDraw z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SmartDraw** w polu wyszukiwania.
1. Wybierz pozycję **SmartDraw** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-smartdraw"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla SmartDraw

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą SmartDraw przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w SmartDraw.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą SmartDraw, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-smartdraw-sso)** w usłudze SmartDraw, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego SmartDraw](#create-smartdraw-test-user)** , aby dysponować odpowiednikiem B. Simon w SmartDraw, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **SmartDraw** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://cloud.smartdraw.com/sso/saml/login/<domain>`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Ta wartość adresu URL logowania zostanie zaktualizowana przy użyciu rzeczywistego adresu URL logowania, co objaśniono w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Kliknij pozycję **Zapisz**.

1. Aplikacja SmartDraw oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja SmartDraw oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | FirstName (Imię) | user.givenname |
    | LastName (Nazwisko) | user.surname |
    | E-mail | user.mail |
    | Grupy | User. Groups |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie SmartDraw** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi SmartDraw.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SmartDraw**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-smartdraw-sso"></a>Konfigurowanie logowania jednokrotnego SmartDraw

1. Aby zautomatyzować konfigurację w programie SmartDraw, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj** , aby przekierować użytkownika do aplikacji SmartDraw. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi SmartDraw. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-5.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować SmartDraw, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy SmartDraw jako administrator i wykonaj następujące czynności:

1. Kliknij pozycję **Logowanie jednokrotne** w obszarze Zarządzanie licencją SmartDraw.

    ![Zrzut ekranu przedstawia okno dialogowe Zarządzanie licencją SmartDraw, w którym można wybrać opcję Logowanie jednokrotne.](./media/smartdraw-tutorial/configure01.png)

1. Na stronie Konfiguracja wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia stronę konfiguracji, na której można wprowadzić podane wartości.](./media/smartdraw-tutorial/configure02.png)

    a. W polu tekstowym **domena (na przykład Acme.com)** wpisz swoją domenę.

    b. Skopiuj **adres URL logowania zainicjowany przez Sp** dla Twojego wystąpienia i wklej go w polu tekstowym adres URL logowania w **podstawowej konfiguracji SAML** na Azure Portal.

    c. W obszarze **grupy zabezpieczeń, aby zezwolić na dostęp SmartDraw** , wpisz **każdy**.

    d. W polu tekstowym **adres URL wystawcy SAML** należy wkleić wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    e. W Notatniku otwórz plik XML metadanych pobrany z Azure Portal, skopiuj jego zawartość, a następnie wklej go do pola **metadanych SAML** .

    f. Kliknij pozycję **Zapisz konfigurację** 

### <a name="create-smartdraw-test-user"></a>Utwórz użytkownika testowego SmartDraw

W tej sekcji użytkownik o nazwie B. Simon został utworzony w SmartDraw. SmartDraw obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze SmartDraw, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SmartDraw w panelu dostępu należy automatycznie zalogować się do SmartDraw, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj SmartDraw z usługą Azure AD](https://aad.portal.azure.com/)