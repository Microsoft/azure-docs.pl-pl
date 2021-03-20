---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO, Single Sign-on) z usługą SSOGEN — Azure AD SSO Gateway for Oracle Web-Business Suite-EBS, PeopleSoft i JDE | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite — EBS, PeopleSoft i JDE.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 9eeafaf0f5fbfaff9394ced0a0623f2fb462ed4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101647000"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO, Single Sign-on) z usługą SSOGEN — Brama rejestracji jednokrotnej w usłudze Azure AD dla Oracle E-Business Suite — EBS, PeopleSoft i JDE

W tym samouczku dowiesz się, jak zintegrować usługę SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite — EBS, PeopleSoft i JDE z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi SSOGEN — bramy rejestracji jednokrotnej w usłudze Azure AD dla programu Oracle E-Business Suite — EBS, PeopleSoft i JDE z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite — EBS, PeopleSoft i JDE.
* Zezwól użytkownikom na automatyczne logowanie do SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite — EBS, PeopleSoft i JDE z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite — EBS, PeopleSoft i JDE z obsługą logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite-EBS, PeopleSoft i JDE obsługuje logowanie jednokrotne z użyciem usług **SP i dostawcy tożsamości** .

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>Dodawanie SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite-EBS, PeopleSoft i JDE z galerii

Aby skonfigurować integrację usługi SSOGEN-Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft i JDE w usłudze Azure AD, należy dodać SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite-EBS, PeopleSoft i JDE z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SSOGEN-Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft i JDE** w polu wyszukiwania.
1. Wybierz pozycję **SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite — EBS, PeopleSoft i JDE** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite — EBS, PeopleSoft i JDE

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite — EBS, PeopleSoft i JDE przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite — EBS, PeopleSoft i JDE.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą usługi SSOGEN — Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft i JDE, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite-EBS, PeopleSoft i JDE logowanie](#configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso)** jednokrotne, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite-EBS, PeopleSoft i JDE użytkownika testowego](#create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user)** , aby dysponować odpowiednikiem B. Simon w SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite — EBS, PEOPLESOFT i JDE, która jest połączona z reprezentacją usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal, na stronie **SSOGEN-Azure AD SSO Gateway for Oracle Web-Business Suite-EBS, PeopleSoft i JDE** Application Integration, Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:  `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Contact [SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite-EBS, PeopleSoft i JDE klienta](mailto:support@ssogen.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla aplikacji Oracle E-Business Suite — EBS, PeopleSoft i JDE oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla aplikacji Oracle E-Business Suite — EBS, PeopleSoft i JDE oczekuje, że **NameIdentifier** ma być mapowany do **User. nazwy pospolitej onpremisessamaccountname**, dlatego należy edytować Mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutu.

    ![image (obraz)](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do bramy rejestracji jednokrotnej usługi Azure AD dla programu Oracle dla firmowych zestawów EBS, PeopleSoft i JDE.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite — EBS, PeopleSoft i JDE**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso"></a>Konfigurowanie usługi SSOGEN — Brama rejestracji jednokrotnej w usłudze Azure AD dla programu Oracle E-Business Suite — EBS, PeopleSoft i JDE Logowanie jednokrotne

Aby skonfigurować Logowanie jednokrotne w usłudze **SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite-EBS, PeopleSoft i JDE** , należy znaleźć w dokumentacji dotyczącej rejestracji SSO specyficznej dla aplikacji poniżej:

* Oracle EBS — integracja z logowaniem jednokrotnym w usłudze Azure AD: [https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* PeopleSoft — integracja z logowaniem jednokrotnym w usłudze Azure AD: [https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* JD Edwards — integracja z logowaniem jednokrotnym w usłudze Azure AD: [https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Integracja z usługą Azure AD SSO: [https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user"></a>Tworzenie SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite-EBS, PeopleSoft i JDE użytkownika testowego

Po pomyślnym uwierzytelnieniu usługa Azure AD wysyła unikatowy identyfikator użytkownika (identyfikator nazwy) do aplikacji użytkownika.  Upewnij się, że unikatowy identyfikator użytkownika (Name ID) jest zgodny z rekordem użytkownika w aplikacji, FND_USER. Na przykład USER_NAME w programie Oracle EBS.

Skontaktuj się z pomocą [info@ssogen.com](mailto:info@ssogen.com) [support@ssogen.com](mailto:support@ssogen.com) techniczną i.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do bramy rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite-EBS, PeopleSoft i JDE adresu URL umożliwiającego zainicjowanie przepływu logowania.  

* Przejdź do SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite — EBS, PeopleSoft i JDE adres URL logowania bezpośrednio i zainicjuj w tym miejscu przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do bramy rejestracji jednokrotnej usługi SSOGEN — Azure AD dla Oracle E-Business Suite — EBS, PEOPLESOFT i JDE, dla którego skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka Logowanie jednokrotne w usłudze Azure AD dla programu Oracle Web-Business Suite-EBS, PeopleSoft i JDE w obszarze Moje aplikacje, jeśli jest ono skonfigurowane w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli jest on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do bramy rejestracji jednokrotnej usługi Azure AD dla Oracle , PeopleSoft i JDE, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi SSOGEN-Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft i JDE można wymusić kontrolę sesji, co chroni przede wszystkim ochronę danych poufnych w organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).