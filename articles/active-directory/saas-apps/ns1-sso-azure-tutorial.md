---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu logowania jednokrotnego NS1 dla platformy Azure | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i NS1 Logowanie jednokrotne na platformie Azure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.openlocfilehash: a7fad0af26d15bc77a52d55309c594937ba3be13
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518623"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu logowania jednokrotnego NS1 dla platformy Azure

W tym samouczku dowiesz się, jak zintegrować usługę NS1 SSO dla platformy Azure z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi NS1 SSO dla systemu Azure z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do NS1 SSO dla platformy Azure.
* Zezwól użytkownikom na automatyczne logowanie się, aby NS1 Logowanie jednokrotne na platformie Azure przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji, Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji oprogramowania jako usługi (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* NS1 Logowanie jednokrotne dla subskrypcji z włączonym logowaniem jednokrotnym (SSO) na platformie Azure.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* NS1 Logowanie jednokrotne na platformie Azure obsługuje logowanie jednokrotne z użyciem usług SP i dostawcy tożsamości
* Po skonfigurowaniu logowania jednokrotnego NS1 dla platformy Azure można wymusić kontrolę sesji. Chroni to eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>Dodawanie NS1 SSO dla platformy Azure z galerii

Aby skonfigurować integrację logowania jednokrotnego w usłudze NS1 dla platformy Azure z usługą Azure AD, musisz dodać do usługi NS1 Logowanie jednokrotne z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **ns1 SSO dla platformy Azure** w polu wyszukiwania.
1. W panelu wyników wybierz pozycję **ns1 SSO dla platformy Azure** , a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla logowania jednokrotnego w usłudze Azure NS1

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w usłudze NS1 dla platformy Azure przy użyciu użytkownika testowego o nazwie **B. Simon** Aby logowanie jednokrotne działało, należy ustanowić połączoną relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze NS1 SSO dla platformy Azure.

Poniżej przedstawiono ogólne kroki konfigurowania i testowania logowania jednokrotnego w usłudze Azure AD za pomocą logowania jednokrotnego w usłudze Azure NS1:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.

    a. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.

    b. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie JEDNOkrotne w usłudze ns1 dla logowania](#configure-ns1-sso-for-azure-sso)** jednokrotnego na platformie Azure, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.

    a. **[Utwórz ns1 Logowanie jednokrotne dla użytkownika testowego platformy Azure](#create-an-ns1-sso-for-azure-test-user)** , aby uzyskać odpowiedni odpowiednik B. Simon w ns1 SSO dla platformy Azure. Ten odpowiednik jest połączony z reprezentacją usługi Azure AD użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **usługi ns1 Logowanie jednokrotne dla** integracji aplikacji Azure Znajdź sekcję **Zarządzanie** . Wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający Konfigurowanie logowania jednokrotnego przy użyciu strony SAML z wyróżnioną ikoną ołówka](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz następujący adres URL: `https://api.nsone.net/saml/metadata`

    b. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, który używa następującego wzorca: `https://api.nsone.net/saml/sso/<ssoid>`

1. Wybierz opcję **Ustaw dodatkowe adresy URL**i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wpisz następujący adres URL:  `https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwa. Zaktualizuj wartość adresu URL odpowiedzi przy użyciu rzeczywistego adresu URL odpowiedzi. Aby uzyskać wartość, skontaktuj się z [zespołem pomocy technicznej SSO ns1 dla klientów platformy Azure](mailto:techops@nsone.net) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. NS1 Logowanie jednokrotne dla aplikacji platformy Azure oczekuje potwierdzeń SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów można zarządzać z sekcji " **atrybuty użytkownika & oświadczenia** na stronie integracja aplikacji". Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę ołówka, aby otworzyć okno dialogowe **atrybuty użytkownika** .

    ![Zrzut ekranu przedstawiający sekcje User Attributes & oświadczenia z wyróżnioną ikoną ołówka](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Wybierz nazwę atrybutu, aby edytować to zastrzeżenie.

    ![Zrzut ekranu przedstawiający atrybuty użytkownika & sekcji oświadczeń z wyróżnioną nazwą atrybutu](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Wybierz **transformację**.

    ![Zrzut ekranu przedstawiający sekcję Zarządzanie wnioskiem z wyróżnioną transformację](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. W sekcji **Zarządzanie transformacjami** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający sekcję Zarządzanie transformacjami z różnymi wyróżnionymi polami](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Wybierz pozycję **ExactMailPrefix ()** jako **przekształcenie**.

    1. Wybierz pozycję **User. userPrincipalName** jako **parametr 1**.

    1. Wybierz pozycję **Dodaj**.

    1. Wybierz pozycję **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz przycisk Kopiuj. Spowoduje to skopiowanie **adresu URL metadanych federacji aplikacji** i zapisanie go na komputerze.

    ![Zrzut ekranu przedstawiający certyfikat podpisywania SAML z wyróżnionym przyciskiem kopiowania](common/copy-metadataurl.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi logowania jednokrotnego NS1 dla platformy Azure.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **ns1 SSO dla platformy Azure**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Zrzut ekranu strony użytkownicy i grupy z wyróżnioną pozycją Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy. Następnie wybierz przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że jakakolwiek wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Następnie wybierz przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-ns1-sso-for-azure-sso"></a>Konfigurowanie logowania jednokrotnego NS1 dla logowania jednokrotnego platformy Azure

Aby skonfigurować Logowanie jednokrotne na NS1 rejestracji jednokrotnej na platformie Azure, musisz wysłać adres URL metadanych federacji aplikacji do [usługi ns1 SSO dla zespołu pomocy technicznej systemu Azure](mailto:techops@nsone.net). Ustawi on to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-ns1-sso-for-azure-test-user"></a>Tworzenie NS1 SSO dla użytkownika testowego platformy Azure

W tej sekcji utworzysz użytkownika o nazwie B. Simon w usłudze NS1 SSO dla platformy Azure. Pracuj z zespołem pomocy technicznej NS1 dla systemu Azure, aby dodać użytkowników na platformie rejestracji jednokrotnej NS1 dla platformy Azure. Nie można korzystać z logowania jednokrotnego do momentu utworzenia i aktywowania użytkowników.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka Logowanie jednokrotne dla platformy Azure w panelu dostępu należy automatycznie zalogować się do NS1 logowania jednokrotnego dla platformy Azure, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj usługę NS1 SSO dla platformy Azure w usłudze Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)