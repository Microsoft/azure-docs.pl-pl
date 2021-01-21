---
title: 'Samouczek: integracja Azure Active Directory z oprogramowaniem SAP Qualtrics | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i SAP Qualtrics.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 07340cb66fe9ef2f22e1f3e9b201e46dd07254bb
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622314"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu oprogramowania SAP Qualtrics

W tym samouczku dowiesz się, jak zintegrować oprogramowanie SAP Qualtrics z usługą Azure Active Directory (Azure AD). Po zintegrowaniu integracji oprogramowania SAP Qualtrics z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do rozwiązania SAP Qualtrics.
* Zezwól użytkownikom na automatyczne logowanie do programu SAP Qualtrics przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne będą następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja SAP Qualtrics z włączoną obsługą logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* System SAP Qualtrics obsługuje logowanie jednokrotne z użyciem protokołu **SP** i **dostawcy tożsamości** .
* Rozwiązanie SAP Qualtrics obsługuje funkcję aprowizacji użytkowników **just in Time** .

## <a name="add-sap-qualtrics-from-the-gallery"></a>Dodawanie SAP Qualtrics z galerii

Aby skonfigurować integrację oprogramowania SAP Qualtrics w usłudze Azure AD, musisz dodać do listy zarządzanych aplikacji SaaS SAP Qualtrics z galerii.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SAP Qualtrics** w polu wyszukiwania.
1. Wybierz pozycję **SAP Qualtrics** z wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla oprogramowania SAP Qualtrics

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą oprogramowania SAP Qualtrics przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić połączoną relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w oprogramowaniu SAP Qualtrics.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą oprogramowania SAP Qualtrics, wykonaj następujące bloki konstrukcyjne:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. [Skonfiguruj Logowanie jednokrotne](#configure-sap-qualtrics-sso) w oprogramowaniu SAP Qualtrics na potrzeby konfigurowania ustawień logowania jednokrotnego po stronie aplikacji.
    1. [Utwórz użytkownika testowego SAP Qualtrics](#create-sap-qualtrics-test-user) , aby dysponować odpowiednikiem B. Simon w oprogramowaniu SAP Qualtrics, połączonym z reprezentacją usługi Azure AD.
1. [Przetestuj Logowanie jednokrotne](#test-sso) , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **SAP Qualtrics** Znajdź sekcję **Zarządzanie** . Wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** , jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , wprowadź wartości dla następujących pól:
    
    a. W polu tekstowym **Identyfikator** wpisz adres URL, który używa następującego wzorca:

    `https://< DATACENTER >.qualtrics.com`
   
    b. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, który używa następującego wzorca:

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. W polu tekstowym **stan przekaźnika** wpisz adres URL, który używa następującego wzorca:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Wybierz opcję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wpisz adres URL, który używa następującego wzorca:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania, identyfikatora, adresu URL odpowiedzi i stanu przekazywania. Aby uzyskać te wartości, skontaktuj się z [zespołem obsługi klienta Qualtrics](https://www.qualtrics.com/support/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz ikonę kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** , a następnie wpisz hasło.
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączasz funkcję B. Simon do korzystania z logowania jednokrotnego na platformie Azure, przyznając dostęp do usługi SAP Qualtrics.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SAP Qualtrics**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**. Następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy użytkowników. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. Jeśli oczekujesz, że jakakolwiek wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-sap-qualtrics-sso"></a>Konfigurowanie logowania jednokrotnego SAP Qualtrics

Aby skonfigurować Logowanie jednokrotne na stronie SAP Qualtrics, Wyślij **adres URL skopiowanego metadanych federacji aplikacji** z Azure Portal do [zespołu pomocy technicznej SAP Qualtrics](https://www.qualtrics.com/support/). Zespół pomocy technicznej gwarantuje, że połączenie SSO protokołu SAML jest prawidłowo ustawione na obu stronach.

### <a name="create-sap-qualtrics-test-user"></a>Utwórz użytkownika testowego SAP Qualtrics

Rozwiązanie SAP Qualtrics obsługuje Inicjowanie obsługi użytkowników just in Time, które jest domyślnie włączone. Nie ma żadnych dodatkowych akcji do wykonania. Jeśli użytkownik nie istnieje jeszcze w oprogramowaniu SAP Qualtrics, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania SAP Qualtrics, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania przy użyciu oprogramowania SAP Qualtrics i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do programu SAP Qualtrics, dla którego skonfigurowano Logowanie jednokrotne.

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka SAP Qualtrics w obszarze Moje aplikacje, jeśli skonfigurowano w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do usługi SAP Qualtrics, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu oprogramowania SAP Qualtrics można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Informacje o sposobie wymuszania kontroli sesji przy użyciu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).