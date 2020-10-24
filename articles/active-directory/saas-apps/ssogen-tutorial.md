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
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: b00aaabb68da13840cc167e235a7cedea4aabe97
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522006"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO, Single Sign-on) z usługą SSOGEN — Brama rejestracji jednokrotnej w usłudze Azure AD dla Oracle E-Business Suite — EBS, PeopleSoft i JDE

W tym samouczku dowiesz się, jak zintegrować usługę SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite — EBS, PeopleSoft i JDE z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi SSOGEN — bramy rejestracji jednokrotnej w usłudze Azure AD dla programu Oracle E-Business Suite — EBS, PeopleSoft i JDE z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite — EBS, PeopleSoft i JDE.
* Zezwól użytkownikom na automatyczne logowanie do SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite — EBS, PeopleSoft i JDE z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite — EBS, PeopleSoft i JDE z obsługą logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite-EBS, PeopleSoft i JDE obsługuje logowanie jednokrotne z użyciem usług **SP i dostawcy tożsamości** .
* Po skonfigurowaniu bramy rejestracji jednokrotnej SSOGEN-Azure AD dla programu Oracle E-Business Suite-EBS, PeopleSoft i JDE można wymusić kontrolki sesji, które chronią w czasie rzeczywistym dane poufnych danych organizacji i ich filtrowanie. Kontrolki sesji wykraczają poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>Dodawanie SSOGEN-Azure AD SSO Gateway for Oracle Web-Business Suite-EBS, PeopleSoft i JDE z galerii

Aby skonfigurować integrację usługi SSOGEN-Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft i JDE w usłudze Azure AD, należy dodać SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite-EBS, PeopleSoft i JDE z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SSOGEN-Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft i JDE** w polu wyszukiwania.
1. Wybierz pozycję **SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite — EBS, PeopleSoft i JDE** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite — EBS, PeopleSoft i JDE

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite — EBS, PeopleSoft i JDE przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite — EBS, PeopleSoft i JDE.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite — EBS, PeopleSoft i JDE, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj usługę SSOGEN Azure AD SSO Gateway for Oracle E Business Suite EBS, PeopleSoft i JDE logowanie](#configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso)** jednokrotne, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz bramę rejestracji JEDNOkrotnej usługi Azure AD dla programu Oracle E Business Suite EBS, PeopleSoft i JDE użytkownika testowego](#create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user)** , aby uzyskać odpowiedni odpowiednik B. Simon w SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite — EBS, PEOPLESOFT i JDE, która jest połączona z reprezentacją usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/), na stronie **SSOGEN-Azure AD SSO Gateway for Oracle Web-Business Suite-EBS, PeopleSoft i JDE** Application Integration, Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

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
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do bramy rejestracji jednokrotnej usługi Azure AD dla programu Oracle dla firmowych zestawów EBS, PeopleSoft i JDE.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite — EBS, PeopleSoft i JDE**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso"></a>Konfigurowanie usługi SSOGEN Azure AD SSO Gateway for Oracle E Business Suite EBS, PeopleSoft i JDE SSO

Aby skonfigurować Logowanie jednokrotne w usłudze **SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite-EBS, PeopleSoft i JDE** , należy znaleźć w dokumentacji dotyczącej rejestracji SSO specyficznej dla aplikacji poniżej:

* Oracle EBS — integracja z logowaniem jednokrotnym w usłudze Azure AD: [https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* PeopleSoft — integracja z logowaniem jednokrotnym w usłudze Azure AD: [https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* JD Edwards — integracja z logowaniem jednokrotnym w usłudze Azure AD: [https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Integracja z usługą Azure AD SSO: [https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user"></a>Tworzenie bramy rejestracji jednokrotnej usługi SSOGEN Azure AD dla oprogramowania Oracle E Business Suite EBS, PeopleSoft i JDE użytkownika testowego

Po pomyślnym uwierzytelnieniu usługa Azure AD wysyła unikatowy identyfikator użytkownika (identyfikator nazwy) do aplikacji użytkownika.  Upewnij się, że unikatowy identyfikator użytkownika (Name ID) jest zgodny z rekordem użytkownika w aplikacji, FND_USER. Na przykład USER_NAME w programie Oracle EBS.

Skontaktuj się z pomocą [info@ssogen.com](mailto:info@ssogen.com) [support@ssogen.com](mailto:support@ssogen.com) techniczną i.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Logowanie jednokrotne usługi Azure AD dla programu Oracle Web-Business Suite-EBS, PeopleSoft i JDE w panelu dostępu należy automatycznie zalogować się do bramy rejestracji jednokrotnej usługi Azure AD dla programu Oracle dla firm SSOGEN, EBS i PEOPLESOFT, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj usługę SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla programu Oracle E-Business Suite — EBS, PeopleSoft i JDE z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chronić SSOGEN — Brama rejestracji jednokrotnej usługi Azure AD dla Oracle E-Business Suite-EBS, PeopleSoft i JDE z zaawansowaną widocznością i kontrolkami](/cloud-app-security/proxy-intro-aad)