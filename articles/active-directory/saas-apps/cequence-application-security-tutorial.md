---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z zabezpieczeniami aplikacji Cequence | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i zabezpieczeniami aplikacji Cequence.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: jeedes
ms.openlocfilehash: adcbed3178556c8cb37e053a6bbd203197c7d9ef
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370610"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cequence-application-security"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z zabezpieczeniami aplikacji Cequence

W tym samouczku dowiesz się, jak zintegrować zabezpieczenia aplikacji Cequence z Azure Active Directory (Azure AD). W przypadku integrowania zabezpieczeń aplikacji Cequence z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do zabezpieczeń aplikacji Cequence.
* Zezwól użytkownikom na automatyczne logowanie się, aby Cequence zabezpieczenia aplikacji przy użyciu swoich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w aplikacji Cequence.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Zabezpieczenia aplikacji Cequence obsługują Logowanie jednokrotne w usłudze **SP**

* Zabezpieczenia aplikacji Cequence wspierają Inicjowanie obsługi użytkowników **just in Time**


## <a name="adding-cequence-application-security-from-the-gallery"></a>Dodawanie zabezpieczeń aplikacji Cequence z galerii

Aby skonfigurować integrację zabezpieczeń aplikacji Cequence z usługą Azure AD, musisz dodać zabezpieczenia aplikacji Cequence z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Cequence zabezpieczenia aplikacji** w polu wyszukiwania.
1. Wybierz pozycję **Cequence zabezpieczenia aplikacji** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-cequence-application-security"></a>Skonfiguruj i Testuj Logowanie jednokrotne usługi Azure AD na potrzeby zabezpieczeń aplikacji Cequence

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą zabezpieczeń aplikacji Cequence przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Cequence zabezpieczenia aplikacji.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu zabezpieczeń aplikacji Cequence, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj funkcję logowania jednokrotnego dla aplikacji Cequence](#configure-cequence-application-security-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika test zabezpieczeń aplikacji Cequence](#create-cequence-application-security-test-user)** , aby dysponować odpowiednikiem B. Simon w Cequence zabezpieczenia aplikacji, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **zabezpieczeń aplikacji Cequence** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<CUSTOMERNAME>.s.cequence.cloud`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<CUSTOMERNAME>.s.cequence.cloud:443/saml/metadata`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej aplikacji Cequence](mailto:support@cequence.ai) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja zabezpieczeń aplikacji Cequence oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja zabezpieczeń aplikacji Cequence oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.
    
    | Nazwa |  Atrybut źródłowy|
    | --------------- | --------- |
    | Group (Grupa) | User. Groups |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)
### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory** , wybierz pozycję **Użytkownicy** , a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do zabezpieczeń aplikacji Cequence.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Cequence zabezpieczenia aplikacji**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika** , a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-cequence-application-security-sso"></a>Konfigurowanie logowania jednokrotnego dla aplikacji Cequence

Aby skonfigurować Logowanie jednokrotne na stronie **zabezpieczenia aplikacji Cequence** , musisz wysłać **adres URL metadanych federacji aplikacji** do [Cequence zespołu pomocy technicznej aplikacji](mailto:support@cequence.ai). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-cequence-application-security-test-user"></a>Utwórz użytkownika testowego zabezpieczeń aplikacji Cequence

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w zabezpieczeniach aplikacji Cequence. Zabezpieczenia aplikacji Cequence obsługują funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w Cequence zabezpieczenia aplikacji, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

1. Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania zabezpieczeń aplikacji Cequence, w którym można zainicjować przepływ logowania. 

2. Przejdź do adresu URL logowania zabezpieczeń aplikacji Cequence bezpośrednio i zainicjuj tam przepływ logowania.

3. Możesz użyć panelu programu Microsoft Access. Po kliknięciu kafelka zabezpieczenia aplikacji Cequence w panelu dostępu zostanie on przekierowany do adresu URL logowania Cequence zabezpieczeń aplikacji. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu zabezpieczeń aplikacji Cequence można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


