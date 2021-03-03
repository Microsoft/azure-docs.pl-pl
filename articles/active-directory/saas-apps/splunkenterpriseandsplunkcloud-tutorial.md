---
title: 'Samouczek: integracja Azure Active Directory z usługą Splunk Enterprise i Splunk Cloud | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i rozwiązaniem Splunk Enterprise and Splunk Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 81107b3655ae86d51e36445ce46661d553ab3b5a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649861"
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Samouczek: integracja Azure Active Directory z chmurą Splunk Enterprise i Splunk

W tym samouczku dowiesz się, jak zintegrować chmurę Splunk Enterprise i Splunk z usługą Azure Active Directory (Azure AD). Gdy integrujesz chmurę Splunk Enterprise i Splunk z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do chmury Splunk Enterprise i Splunk.
* Umożliwienie użytkownikom automatycznego logowania się do usługi Splunk Enterprise i Splunk Cloud przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w chmurze Splunk Enterprise i Splunk.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozwiązanie Splunk Enterprise and Splunk Cloud obsługuje logowanie jednokrotne inicjowane przez **SP**

## <a name="add-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Dodaj chmurę Splunk Enterprise i Splunk z galerii

Aby skonfigurować integrację rozwiązania Splunk Enterprise and Splunk Cloud z usługą Azure AD, należy dodać rozwiązanie Splunk Enterprise and Splunk Cloud z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Splunk Enterprise i Splunk Cloud** w polu wyszukiwania.
1. Wybierz pozycję **Splunk Enterprise i Splunk Cloud** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla usług Splunk Enterprise i Splunk Cloud

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą chmury Splunk Enterprise i Splunk przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w chmurze Splunk Enterprise i Splunk.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą chmury Splunk Enterprise i Splunk, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. Skonfiguruj ustawienia rejestracji jednokrotnej w **[chmurze Splunk Enterprise i Splunk](#configure-splunk-enterprise-and-splunk-cloud-sso)** w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego w chmurze Splunk Enterprise i Splunk](#create-splunk-enterprise-and-splunk-cloud-test-user)** , aby dysponować odpowiednikiem B. Simon w Splunk Enterprise i Splunk Cloud, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Splunk Enterprise i Splunk w chmurze** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)
4. W sekcji **Podstawowa konfiguracja języka SAML** wykonaj następujący wzorzec:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<splunkserverUrl>/app/launcher/home`

    b. W polu **Identyfikator** wpisz adres URL, używając następującego wzorca: `<splunkserverUrl>`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<splunkserver>/saml/acs`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem obsługi klienta rozwiązania Splunk Enterprise and Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do chmury Splunk Enterprise i Splunk.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Splunk Enterprise i Splunk Cloud**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-splunk-enterprise-and-splunk-cloud-sso"></a>Konfigurowanie rejestracji jednokrotnej w chmurze Splunk Enterprise i Splunk

  Aby skonfigurować logowanie jednokrotne po stronie rozwiązania **Splunk Enterprise and Splunk Cloud**, należy wysłać pobrany **plik XML metadanych federacji** i odpowiednie adresy URL skopiowane z witryny Azure Portal do [zespołu pomocy technicznej rozwiązania Splunk Enterprise and Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-splunk-enterprise-and-splunk-cloud-test-user"></a>Tworzenie użytkownika testowego rozwiązania Splunk Enterprise and Splunk Cloud

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w rozwiązaniu Splunk Enterprise and Splunk Cloud. Współpracuj z [zespołem pomocy technicznej w chmurze Splunk Enterprise i Splunk](https://www.splunk.com/en_us/about-splunk/contact-us.html) , aby dodać użytkowników z platformy Splunk Enterprise i Splunk Cloud. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do usługi Splunk Enterprise i Splunk w chmurze, w którym można zainicjować przepływ logowania. 

* Przejdź do adresu URL logowania w chmurze Splunk przedsiębiorstwa i Splunk bezpośrednio i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Cloud Splunk Enterprise i Splunk w obszarze Moje aplikacje zostanie ono przekierowany do adresu URL logowania w chmurze Splunk Enterprise i Splunk. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu chmury Splunk Enterprise i Splunk można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)