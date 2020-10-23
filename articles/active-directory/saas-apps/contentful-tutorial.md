---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z zawartością | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i zawartością.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: jeedes
ms.openlocfilehash: b664a7e4aac571359fb1494cf69d5bbe3e841b4d
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455323"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-contentful"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu zawartości

W tym samouczku dowiesz się, jak zintegrować zawartość z usługą Azure Active Directory (Azure AD). Po zintegrowaniu zawartości z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do zawartości.
* Zezwól użytkownikom na automatyczne logowanie się do zawartości przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączoną obsługą logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Zawartość obsługuje usługę **SP i dostawcy tożsamości** zainicjowane przez usługę SSO
* Zawartość obsługuje Inicjowanie obsługi użytkowników **just in Time**
* Po skonfigurowaniu zawartości można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu. Tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-contentful-from-the-gallery"></a>Dodawanie zawartości z galerii

Aby skonfigurować integrację zawartości w usłudze Azure AD, musisz dodać zawartość z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **zawartość** w polu wyszukiwania.
1. W wynikach wybierz pozycję **zawartość** , a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-contentful"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla zawartości

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD z zawartością przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w zawartości.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu zawartości, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj zawartość Logowanie jednokrotne](#configure-contentful-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego zawartości](#create-contentful-test-user)** , aby dysponować odpowiednikiem B. Simon w zawartości, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacją **zawartość** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    - W polu tekstowym **adres URL odpowiedzi** Skopiuj adres URL ACS (usługa konsumencka odbiorcy) ze strony Konfiguracja logowania jednokrotnego w temacie zawartość. Będzie wyglądać następująco: `https://be.contentful.com/sso/<organization_id>/consume`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    - W polu tekstowym **adres URL logowania** Skopiuj ten sam adres URL usługi ACS (usługa konsumencka odbiorcy). Będzie wyglądać następująco: `https://be.contentful.com/sso/<organization_id>/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu adresu URL rzeczywistego odpowiedzi i adresu URL Sign-On, kopiując adres URL ACS (usługi Konsumenckej potwierdzenia) ze strony Instalatora rejestracji jednokrotnej w temacie zawartość.

1. Aplikacja z zawartością oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja z zawartością oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.
    
    | Nazwa |  Atrybut źródłowy|
    | --------------- | --------- |
    | poczta e-mail | user.userprincipalname |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Skonfiguruj zawartość zawartości** Skopiuj adres URL logowania, aby skonfigurować zawartość logowania jednokrotnego.

    ![Kopiowanie adresów URL konfiguracji](media/contentful-tutorial/copy-configuration-urls.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do zawartości.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **zawartość**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części strony.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części strony.
1. W oknie dialogowym **Dodaj przypisanie** kliknij przycisk **Przypisz** .

## <a name="configure-contentful-sso"></a>Skonfiguruj zawartość Logowanie jednokrotne

Wykonaj następujące kroki, aby skonfigurować Logowanie jednokrotne na stronie z **zawartością** .

1. W obszarze [zawartość](https://app.contentful.com)przejdź do strony Konfiguracja logowania jednokrotnego w obszarze **Ustawienia organizacji**.
1. Kliknij pozycję **Skonfiguruj Logowanie jednokrotne**.
1. Skopiuj i wklej adres URL logowania z sekcji **Konfiguracja zawartości** w usłudze Azure AD.
1. Skopiuj i wklej certyfikat z pliku certyfikatu Base64 pobranego z usługi Azure AD.
1. Skonfiguruj nazwę rejestracji Jednokrotnej dla logowania zainicjowanego przez usługę SP.
1. Kliknij pozycję **Włącz logowanie jednokrotne**.

Jeśli to nie zadziała, skontaktuj się z [zespołem pomocy technicznej ds](mailto:support@contentful.com). klientów.

### <a name="create-contentful-test-user"></a>Tworzenie użytkownika testowego zawartości

W tej sekcji użytkownik o nazwie B. Simon jest tworzony w zawartości. Zawartość obsługuje funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w zawartości, po uwierzytelnieniu zostanie utworzony nowy.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka zawartość w panelu dostępu należy automatycznie zalogować się do zawartości, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj zawartość za pomocą usługi Azure AD](https://aad.portal.azure.com/)