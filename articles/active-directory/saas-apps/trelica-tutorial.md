---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Trelica | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Trelica.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.openlocfilehash: a674f5f653ad420ab8f28ff73c6b86f9c18b154e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92517756"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trelica"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Trelica

W tym samouczku dowiesz się, jak zintegrować usługę Trelica z usługą Azure Active Directory (Azure AD).

Dzięki tej integracji można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Trelica.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Trelica przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji oprogramowania jako usługi (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi Trelica z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Trelica obsługuje logowanie jednokrotne inicjowane przez usługę dostawcy tożsamości.
* Trelica obsługuje Inicjowanie obsługi użytkowników just in Time.
* Po skonfigurowaniu Trelica można wymusić kontrolę sesji. Ta kontrolka chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trelica-from-the-gallery"></a>Dodawanie Trelica z galerii

Aby skonfigurować integrację programu Trelica z usługą Azure AD, musisz dodać Trelica z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wprowadź **Trelica** w polu wyszukiwania.
1. Wybierz pozycję **Trelica** z wyników wyszukiwania, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trelica"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Trelica

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Trelica przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić połączoną relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Trelica.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Trelica, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-trelica-sso)** w usłudze Trelica, aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Trelica](#create-a-trelica-test-user)** , aby miał odpowiednik B. Simon w Trelica. Ten odpowiednik jest połączony z reprezentacją usługi Azure AD użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne w usłudze Azure AD w Azure Portal:

1. Na [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Trelica** przejdź do sekcji **Zarządzanie** . Wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Strona Konfigurowanie pojedynczego Sign-On ze stroną SAML z ikoną ołówka dla podstawowej konfiguracji języka SAML została wyróżniona](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wprowadź następujące wartości:

    1. W polu **Identyfikator** wprowadź adres URL **https://app.trelica.com** .

    1. W polu **adres URL odpowiedzi** wprowadź adres URL ze wzorcem `https://app.trelica.com/Id/Saml2/<CUSTOM_IDENTIFIER>/Acs` .

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwa. Zaktualizuj tę wartość za pomocą rzeczywistego adresu URL odpowiedzi (znanego również jako ACS).
    > Można to sprawdzić, logując się do Trelica i przechodząc do [strony konfiguracji dostawcy tożsamości SAML](https://app.trelica.com/Admin/Profile/SAML) (konto > administratora > SAML). Kliknij przycisk Kopiuj obok **adresu URL usługi konsumenckej odbiorcy (ACS)** , aby umieścić ten element w schowku, gotowy do wklejenia do pola tekstowego **adres URL odpowiedzi** w usłudze Azure AD.
    > Zapoznaj się z [dokumentacją pomocy Trelica](https://docs.trelica.com/admin/saml/azure-ad) lub skontaktuj się z [zespołem obsługi klienta Trelica](mailto:support@trelica.com) , jeśli masz pytania.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Sekcja certyfikat podpisywania SAML z wyróżnionym przyciskiem kopiowania obok adresu URL metadanych federacji aplikacji](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B. Simon w Azure Portal.

1. W okienku po lewej stronie Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. W górnej części ekranu wybierz pozycję **nowy użytkownik**.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wpisz **B. Simon**.
   1. W polu **Nazwa użytkownika** wprowadź **B.Simon@**_formacie_**.** _rozszerzenie_. Na przykład B.Simon@contoso.com.
   1. Zaznacz pole wyboru **Pokaż hasło** , a następnie Zapisz wartość, która jest wyświetlana w polu **hasło** .
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Trelica.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Trelica**.
1. Na stronie Przegląd aplikacji przejdź do sekcji **Zarządzanie** , a następnie wybierz pozycję **Użytkownicy i grupy**.

   ![Sekcja zarządzanie z wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

   ![Okno Użytkownicy i grupy z wyróżnioną pozycją Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy. Następnie wybierz przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz jakiejkolwiek wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybieranie roli** wybierz odpowiednią rolę dla użytkownika z listy. Następnie wybierz przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-trelica-sso"></a>Konfigurowanie logowania jednokrotnego Trelica

Aby skonfigurować Logowanie jednokrotne po stronie **Trelica** , przejdź do [strony Konfiguracja dostawców tożsamości saml](https://app.trelica.com/Admin/Profile/SAML) (konto > administratora > SAML). Kliknij przycisk **Nowy** . Wprowadź nazwę **usługi Azure AD** , a następnie wybierz pozycję **metadane z adresu URL** dla typu metadanych. Wklej **adres URL metadanych federacji aplikacji** pochodzący z usługi Azure AD do pola **adres URL metadanych** w Trelica.

Zapoznaj się z [dokumentacją pomocy Trelica](https://docs.trelica.com/admin/saml/azure-ad) lub skontaktuj się z [zespołem obsługi klienta Trelica](mailto:support@trelica.com) , jeśli masz pytania.

### <a name="create-a-trelica-test-user"></a>Tworzenie użytkownika testowego Trelica

Trelica obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. Nie ma żadnych akcji, które należy wykonać w tej sekcji. Jeśli użytkownik nie istnieje jeszcze w usłudze Trelica, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą portalu My Apps.

Po wybraniu kafelka Trelica w portalu My Apps użytkownik zostanie automatycznie zalogowany do Trelica, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [wprowadzenie do portalu My Apps](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Tutorials for integrating SaaS apps with Azure Active Directory (Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory)](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj Trelica z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chronić Trelica z zaawansowaną widocznością i kontrolkami](/cloud-app-security/proxy-intro-aad)