---
title: 'Samouczek: integracja Azure Active Directory z uwierzytelnianiem tożsamości platformy SAP Cloud Platform | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługami Azure Active Directory i SAP Cloud Platform Identity Authentication.
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
ms.openlocfilehash: dc0cd57eb32baaeac0850337bbead3a73dec9292
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897349"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą SAP Cloud Platform uwierzytelnianie tożsamości

W tym samouczku dowiesz się, jak zintegrować uwierzytelnianie tożsamości platformy SAP Cloud Platform z usługą Azure Active Directory (Azure AD). Podczas integrowania uwierzytelniania tożsamości platformy w chmurze SAP z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do uwierzytelniania tożsamości platformy SAP Cloud Platform.
* Zezwól użytkownikom na automatyczne logowanie do uwierzytelniania tożsamości platformy SAP w chmurze przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w usłudze SAP Cloud Platform Authentication.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa SAP Cloud Platform Identity Authentication obsługuje logowanie jednokrotne zainicjowane przez **dostawcę usługi** oraz **dostawcę tożsamości**

Zanim zagłębisz się w szczegóły techniczne, konieczne jest zrozumienie koncepcji, które zostaną omówione. Usługi SAP Cloud Platform Identity Authentication oraz Active Directory Federation Services umożliwiają wdrożenie logowania jednokrotnego we wszystkich aplikacjach i usługach chronionych przez usługę Azure AD (jako dostawcę tożsamości) z aplikacjami i usługami SAP chronionymi przez usługę SAP Cloud Platform Identity Authentication.

Obecnie usługa SAP Cloud Platform Identity Authentication pełni funkcję dodatkowego dostawcy tożsamości w aplikacjach SAP. Usługa Azure Active Directory z kolei pełni funkcję głównego dostawcy tożsamości w tej konfiguracji. 

Na poniższym diagramie przedstawiono tę relację:

![Tworzenie użytkownika testowego usługi Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

W tej konfiguracji dzierżawa usługi SAP Cloud Platform Identity Authentication jest konfigurowana jako zaufana aplikacja w usłudze Azure Active Directory.

Wszystkie usługi i aplikacje SAP, które chcesz zabezpieczyć w ten sposób, są następnie konfigurowane w konsoli zarządzania usługą SAP Cloud Platform Identity Authentication.

Autoryzacja dostępu do aplikacji i usług SAP musi mieć miejsce w usłudze SAP Cloud Platform Identity Authentication, a nie w usłudze Azure Active Directory.

Dzięki skonfigurowaniu usługi SAP Cloud Platform Identity Authentication jako aplikacji w witrynie Azure Active Directory Marketplace nie musisz konfigurować pojedynczych oświadczeń lub asercji SAML.

> [!NOTE]
> Obecnie tylko logowanie jednokrotne przez Internet zostało przetestowane przez obie strony. Przepływy, które są niezbędne do nawiązania komunikacji pomiędzy aplikacją a interfejsem API lub pomiędzy interfejsami API, powinny działać, ale nie zostały jeszcze przetestowane. Zostaną przetestowane później.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Dodawanie usługi SAP Cloud Platform Identity Authentication z galerii

Aby skonfigurować integrację usługi SAP Cloud Platform Identity Authentication z usługą Azure AD, należy dodać aplikację SAP Cloud Platform Identity Authentication z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SAP Cloud Platform Identity Authentication** w polu wyszukiwania.
1. Wybierz pozycję **uwierzytelnianie tożsamości platformy w chmurze SAP** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform-identity-authentication"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla oprogramowania SAP Cloud Platform uwierzytelnianie tożsamości

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą uwierzytelniania tożsamości platformy SAP Cloud Platform przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ramach uwierzytelniania tożsamości platformy SAP Cloud Platform.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu uwierzytelniania tożsamości platformy SAP Cloud Platform, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Konfigurowanie logowania JEDNOkrotnego uwierzytelniania tożsamości platformy SAP w chmurze](#configure-sap-cloud-platform-identity-authentication-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego uwierzytelniania tożsamości platformy SAP Cloud Platform](#create-sap-cloud-platform-identity-authentication-test-user)** , aby uzyskać odpowiednik elementu B. Simon w ramach uwierzytelniania tożsamości platformy SAP Cloud Platform, który jest połączony z reprezentacją usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji do **uwierzytelniania tożsamości platformy SAP Cloud Platform** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować tryb zainicjowany przy użyciu programu **dostawcy tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `<IAS-tenant-id>.accounts.ondemand.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta usługi SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html), aby uzyskać te wartości. Jeśli nie rozumiesz wartości identyfikatora, zapoznaj się z tematem [Tenant SAML 2.0 configuration (Konfiguracja formatu SAML 2.0 dzierżawy)](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html) w dokumentacji usługi SAP Cloud Platform Identity Authentication.

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie zainicjowanym przez program **SP**:

    ![Domena i adresy URL usługi SAP Cloud Platform Identity Authentication — informacje dotyczące logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Użyj konkretnego adresu URL logowania aplikacji biznesowej. Skontaktuj się z [zespołem obsługi klienta usługi SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html), jeśli masz jakiekolwiek wątpliwości.

1. Aplikacja do uwierzytelniania tożsamości platformy Cloud Platform SAP oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja do uwierzytelniania tożsamości platformy w chmurze SAP oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | firstName | user.givenname |

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

9. W sekcji **Konfigurowanie usługi SAP Cloud Platform Identity Authentication** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do uwierzytelniania tożsamości platformy SAP Cloud Platform.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **SAP Cloud Platform Identity Authentication**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".

1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>Konfigurowanie tożsamości logowania jednokrotnego uwierzytelniania platformy SAP Cloud Platform

1. Aby zautomatyzować konfigurację w ramach uwierzytelniania tożsamości platformy SAP w chmurze, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Skonfiguruj rozwiązanie SAP Cloud Platform uwierzytelnianie tożsamości** spowoduje przekierowanie do aplikacji SAP Cloud Platform Identity Authentication. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do uwierzytelniania tożsamości platformy SAP Cloud Platform. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować uwierzytelnianie tożsamości platformy SAP w chmurze, w innym oknie przeglądarki sieci Web przejdź do konsoli administracyjnej uwierzytelniania platformy SAP Cloud Identity Authentication Console. Adres URL odpowiada następującemu wzorcowi: `https://<tenant-id>.accounts.ondemand.com/admin`. Następnie zapoznaj się z tematem [Integration with Microsoft Azure AD (Integracja z usługą Microsoft Azure AD)](https://developers.sap.com/tutorials/cp-ias-azure-ad.html) w dokumentacji usługi SAP Cloud Platform Identity Authentication.

2. W witrynie Azure Portal wybierz przycisk **Zapisz**.

3. Wykonuj dalsze czynności tylko, jeśli chcesz dodać i włączyć logowanie jednokrotne dla kolejnej aplikacji SAP. Powtórz kroki opisane w sekcji **Dodawanie usługi SAP Cloud Platform Identity Authentication z galerii**.

4. W witrynie Azure Portal na stronie integracji aplikacji **SAP Cloud Platform Identity Authentication**, wybierz pozycję **Połączone logowanie**.

    ![Konfigurowanie funkcji Połączone logowanie](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked-sign-on.png)

5. Zapisz konfigurację.

> [!NOTE]
> Nowa aplikacja wykorzystuje konfigurację pojedynczego logowania poprzedniej aplikacji SAP. Upewnij się, że korzystasz z tych samych firmowych dostawców tożsamości w konsoli administracyjnej usługi SAP Cloud Platform Identity Authentication.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Tworzenie użytkownika testowego usługi SAP Cloud Platform Identity Authentication

Nie musisz tworzyć użytkownika w usłudze SAP Cloud Platform Identity Authentication. Użytkownicy, którzy są w magazynie użytkowników usługi Azure AD, mogą korzystać z funkcji logowania jednokrotnego.

Usługa SAP Cloud Platform Identity Authentication obsługuje opcję federacji tożsamości. Ta opcja umożliwia aplikacji sprawdzenie, czy użytkownicy, którzy są uwierzytelniani przez firmowego dostawcę tożsamości, istnieją w magazynie użytkowników usługi SAP Cloud Platform Identity Authentication.

Opcja federacji tożsamości jest domyślnie wyłączona. Jeśli opcja federacji tożsamości jest włączona, tylko użytkownicy, którzy są zaimportowani w usłudze SAP Cloud Platform Identity Authentication, mogą uzyskać dostęp do aplikacji.

Abu uzyskać więcej informacji na temat włączania lub wyłączania funkcji federacji tożsamości w usłudze SAP Cloud Platform Identity Authentication, zobacz „Enable Identity Federation with SAP Cloud Platform Identity Authentication” („Włączanie funkcji federacji tożsamości w usłudze SAP Cloud Platform Identity Authentication”) w temacie [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication (Konfigurowanie funkcji federacji tożsamości z magazynem użytkowników w usłudze SAP Cloud Platform Identity Authentication)](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/c029bbbaefbf4350af15115396ba14e2.html).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania tożsamości usługi SAP Cloud Platform, gdzie można zainicjować przepływ logowania.

* Przejdź do adresu URL logowania tożsamości usługi SAP Cloud Platform bezpośrednio i zainicjuj w tym miejscu przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do uwierzytelniania tożsamości platformy SAP Cloud Platform, dla którego skonfigurowano Logowanie jednokrotne

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka uwierzytelnianie tożsamości platformy SAP Cloud Platform w obszarze Moje aplikacje, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do uwierzytelniania tożsamości platformy SAP Cloud Platform, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu uwierzytelniania tożsamości platformy SAP Cloud Platform można wymusić kontrolki sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)