---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą AWS Single-Account Access | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedynczej między usługą Azure Active Directory a usługą AWS Single-Account Access.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: jeedes
ms.openlocfilehash: eb469c757e2898a9925dd7d3358cfe95734cb2e9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537718"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-account-access"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą AWS Single-Account Access

Z tego samouczka dowiesz się, jak zintegrować usługę AWS Single-Account Access z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi AWS Single-Account Access z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do usługi AWS Single-Account Access.
* Umożliwianie użytkownikom automatycznego zalogowania się do usługi AWS Single-Account Access przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="understanding-the-different-aws-applications-in-the-azure-ad-application-gallery"></a>Opis różnych aplikacji platformy AWS w galerii aplikacji usługi Azure AD
Skorzystaj z poniższych informacji, aby podjąć decyzję o użyciu aplikacji AWS Single Sign-On i AWS Single-Account Access w galerii aplikacji usługi Azure AD.

**Logowanie pojedynczej w u platformie AWS**

[Logowanie pojedynczej usługi AWS](./aws-single-sign-on-tutorial.md) zostało dodane do galerii aplikacji usługi Azure AD w lutym 2021 r. Ułatwia ona centralne zarządzanie dostępem do wielu kont usług AWS i aplikacji usług AWS przy użyciu logowania za pośrednictwem Microsoft Azure AD. Federuj Microsoft Azure AD usługą AWS SSO i używaj logowania jednokrotnego usługi AWS do zarządzania uprawnieniami na wszystkich kontach usług AWS w jednym miejscu. Logowanie jednokrotne na platformie AWS automatycznie apowiuje uprawnienia i aktualizuje je w przypadku aktualizowania zasad i przypisań dostępu. Użytkownicy końcowi mogą uwierzytelniać się przy użyciu poświadczeń usługi Azure AD, aby uzyskać dostęp do konsoli platformy AWS, interfejsu wiersza polecenia i zintegrowanych aplikacji usługi AWS SSO.

**AWS Single-Account Access**

[Usługa AWS Single-Account Access]() była używana przez klientów w ciągu ostatnich kilku lat i umożliwia federowanie usługi Azure AD z jednym kontem usług AWS oraz zarządzanie dostępem do ról IAM usług AWS za pomocą usługi Azure AD. Administratorzy usługi AWS IAM definiują role i zasady na każdym koncie usług AWS. Dla każdego konta platformy AWS administratorzy usługi Azure AD federują z usługą AWS IAM, przypisują użytkowników lub grupy do konta i konfigurują usługę Azure AD do wysyłania asercji, które autoryzują dostęp roli.  

| Cecha | AWS Single Sign-On | AWS Single-Account Access |
|:--- |:---:|:---:|
|Dostęp warunkowy| Obsługuje pojedyncze zasady dostępu warunkowego dla wszystkich kont usług AWS. | Obsługuje pojedyncze zasady dostępu warunkowego dla wszystkich kont lub zasad niestandardowych na konto|
| Dostęp do interfejsu wiersza polecenia | Obsługiwane | Obsługiwane|
| Privileged Identity Management | Jeszcze nie obsługiwane | Jeszcze nie obsługiwane |
| Scentralizowane zarządzanie kontami | Scentralizowanie zarządzania kontami na platformie AWS. | Scentralizowanie zarządzania kontami w usłudze Azure AD (prawdopodobnie będzie wymagać aplikacji dla przedsiębiorstw usługi Azure AD na konto). |
| Certyfikat SAML| Pojedynczy certyfikat| Oddzielne certyfikaty na aplikację/konto | 

## <a name="aws-single-account-access-architecture"></a>Architektura usługi AWS Single-Account Access
![Diagram relacji usług Azure AD i AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Dla wielu wystąpień można skonfigurować wiele identyfikatorów. Na przykład:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Dzięki tym wartościom usługa Azure AD usuwa wartość i wysyła poprawną wartość jako adres **#** `https://signin.aws.amazon.com/saml` URL odbiorców w tokenie SAML.

Zalecamy takie podejście z następujących powodów:

- Każda aplikacja zapewnia unikatowy certyfikat X509. Każde wystąpienie wystąpienia aplikacji usługi AWS może mieć inną datę wygaśnięcia certyfikatu, którą można zarządzać na podstawie poszczególnych kont usług AWS. Ogólne przewłacanie certyfikatów jest łatwiejsze w tym przypadku.

- Możesz włączyć aprowizowanie użytkowników za pomocą aplikacji platformy AWS w usłudze Azure AD, a następnie nasza usługa pobierze wszystkie role z tego konta platformy AWS. Nie musisz ręcznie dodawać ani aktualizować ról usług AWS w aplikacji.

- Właściciela aplikacji można przypisać indywidualnie. Ta osoba może zarządzać aplikacją bezpośrednio w usłudze Azure AD.

> [!Note]
> Upewnij się, że używasz tylko aplikacji z galerii.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi AWS z obsługą logowania jednokrotnego.

> [!Note]
> Ról nie należy edytować ręcznie w usłudze Azure AD podczas wykonywania operacji importu ról.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program AWS Single-Account Access obsługuje logowanie **jednokrotne inicjowane przez sp i IDP.**

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc w jednej dzierżawie można skonfigurować tylko jedno wystąpienie.

## <a name="adding-aws-single-account-access-from-the-gallery"></a>Dodawanie aplikacji AWS Single-Account Access z galerii

Aby skonfigurować integrację usługi AWS Single-Account Access z usługą Azure AD, należy dodać usługę AWS Single-Account Access z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub konta konto Microsoft.
1. W Azure Portal wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W menu przeglądu Azure Active Directory wybierz pozycję **Aplikacje dla przedsiębiorstw** Wszystkie  >  **aplikacje.**
1. Wybierz **pozycję Nowa aplikacja,** aby dodać aplikację.
1. W sekcji **Dodaj z galerii** wpisz **AWS Single-Account Access** w polu wyszukiwania.
1. Wybierz **pozycję AWS Single-Account Access** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-aws-single-account-access"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla programu AWS Single-Account Access

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z usługą AWS Single-Account Access przy użyciu użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem usługi AWS Single-Account Access.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z usługą AWS Single-Account Access, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedynczej usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD —](#assign-the-azure-ad-test-user)** aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie usługi AWS Single-Account Access SSO](#configure-aws-single-account-access-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji AWS Single-Account Access](#create-aws-single-account-access-test-user)** — aby mieć w usłudze AWS Single-Account Access odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
    1. **[Jak skonfigurować aprowizowanie ról w programie AWS Single-Account Access](#how-to-configure-role-provisioning-in-aws-single-account-access)**
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na stronie Azure Portal **aplikacji AWS Single-Account Access** znajdź sekcję Zarządzanie  i wybierz pozycję **Logowanie pojedynczej.**
1. Na stronie **Select a single sign-on method (Wybieranie** metody logowania pojedynczego) wybierz pozycję **SAML**.
1. Na stronie Konfigurowanie logowania pojedynczego za pomocą **saml** kliknij ikonę edytowania/pióra dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** zaktualizuj identyfikator **(identyfikator jednostki)** i adres **URL odpowiedzi** przy użyciu tej samej wartości domyślnej: `https://signin.aws.amazon.com/saml` . Musisz wybrać pozycję **Zapisz,** aby zapisać zmiany konfiguracji.

1. Podczas konfigurowania więcej niż jednego wystąpienia podaj wartość identyfikatora. Od drugiego wystąpienia użyj następującego formatu, łącznie ze **#** znakiem, aby określić unikatową wartość spn.

    `https://signin.aws.amazon.com/saml#2`

1. Aplikacja AWS oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych aplikacja AWS oczekuje jeszcze kilku atrybutów, które powinny zostać przekazane w odpowiedzi SAML, jak pokazano poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.
    
    | Nazwa  | Atrybut źródłowy  | Przestrzeń nazw |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Rola | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes` |
    | SessionDuration | „podaj wartość z zakresu od 900 sekund (15 minut) do 43200 sekund (12 godzin)” |  `https://aws.amazon.com/SAML/Attributes` |

    > [!NOTE]
    > AWS oczekuje ról dla użytkowników przypisanych do aplikacji. Skonfiguruj te role w usłudze Azure AD, aby użytkownikom można było przypisać odpowiednie role. Aby dowiedzieć się, jak skonfigurować role w usłudze Azure AD, zobacz [tutaj](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)

1. Na **stronie Konfigurowanie logowania** pojedynczego za pomocą saml w oknie dialogowym Certyfikat podpisywania **SAML** (krok 3) wybierz pozycję **Dodaj certyfikat.**

    ![Tworzenie nowego certyfikatu SAML](common/add-saml-certificate.png)

1. Wygeneruj nowy certyfikat podpisywania SAML, a następnie wybierz **pozycję Nowy certyfikat.** Wprowadź adres e-mail dla powiadomień o certyfikatach.
   
    ![Nowy certyfikat SAML](common/new-saml-certificate.png) 

1. W sekcji **Certyfikat podpisywania SAML** znajdź plik  **XML** metadanych federacji i wybierz pozycję Pobierz, aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](./media/amazon-web-service-tutorial/certificate.png)

1. W **sekcji Konfigurowanie usługi AWS Single-Account Access** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w aplikacji o nazwie Azure Portal B.Simon.

1. W Azure Portal wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W menu przeglądu Azure Active Directory wybierz pozycję **Użytkownicy**  >  **Wszyscy użytkownicy.**
1. Wybierz **pozycję Nowy** użytkownik w górnej części ekranu.
1. We **właściwościach** Użytkownik wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **polu Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji AWS Single-Account Access.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **AWS Single-Account Access.**
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy w** **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie została ustawiona żadna rola, zobaczysz wybraną rolę "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-aws-single-account-access-sso"></a>Konfigurowanie logowania jednokrotnego Single-Account AWS Single-Account Access

1. W innym oknie przeglądarki zaloguj się do firmowej witryny usługi AWS jako administrator.

2. Wybierz pozycję **Strona główna usług AWS.**

    ![Zrzut ekranu przedstawiający witrynę firmową usług AWS z wyróżniona ikoną strona główna usług AWS][11]

3. Wybierz **pozycję Zarządzanie tożsamościami i dostępem.**

    ![Zrzut ekranu przedstawiający stronę usług AWS z wyróżnioną usługą IAM][12]

4. Wybierz **pozycję Dostawcy tożsamości Utwórz**  >  **dostawcę.**

    ![Zrzut ekranu przedstawiający stronę IAM (IAM) z wyróżnioną stroną Identity Providers (Dostawcy tożsamości) i Create Provider (Utwórz dostawcę)][13]

5. Na stronie **Konfigurowanie dostawcy** wykonaj następujące kroki:

    ![Zrzut ekranu przedstawiający konfigurowanie dostawcy][14]

    a. W **przypadku opcji Typ dostawcy** wybierz pozycję **SAML.**

    b. W **nazwa dostawcy**, wpisz nazwę dostawcy (na przykład: *WAAD*).

    c. Aby przekazać pobrany **plik metadanych z** Azure Portal wybierz **pozycję Wybierz plik**.

    d. Wybierz **pozycję Następny krok.**

6. Na stronie **Verify Provider Information (Weryfikowanie informacji o dostawcy)** wybierz **pozycję Create (Utwórz).**

    ![Zrzut ekranu przedstawiający weryfikację informacji o dostawcy z wyróżnionem tworzeniem][15]

7. Wybierz **pozycję Role** Utwórz  >  **rolę.**

    ![Zrzut ekranu przedstawiający stronę Role][16]

8. Na stronie **Create role** (Tworzenie roli) wykonaj następujące kroki:  

    ![Zrzut ekranu przedstawiający stronę Tworzenie roli][19]

    a. W **obszarze Wybierz typ zaufanej jednostki** wybierz pozycję **Federacja SAML 2.0.**

    b. W **obszarze Wybierz dostawcę SAML 2.0** wybierz utworzonego wcześniej dostawcę **SAML** (na *przykład: WAAD*).

    c. Wybierz pozycję **Allow programmatic and AWS Management Console access** (Zezwalaj na dostęp programowy i za pomocą konsoli AWS Management Console).
  
    d. Wybierz opcję **Dalej: Permissions** (Dalej: uprawnienia).

9. W **oknie dialogowym Dołączanie** zasad uprawnień dołącz odpowiednie zasady dla swojej organizacji. Następnie wybierz pozycję **Dalej: Przejrzyj**.  

    ![Zrzut ekranu przedstawiający okno dialogowe Dołączanie zasad uprawnień][33]

10. W **oknie dialogowym** Review (Przegląd) wykonaj następujące kroki:

    ![Zrzut ekranu przedstawiający okno dialogowe Przegląd][34]

    a. W **nazwa roli**, wprowadź nazwę roli.

    b. W **polu Opis** roli wprowadź opis.

    c. Wybierz **pozycję Utwórz rolę**.

    d. Utwórz tyle ról, ile jest potrzebnych, i zamapuj je na dostawcę tożsamości.

11. Użyj poświadczeń konta usługi AWS do pobrania ról z konta platformy AWS w ramach aprowowania użytkowników w usłudze Azure AD. W tym celu otwórz stronę główną konsoli usług AWS.

12. Wybierz **pozycję Usługi.** W **obszarze Zabezpieczenia, Zgodność & tożsamościami** wybierz pozycję **IAM**.

    ![Zrzut ekranu strony głównej konsoli usług AWS z wyróżnioną oknie Usługi i IAM](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. W sekcji IAM (IAM) wybierz pozycję **Policies (Zasady).**

    ![Zrzut ekranu przedstawiający sekcję IAM z wyróżnioną chybabędszą erą zasad](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Utwórz nowe zasady, wybierając pozycję **Utwórz** zasady w celu pobrania ról z konta platformy AWS w ramach aprowowania użytkowników w usłudze Azure AD.

    ![Zrzut ekranu przedstawiający stronę Tworzenie roli z wyróżnieniami Utwórz zasady](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Utwórz własne zasady, aby pobrać wszystkie role z kont usług AWS.

    ![Zrzut ekranu przedstawiający stronę Tworzenie zasad z wyróżnioną json](./media/amazon-web-service-tutorial/policy1.png)

    a. W **obszarze Tworzenie** zasad wybierz **kartę JSON.**

    b. W dokumencie zasad dodaj następujący kod JSON:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Wybierz **pozycję Przejrzyj zasady,** aby zweryfikować zasady.

    ![Zrzut ekranu przedstawiający stronę Tworzenie zasad](./media/amazon-web-service-tutorial/policy5.png)

16. Zdefiniuj nowe zasady.

    ![Zrzut ekranu przedstawiający stronę Tworzenie zasad z wyróżnieniami pól Nazwa i Opis](./media/amazon-web-service-tutorial/policy2.png)

    a. W **polach** Nazwa **wprowadź AzureAD_SSOUserRole_Policy**.

    b. W **polu Opis** wprowadź wartość Te zasady **umożliwią pobranie ról z kont usług AWS.**

    c. Wybierz pozycję **Create policy** (Utwórz zasady).

17. Utwórz nowe konto użytkownika w usłudze AWS IAM.

    a. W konsoli usługi AWS IAM wybierz pozycję **Użytkownicy.**

    ![Zrzut ekranu przedstawiający konsolę AWS IAM z wyróżnioną oknie Użytkownicy](./media/amazon-web-service-tutorial/policy3.png)

    b. Aby utworzyć nowego użytkownika, wybierz pozycję **Dodaj użytkownika.**

    ![Zrzut ekranu przedstawiający przycisk Dodaj użytkownika](./media/amazon-web-service-tutorial/policy4.png)

    c. W sekcji **Dodawanie** użytkownika:

    ![Zrzut ekranu przedstawiający stronę Dodawanie użytkownika z wyróżnieniami Nazwa użytkownika i Typ dostępu](./media/amazon-web-service-tutorial/adduser1.png)

    * Podaj nazwę użytkownika: **AzureADRoleManager**.

    * Jako typ dostępu wybierz pozycję **Dostęp programowy.** W ten sposób użytkownik może wywołać interfejsy API i pobrać role z konta platformy AWS.

    * Wybierz **pozycję Następne uprawnienia.**

18. Utwórz nowe zasady dla tego użytkownika.

    ![Zrzut ekranu przedstawia stronę Dodawanie użytkownika, na której można utworzyć zasady dla użytkownika.](./media/amazon-web-service-tutorial/adduser2.png)

    a. Wybierz **pozycję Dołącz istniejące zasady bezpośrednio.**

    b. Wyszukaj nowo utworzone zasady w sekcji **AzureAD_SSOUserRole_Policy** filtru.

    c. Wybierz zasady, a następnie wybierz pozycję **Dalej: Przejrzyj**.

19. Przejrzyj zasady dla dołączonego użytkownika.

    ![Zrzut ekranu przedstawiający stronę Dodawanie użytkownika z wyróżnieniami Utwórz użytkownika](./media/amazon-web-service-tutorial/adduser3.png)

    a. Przejrzyj nazwę użytkownika, typ dostępu i zasady zamapowane do użytkownika.

    b. Wybierz **pozycję Utwórz użytkownika.**

20. Pobierz poświadczenia użytkownika.

    ![Zrzut ekranu przedstawiający stronę Dodawanie użytkownika z przyciskiem Pobierz plik c s v w celu pobrania poświadczeń użytkownika.](./media/amazon-web-service-tutorial/adduser4.png)

    a. Skopiuj **identyfikator klucza dostępu** i **klucz dostępu wpisu tajnego**.

    b. Wprowadź te poświadczenia w sekcji aprowizowania użytkowników usługi Azure AD, aby pobrać role z konsoli platformy AWS.

    c. Wybierz pozycję **Zamknij**.

### <a name="how-to-configure-role-provisioning-in-aws-single-account-access"></a>Jak skonfigurować aprowizowanie ról na platformie AWS Single-Account Access

1. W portalu zarządzania usługi Azure AD w aplikacji AWS przejdź do tematu **Provisioning (Aprowizowanie).**

    ![Zrzut ekranu aplikacji AWS z wyróżnionem aprowizowanie](./media/amazon-web-service-tutorial/provisioning.png)

2. Wprowadź klucz dostępu i wpis tajny odpowiednio w **polach clientsecret** (klient) **i Secret Token (Token** tajny).

    ![Zrzut ekranu przedstawiający okno dialogowe Poświadczenia administratora](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Podaj klucz dostępu użytkownika usług AWS w polu **clientsecret**.

    b. Podaj wpis tajny użytkownika usług AWS w polu **Secret Token** (Token wpisu tajnego).

    c. Wybierz pozycję **Testuj połączenie**.

    d. Zapisz ustawienie, wybierając pozycję **Zapisz.**

3. W sekcji **Ustawienia** w sekcji **Stan aprowowania** wybierz **pozycję Wł.**. Następnie wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający sekcję Ustawienia z wyróżnionem wł.](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> Usługa aprowizowania importuje role tylko z usług AWS do usługi Azure AD. Usługa nie aprowizuje użytkowników i grup z usługi Azure AD na platformie AWS.

> [!NOTE]
> Po zapisaniu poświadczeń aprowizowania należy poczekać na uruchomienie początkowego cyklu synchronizacji. Synchronizacja zwykle trwa około 40 minut. Stan jest wyświetlony w dolnej części strony Inicjowanie **obsługi** administracyjnej w obszarze **Bieżący stan.**

### <a name="create-aws-single-account-access-test-user"></a>Tworzenie użytkownika testowego Single-Account AWS Single-Account Access

Celem tej sekcji jest utworzenie użytkownika o nazwie B.Simon w aplikacji AWS Single-Account Access. Program AWS Single-Account Access nie wymaga utworzenia użytkownika w systemie na potrzeby logowania jednokrotnego, dlatego nie trzeba wykonywać żadnych działań w tym miejscu.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Inicjowane przez sp:

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Single-Account AWS, pod którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do Single-Account AWS access sign-on URL (Adres URL logowania do usługi AWS i zainicjuj przepływ logowania).

#### <a name="idp-initiated"></a>Inicjowane przez IDP:

* Kliknij pozycję **Test this application** in Azure Portal and you should be automatically signed in to AWS Single-Account Access (Przetestuj tę aplikację na platformie Azure Portal). Powinno na celu automatyczne zalogowanie się do usługi AWS Single-Account Access, dla której została ustawiona logowanie jednokrotne 

Możesz również użyć usługi Microsoft Moje aplikacje, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka AWS Single-Account Access w trybie Moje aplikacje, jeśli jest skonfigurowany w trybie sp nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie skonfigurowane w trybie dostawcy tożsamości, powinno na celu automatyczne zalogowanie się do usługi AWS Single-Account Access, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na Moje aplikacje, [zobacz Introduction to the Moje aplikacje (Wprowadzenie do Moje aplikacje](../user-help/my-apps-portal-end-user-access.md)).


## <a name="known-issues"></a>Znane problemy

* Integracja aprow Single-Account access platformy AWS może być używana tylko do łączenia się z punktami końcowymi chmury publicznej AWS. Integracji aprow Single-Account AWS Single-Account Access nie można używać do uzyskiwania dostępu do środowisk AWS Government.
 
* W sekcji **Provisioning** (Aprowowanie) **podsekcja Mappings** (Mapowania) zawiera "Trwa ładowanie..." i nigdy nie wyświetla mapowań atrybutów. Jedynym obsługiwanym obecnie przepływem pracy aprowizowania jest importowanie ról z usług AWS do usługi Azure AD do wyboru podczas przypisywania użytkownika lub grupy. Mapowania atrybutów są wstępnie określone i nie można ich konfigurować.

* Sekcja **Aprowizacja** obsługuje wprowadzanie tylko jednego zestawu poświadczeń jednocześnie dla jednej dzierżawy usług AWS. Wszystkie zaimportowane role są zapisywane we `appRoles` właściwości obiektu usługi Azure AD [ `servicePrincipal` dla](/graph/api/resources/serviceprincipal) dzierżawy platformy AWS.

  Wiele dzierżaw platformy AWS (reprezentowanych przez usługę ) można dodać do usługi `servicePrincipals` Azure AD z galerii w celu aprowizowania. Istnieje jednak znany problem z tym, że nie można automatycznie zapisywać wszystkich zaimportowanych ról z wielu usług AWS używanych do aprowizowania w ramach jednej aplikacji używanej do logowania `servicePrincipals` `servicePrincipal` jednokrotnego.

  Aby obejść ten problem, można użyć interfejsu [API Microsoft Graph,](/graph/api/resources/serviceprincipal) aby wyodrębnić wszystkie zaimportowane dane do poszczególnych usług AWS, na których skonfigurowano `appRoles` `servicePrincipal` aprowizowanie. Następnie możesz dodać te ciągi ról do usługi AWS, `servicePrincipal` na której skonfigurowano logowanie jednokrotne.

* Aby można było zaimportować role z usług AWS do usługi Azure AD, muszą spełniać następujące wymagania:

  * Role muszą mieć dokładnie jednego dostawcę saml zdefiniowanego na platformie AWS
  * Łączna długość nazwy ARN (nazwa zasobu Amazon) dla roli i nazwy ARN skojarzonego dostawcy saml-provider musi być mniejsza niż 240 znaków.

## <a name="change-log"></a>Dziennik zmian

* 2020-01-12 — zwiększono limit długości roli z 119 do 239 znaków. 

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi AWS Single-Account Access można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png