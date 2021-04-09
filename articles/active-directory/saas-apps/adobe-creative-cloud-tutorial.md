---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z Adobe Creative Cloud | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i rozwiązaniem Adobe Creative Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 553f77cdb42aa0adb230ee3efd7bec7e9fbfa972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653375"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Samouczek: integracja logowania jednokrotnego (SSO) Azure Active Directory z usługą Adobe Creative Cloud

W tym samouczku dowiesz się, jak zintegrować Adobe Creative Cloud z usługą Azure Active Directory (Azure AD). Podczas integrowania Adobe Creative Cloud z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Adobe Creative Cloud.
* Zezwól użytkownikom na automatyczne logowanie do Adobe Creative Cloud przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO) Adobe Creative Cloud.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozwiązanie Adobe Creative Cloud obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="add-adobe-creative-cloud-from-the-gallery"></a>Dodawanie Adobe Creative Cloud z galerii

Aby skonfigurować integrację rozwiązania Adobe Creative Cloud z usługą Azure AD, należy dodać rozwiązanie Adobe Creative Cloud z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Adobe Creative Cloud** w polu wyszukiwania.
1. Wybierz **Adobe Creative Cloud** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-adobe-creative-cloud"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Adobe Creative Cloud

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Adobe Creative Cloud przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Adobe Creative Cloud.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Adobe Creative Cloud, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Adobe Creative Cloud Logowanie jednokrotne](#configure-adobe-creative-cloud-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz Adobe Creative Cloud użytkownika testowego](#create-adobe-creative-cloud-test-user)** , aby dysponować odpowiednikiem B. Simon w Adobe Creative Cloud, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na Azure Portal na stronie integracja aplikacji **Adobe Creative Cloud** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz następujący adres URL: `https://adobe.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Wartość identyfikatora nie jest prawdziwa. Postępuj zgodnie ze wskazówkami w sekcji Krok 4 **konfigurowania logowania jednokrotnego w chmurze firmy Adobe** . W tym celu można otworzyć **plik XML metadanych Federacji** i pobrać z niego wartość identyfikatora jednostki i umieścić ją jako wartość identyfikatora w konfiguracji usługi Azure AD. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja Adobe Creative Cloud oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/edit-attribute.png)

1. Oprócz powyższych, aplikacja Adobe Creative Cloud oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    |----- | --------- |
    | FirstName (Imię) | user.givenname |
    | LastName (Nazwisko) | user.surname |
    | E-mail | user.mail |

    > [!NOTE]
    > Aby wartość "ExO" w odpowiedzi SAML była wypełniana, użytkownicy muszą mieć prawidłową licencję Microsoft 365.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **dane XML Federacji**, a następnie wybierz pozycję **Pobierz** , aby pobrać plik metadanych XML i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **konfigurowanie Adobe Creative Cloud** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do Adobe Creative Cloud.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Adobe Creative Cloud**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**. Następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy użytkowników. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-adobe-creative-cloud-sso"></a>Skonfiguruj Adobe Creative Cloud Logowanie jednokrotne

1. W innym oknie przeglądarki sieci Web Zaloguj się do [konsoli administracyjnej programu Adobe](https://adminconsole.adobe.com) jako administrator systemu.

1. Przejdź do pozycji **Ustawienia** na górnym pasku nawigacyjnym, a następnie wybierz pozycję **tożsamość**. Zostanie otwarta lista katalogów. Wybierz żądany katalog federacyjny.

1. Na stronie **szczegóły katalogu** wybierz pozycję **Konfiguruj**.

1. Skopiuj identyfikator jednostki i adres URL ACS (adres URL usługi Konsumenckej potwierdzenia lub adres URL odpowiedzi). Wprowadź adresy URL w odpowiednich polach Azure Portal.

    ![Skonfiguruj Logowanie jednokrotne po stronie aplikacji](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. W oknie dialogowym **Konfigurowanie ustawień aplikacji** należy użyć wartości **identyfikatora jednostki** firmy Adobe dostarczonej przez użytkownika.

    b. Użyj adresu URL usług ACS (adresu URL usługi Konsumenckej potwierdzenia) firmy Adobe pod warunkiem, że **adres URL odpowiedzi** jest dostępny w oknie dialogowym **Konfigurowanie ustawień aplikacji** .

1. W dolnej części strony Przekaż plik **XML danych federacyjnych** pobrany z Azure Portal. 

    ![Plik XML danych federacyjnych](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "Plik XML metadanych dostawcy tożsamości")

1. Wybierz pozycję **Zapisz**.

### <a name="create-adobe-creative-cloud-test-user"></a>Tworzenie użytkownika testowego rozwiązania Adobe Creative Cloud

Aby umożliwić użytkownikom usługi Azure AD logowanie się do Adobe Creative Cloud, muszą one być obsługiwane w Adobe Creative Cloud. W przypadku rozwiązania Adobe Creative Cloud aprowizowanie jest zadaniem ręcznym.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aby aprowizować konta użytkowników, wykonaj następujące czynności:

1. Zaloguj się w witrynie konsoli administracyjnej [Adobe Admin Console](https://adminconsole.adobe.com) jako administrator.

2. W konsoli firmy Adobe dodaj użytkownika z identyfikatorem Federated ID i przypisz go do profilu produktu. Aby uzyskać szczegółowe informacje na temat dodawania użytkowników, zobacz [Dodawanie użytkowników w konsoli administracyjnej programu Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers).

3. W tym momencie wpisz adres e-mail/nazwę UPN w formularzu Adobe Sign-in, naciśnij klawisz Tab, a następnie zastąp z powrotem do usługi Azure AD:
   * Web Access: \. Logowanie za Adobe.com www >
   * W narzędziu aplikacji klasycznej > logowanie
   * W aplikacji > pomoc > logowanie

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Adobe Creative Cloud, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania Adobe Creative Cloud i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Adobe Creative Cloud w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania Adobe Creative Cloud. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Adobe Creative Cloud można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)