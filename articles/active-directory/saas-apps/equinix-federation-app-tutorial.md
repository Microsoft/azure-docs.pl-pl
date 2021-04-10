---
title: 'Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) w usłudze Equinix Federation App | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i aplikacją Federacji Equinix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: c1172cd818a3b40e908bbf5a133ea76d6b0d17b9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642869"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-equinix-federation-app"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z aplikacją Equinix Federation

W ramach tego samouczka dowiesz się, jak zintegrować aplikację Federacji Equinix z usługą Azure Active Directory (Azure AD). Podczas integrowania aplikacji federacyjnej Equinix z usługą Azure AD można wykonać następujące czynności:

* Kontrolka w usłudze Azure AD, która ma dostęp do aplikacji federacyjnej Equinix.
* Zezwól użytkownikom na automatyczne logowanie do aplikacji federacyjnej Equinix przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym w aplikacji federacyjnej Equinix.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Federacji Equinix obsługuje logowanie jednokrotne w ramach usługi **SP** .

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-equinix-federation-app-from-the-gallery"></a>Dodawanie aplikacji federacyjnej Equinix z galerii

Aby skonfigurować integrację aplikacji federacyjnej Equinix z usługą Azure AD, musisz dodać aplikację Federacji Equinix z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Equinix Federation App** w polu wyszukiwania.
1. Wybierz pozycję **Equinix Federation App** from the Results panel, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-equinix-federation-app"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla aplikacji federacyjnej Equinix

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą aplikacji federacyjnej Equinix przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w aplikacji Equinix Federation.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą aplikacji federacyjnej Equinix, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne w aplikacji federacyjnej Equinix](#configure-equinix-federation-app-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego aplikacji federacyjnej Equinix](#create-equinix-federation-app-test-user)** , aby dysponować odpowiednikiem B. Simon w aplikacji federacyjnej Equinix, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Federacji Equinix** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca:  `https://<customerprefix>customerportal.equinix.com`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zaktualizuj wartość za pomocą rzeczywistego adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta aplikacji federacyjnej Equinix](mailto:prodsecops@equinix.com) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie aplikacji federacyjnej Equinix** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do aplikacji federacyjnej Equinix.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Equinix Federation App**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-equinix-federation-app-sso"></a>Konfigurowanie logowania jednokrotnego dla aplikacji federacyjnej Equinix

Aby skonfigurować pojedyncze Sign-On po stronie **aplikacji federacyjnej Equinix** , użyj [linku](https://docs.equinix.com/Content/ECPortal/ssf/ssf.htm).

### <a name="create-equinix-federation-app-test-user"></a>Utwórz użytkownika testowego aplikacji federacyjnej Equinix

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w aplikacji federacyjnej Equinix. Współpracuj z [zespołem pomocy technicznej aplikacji federacyjnych Equinix](mailto:prodsecops@equinix.com) , aby dodać użytkowników z platformy aplikacji federacyjnej Equinix. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

Przejdź bezpośrednio do adresu URL logowania aplikacji federacyjnej Equinix i zainicjuj tam przepływ logowania.

 > [!NOTE]
 > Jeśli podjęto próbę przetestowania aplikacji platformy Azure za pomocą tego linku **test** lub kliknięcie kafelka aplikacji federacyjnej Equinix, nie będzie on działał, ponieważ jest inicjowane przez dostawcy tożsamości Logowanie jednokrotne, które Equinix nie jest obsługiwane domyślnie.  Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu aplikacji federacyjnej Equinix można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


