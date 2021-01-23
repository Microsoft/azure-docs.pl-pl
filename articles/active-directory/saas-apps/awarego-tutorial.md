---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą AwareGo | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i AwareGo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2020
ms.author: jeedes
ms.openlocfilehash: 4682396f68d6ff1af0b2fb6a5b1a8419d6963529
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735343"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-awarego"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym za pomocą AwareGo

W tym samouczku dowiesz się, jak zintegrować usługę AwareGo z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi AwareGo z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do AwareGo.
* Zezwól użytkownikom na automatyczne logowanie się do usługi AwareGo przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji, Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO) AwareGo.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. AwareGo obsługuje logowanie jednokrotne przez dostawcę usług (SP).


## <a name="adding-awarego-from-the-gallery"></a>Dodawanie AwareGo z galerii

Aby skonfigurować integrację programu AwareGo z usługą Azure AD, musisz dodać AwareGo z galerii do listy aplikacji zarządzanych oprogramowania jako usługi (SaaS).

1. Zaloguj się do Azure Portal przy użyciu konta służbowego, konta szkoły lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz usługę **Azure Active Directory** .
1. Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **AwareGo** w polu wyszukiwania.
1. W okienku wyników wybierz pozycję **AwareGo**, a następnie Dodaj aplikację. W ciągu kilku sekund aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-awarego"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla AwareGo

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą AwareGo przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w AwareGo.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą AwareGo, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.  

    a. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą użytkownika B. Simon.  
    b. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić użytkownikowi B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.  

1. **[Skonfiguruj Logowanie jednokrotne](#configure-awarego-sso)** w usłudze AwareGo, aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.

    a. **[Utwórz użytkownika testowego AwareGo](#create-an-awarego-test-user)** , aby miał odpowiednik B. Simon w AwareGo, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.  
    b. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal, wykonaj następujące czynności:

1. W Azure Portal na stronie integracja aplikacji **AwareGo** w obszarze **Zarządzaj** wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Aby edytować ustawienia, w okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** wybierz przycisk **Edytuj** .

   ![Zrzut ekranu przycisku Edytuj na potrzeby podstawowej konfiguracji protokołu SAML.](common/edit-urls.png)

1. W okienku Edycja w obszarze **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    a. W polu **adres URL logowania** wprowadź jeden z następujących adresów URL:

    * `https://lms.awarego.com/auth/signin/` 
    * `https://my.awarego.com/auth/signin/`

    b. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL w następującym formacie: `https://<SUBDOMAIN>.awarego.com`

    c. W polu **adres URL odpowiedzi** wprowadź adres URL w następującym formacie: `https://<SUBDOMAIN>.awarego.com/auth/sso/callback`

    > [!NOTE]
    > Podane wyżej wartości nie są rzeczywiste. Zaktualizuj je za pomocą rzeczywistego identyfikatora i adresów URL odpowiedzi. Aby uzyskać wartości, skontaktuj się z [zespołem obsługi klienta AwareGo](mailto:support@awarego.com). Możesz również zapoznać się z przykładami w sekcji **podstawowe informacje o konfiguracji SAML** w Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** obok pozycji **certyfikat (base64)** wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Zrzut ekranu przedstawiający link "Pobierz" certyfikatu w okienku certyfikat podpisywania SAML.](common/certificatebase64.png)

1. W sekcji **Konfiguracja AwareGo** Skopiuj co najmniej jeden adres URL, w zależności od wymagań.

    ![Zrzut ekranu przedstawiający okienko "Set up AwareGo" służące do kopiowania adresów URL konfiguracji.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku Azure Portal wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. W okienku właściwości **użytkownika** wykonaj następujące czynności:

   a. W polu **Nazwa** wprowadź wartość **B. Simon**.  
   b. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika w następującym formacie: `<username>@<companydomain>.<extension>` (na przykład B.Simon@contoso.com ).  
   c. Zaznacz pole wyboru **Pokaż hasło** , a następnie zanotuj wartość, która jest wyświetlana w polu **hasło** do późniejszego użycia.  
   d. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji zostanie włączona funkcja User B. Simon, która umożliwia logowanie jednokrotne na platformie Azure przez przyznanie dostępu do usługi AwareGo.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście **aplikacje** wybierz pozycję **AwareGo**.
1. Na stronie Przegląd aplikacji w sekcji **Zarządzanie** wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika** , a następnie w okienku **Dodaj przypisanie** wybierz pozycję **Użytkownicy i grupy**.
1. W okienku **Użytkownicy i grupy** , na liście **Użytkownicy** wybierz pozycję **B. Simon**, a następnie wybierz przycisk **Wybierz** .
1. Jeśli zamierzasz przypisać rolę do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli żadna rola nie została skonfigurowana dla tej aplikacji, zostanie wybrana *Domyślna rola dostępu* .
1. W okienku **Dodaj przypisanie** wybierz przycisk **Przypisz** .

## <a name="configure-awarego-sso"></a>Konfigurowanie logowania jednokrotnego AwareGo

Aby skonfigurować Logowanie jednokrotne po stronie **AwareGo** , Wyślij wcześniej pobrany certyfikat **certyfikatu (base64)** i adresy URL skopiowane wcześniej z Azure Portal do [zespołu pomocy technicznej AwareGo](mailto:support@awarego.com). Zespół pomocy technicznej tworzy to ustawienie, aby prawidłowo nawiązać połączenie SSO SAML po obu stronach.

### <a name="create-an-awarego-test-user"></a>Tworzenie użytkownika testowego AwareGo

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w AwareGo. Współpracuj z [zespołem pomocy technicznej AwareGo](mailto:support@awarego.com) , aby dodać użytkowników z platformy AwareGo. Aby móc korzystać z logowania jednokrotnego, należy najpierw utworzyć i aktywować użytkowników.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD, wykonując jedną z następujących czynności: 

* W Azure Portal wybierz pozycję **Testuj tę aplikację**. Przekieruje Cię do strony logowania AwareGo, na której można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do strony logowania AwareGo i zainicjuj tam przepływ logowania.

* Przejdź do witryny Microsoft moje aplikacje. Po wybraniu kafelka **AwareGo** w obszarze Moje aplikacje nastąpi przekierowanie do strony logowania do AwareGo. Aby uzyskać więcej informacji, zobacz artykuł [Logowanie i uruchamianie aplikacji z poziomu portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu AwareGo można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od Kontrola dostępu warunkowego aplikacji. Aby uzyskać więcej informacji, zobacz [Informacje o sposobie wymuszania kontroli sesji przy użyciu Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).