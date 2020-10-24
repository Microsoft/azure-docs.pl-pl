---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z zabezpieczeniami usługa TINFOIL | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i zabezpieczeniami usługa TINFOIL.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: 5c2ac2c7bb1b60c87075a1bc62241edab2fc310e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516300"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tinfoil-security"></a>Samouczek: integracja logowania jednokrotnego (SSO) Azure Active Directory z zabezpieczeniami usługa TINFOIL

W tym samouczku dowiesz się, jak zintegrować zabezpieczenia usługi Usługa TINFOIL z usługą Azure Active Directory (Azure AD). W przypadku integrowania zabezpieczeń usługa TINFOIL z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do zabezpieczeń usługa TINFOIL.
* Zezwól użytkownikom na automatyczne logowanie się, aby usługa TINFOIL zabezpieczenia przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi Usługa TINFOIL.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* ZABEZPIECZENIA usługa TINFOIL obsługują **dostawcy tożsamości** zainicjowane Logowanie jednokrotne

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-tinfoil-security-from-the-gallery"></a>Dodawanie zabezpieczeń usługa TINFOIL z galerii

Aby skonfigurować integrację zabezpieczeń usługa TINFOIL w usłudze Azure AD, musisz dodać zabezpieczenia usługa TINFOIL z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Usługa TINFOIL Security** w polu wyszukiwania.
1. Wybierz pozycję **zabezpieczenia usługa TINFOIL** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tinfoil-security"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby zabezpieczeń usługa TINFOIL

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą usługa TINFOIL SECURITY przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ZABEZPIECZENIAch usługa TINFOIL.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu zabezpieczeń usługa TINFOIL, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne zabezpieczeń usługa TINFOIL](#configure-tinfoil-security-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika test zabezpieczeń usługa TINFOIL](#create-tinfoil-security-test-user)** , aby uzyskać odpowiednik B. Simon w zabezpieczeniach usługa TINFOIL, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **zabezpieczeń usługa TINFOIL** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** aplikacja została wstępnie skonfigurowana i wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz** .

1. Odwiedzanie aplikacji oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, odwiedzanie aplikacji oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy |
    | ------------------- | -------------|
    | accountId | UXXXXXXXXXXXXX |

    > [!NOTE]
    > Wartość AccountId zostanie omówiona w dalszej części tego samouczka.

1. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

1. W sekcji **certyfikat podpisywania SAML** Skopiuj **wartość odcisku palca** i Zapisz ją na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

1. W sekcji **Konfigurowanie zabezpieczeń usługa TINFOIL** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do zabezpieczeń usługa TINFOIL.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **zabezpieczenia usługa TINFOIL**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-tinfoil-security-sso"></a>Konfigurowanie logowania jednokrotnego zabezpieczeń usługa TINFOIL

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny zabezpieczeń usługa TINFOIL jako administrator.

1. Na pasku narzędzi u góry kliknij pozycję **Moje konto**.

    ![Pulpit nawigacyjny](./media/tinfoil-security-tutorial/ic798971.png "Pulpit nawigacyjny")

1. Kliknij pozycję **Security** (Zabezpieczenia).

    ![Zabezpieczenia](./media/tinfoil-security-tutorial/ic798972.png "Zabezpieczenia")

1. Na stronie konfiguracji **Single Sign On** (Logowanie jednokrotne) wykonaj następujące kroki:

    ![Logowanie jednokrotne](./media/tinfoil-security-tutorial/ic798973.png "Logowanie jednokrotne")

    a. Wybierz pozycję **Enable SAML** (Włącz SAML).

    b. Kliknij pozycję **Konfiguracja ręczna**.

    c. W polu tekstowym **adres URL post protokołu SAML** wklej wartość **adresu URL logowania** skopiowanego z Azure Portal

    d. W polu tekstowym **odcisk palca certyfikatu SAML** wklej wartość **odcisku palca** skopiowanego z sekcji **certyfikat podpisywania SAML** .
  
    e. Skopiuj wartość **identyfikatora konta** i wklej wartość w polu tekstowym **atrybut źródłowy** w obszarze **atrybuty użytkownika & oświadczenia** w Azure Portal.

    f. Kliknij pozycję **Zapisz**.

### <a name="create-tinfoil-security-test-user"></a>Utwórz użytkownika testowego zabezpieczeń usługa TINFOIL

Aby umożliwić użytkownikom usługi Azure AD logowanie się do zabezpieczeń usługa TINFOIL, muszą one być obsługiwane w ZABEZPIECZENIAch usługa TINFOIL. W przypadku zabezpieczeń usługa TINFOIL Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby uzyskać dostęp do użytkownika, wykonaj następujące czynności:**

1. Jeśli użytkownik jest częścią konta przedsiębiorstwa, należy [skontaktować się z zespołem pomocy technicznej usługa TINFOIL](https://www.tinfoilsecurity.com/contact) w celu uzyskania utworzonego konta użytkownika.

1. Jeśli użytkownik jest zwykłym użytkownikiem usługa TINFOIL SECURITY SaaS, wówczas użytkownik może dodać współpracownika do dowolnej lokacji użytkownika. Spowoduje to wyzwolenie procesu wysłania zaproszenia do określonej wiadomości e-mail w celu utworzenia nowego konta użytkownika zabezpieczeń usługa TINFOIL.

> [!NOTE]
> Do udostępniania kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników zabezpieczeń usługa TINFOIL lub interfejsów API udostępnianych przez zabezpieczenia usługa TINFOIL.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka zabezpieczenia usługa TINFOIL w panelu dostępu należy automatycznie zalogować się do zabezpieczeń usługa TINFOIL, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj zabezpieczenia usługa TINFOIL z usługą Azure AD](https://aad.portal.azure.com/)