---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Procaire | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Procaire.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: e41681dda647a12a457675c27e06fc114ae7ccae
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146410"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-procaire"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Procaire

W tym samouczku dowiesz się, jak zintegrować usługę Procaire z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Procaire z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Procaire.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Procaire przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Procaire.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Procaire obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-procaire-from-the-gallery"></a>Dodawanie Procaire z galerii

Aby skonfigurować integrację programu Procaire z usługą Azure AD, musisz dodać Procaire z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje** .
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** .
1. W sekcji **Dodaj z galerii** wpisz **Procaire** w polu wyszukiwania.
1. Wybierz pozycję **Procaire** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-procaire"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Procaire

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Procaire przy użyciu użytkownika testowego o nazwie **B. Simon** . Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Procaire.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Procaire, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-procaire-sso)** w usłudze Procaire, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Procaire](#create-procaire-test-user)** , aby dysponować odpowiednikiem B. Simon w Procaire, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Procaire** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne** .
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML** .
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://praisidio.app/auth/login`


1. Kliknij pozycję **Zapisz** .

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)
### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory** , wybierz pozycję **Użytkownicy** , a następnie wybierz pozycję **Wszyscy użytkownicy** .
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło** .
   1. Kliknij pozycję **Utwórz** .

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Procaire.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje** .
1. Na liście Aplikacje wybierz pozycję **Procaire** .
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy** .
1. Wybierz pozycję **Dodaj użytkownika** , a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-procaire-sso"></a>Konfigurowanie logowania jednokrotnego Procaire

Aby skonfigurować Logowanie jednokrotne na stronie **Procaire** , musisz wysłać **adres URL metadanych federacji aplikacji** do zespołu pomocy technicznej Praisidio. Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-procaire-test-user"></a>Utwórz użytkownika testowego Procaire

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Procaire. Aby dodać użytkowników na platformie Procaire, Pracuj z zespołem pomocy technicznej Praisidio. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Procaire, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania Procaire i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do Procaire, dla którego skonfigurowano Logowanie jednokrotne 

Możesz również użyć panelu dostępu programu Microsoft, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka Procaire w panelu dostępu, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do Procaire, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Procaire można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


