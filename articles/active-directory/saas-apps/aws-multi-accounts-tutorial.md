---
title: 'Samouczek: Azure Active Directory integrację z usługą Amazon Web Services w celu połączenia wielu kont | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między usługą Azure AD i Amazon Web Services (starszy samouczek).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 0ddcb239ba106bcdc2f0a29d68eb395876fadc06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100384116"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services"></a>Samouczek: integracja Azure Active Directory z Amazon Web Services

W tym samouczku dowiesz się, jak zintegrować Azure Active Directory (Azure AD) z Amazon Web Services (AWS) (starszy samouczek).

Ta integracja zapewnia następujące korzyści:

- Możesz kontrolować usługę Azure AD, która ma dostęp do usługi AWS.
- Możesz umożliwić użytkownikom automatyczne logowanie do usługi AWS przy użyciu logowania jednokrotnego (SSO) z kontami usługi Azure AD.
- Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

![Diagram integracji usługi Azure AD z usługą AWS.](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Zalecamy, aby *nie* łączyć jednej aplikacji AWS z wszystkimi kontami AWS. Zamiast tego zalecamy użycie [integracji SSO usługi Azure AD z usługą AWS](./amazon-web-service-tutorial.md) w celu skonfigurowania wielu wystąpień konta AWS w wielu wystąpieniach aplikacji AWS w usłudze Azure AD. 

Zalecamy, aby *nie* łączyć jednej aplikacji AWS z wszystkimi kontami AWS, z następujących powodów:

* Tego podejścia należy używać tylko wtedy, gdy masz niewielką liczbę kont AWS i ról, ponieważ ten model nie jest skalowalny, ponieważ liczba kont AWS i ról w nich rośnie. Podejście nie korzysta z funkcji AWS role-import z obsługą administracyjną użytkowników usługi Azure AD, dlatego należy ręcznie dodawać, aktualizować lub usuwać role. 

* Musisz użyć podejścia Microsoft Graph Explorer, aby poprawić wszystkie role do aplikacji. Nie zalecamy użycia metody pliku manifestu.

* Klient raportuje, że po dodaniu przez nich ~ 1 200 ról aplikacji dla pojedynczej aplikacji AWS każda kolejna operacja w aplikacji zacznie zgłaszać błędy związane z rozmiarem. Istnieje limit rozmiaru dla obiektu aplikacji.

* Musisz ręcznie zaktualizować role w miarę ich dodawania do dowolnego konta. Jest to niestety podejście *zamiany* , a nie podejścia do *dołączania* . Ponadto, jeśli rośnie Twoje numery kont, to to *n* &times; *n* relacja z kontami i rolami.

* Wszystkie konta AWS używają tego samego pliku XML metadanych Federacji. W momencie przerzucania certyfikatów Aktualizacja certyfikatu na wszystkich kontach AWS w tym samym czasie może być czasochłonna.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą AWS, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji usługi Azure AD, możesz uzyskać [miesięczną wersję próbną](https://azure.microsoft.com/pricing/free-trial/).
* AWS subskrypcja z obsługą logowania jednokrotnego.

> [!NOTE]
> Nie zaleca się przetestowania kroków opisanych w tym samouczku w środowisku produkcyjnym, chyba że jest to konieczne.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

Usługa AWS obsługuje logowanie jednokrotne z użyciem zainicjowanych przez usługę SP i dostawcy tożsamości.

## <a name="add-aws-from-the-gallery"></a>Dodaj AWS z galerii

Aby skonfigurować integrację programu AWS z usługą Azure AD, Dodaj AWS z galerii do listy aplikacji zarządzanych oprogramowania jako usługi (SaaS).

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz usługę Azure AD, z którą chcesz korzystać.
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Amazon Web Services** w polu wyszukiwania.
1. Na liście wyników wybierz pozycję **Amazon Web Services**, a następnie Dodaj aplikację. W ciągu kilku sekund aplikacja zostanie dodana do dzierżawy.

1. Przejdź do okienka **Właściwości** , a następnie skopiuj wartość, która jest wyświetlana w polu **Identyfikator obiektu** .

    ![Zrzut ekranu przedstawiający pole Identyfikator obiektu w okienku właściwości.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD

W tej sekcji należy skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą AWS na podstawie użytkownika testowego o nazwie "Britta Simon".

Aby logowanie jednokrotne działało, usługa Azure AD musi wiedzieć, co odpowiada użytkownik w usłudze AWS, do użytkownika usługi Azure AD. Innymi słowy, należy ustanowić relację linku między użytkownikiem usługi Azure AD i tym samym użytkownikiem w AWS.

W AWS Przypisz wartość **Nazwa użytkownika** w usłudze Azure AD jako wartość **nazwy AWS,** aby określić relację łącza.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi AWS, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-aws-sso)** w usłudze AWS, aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji należy włączyć logowanie jednokrotne usługi Azure AD w Azure Portal i skonfigurować Logowanie jednokrotne w aplikacji AWS, wykonując następujące czynności:

1. W Azure Portal w lewym okienku na stronie integracja aplikacji **Amazon Web Services (AWS)** wybierz pozycję **Logowanie jednokrotne**.

    ![Zrzut ekranu przedstawiający polecenie "Logowanie jednokrotne".](common/select-sso.png)

1. W okienku **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML/WS-karmione** , aby włączyć logowanie jednokrotne.

    ![Zrzut ekranu przedstawiający okienko "Wybierz metodę logowania jednokrotnego".](common/select-saml-option.png)

1. W okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** wybierz przycisk  **Edytuj** (ikona ołówka).

    ![Zrzut ekranu przycisku Edytuj w okienku "Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML".](common/edit-urls.png)

1. Zostanie otwarte okienko **podstawowe Konfigurowanie protokołu SAML** . Pomiń tę sekcję, ponieważ aplikacja jest wstępnie zintegrowana z platformą Azure. Wybierz pozycję **Zapisz**.

   Aplikacja AWS oczekuje potwierdzeń SAML w określonym formacie. Wartościami tych atrybutów można zarządzać z sekcji " **atrybuty użytkownika & oświadczenia** na stronie **integracja aplikacji** ". 
   
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz przycisk **Edytuj** .

    ![Zrzut ekranu przycisku Edytuj w okienku "atrybuty użytkownika".](common/edit-attribute.png)

1. W sekcji **oświadczenia użytkownika** w okienku **atrybuty użytkownika** Skonfiguruj atrybut tokenu SAML przy użyciu wartości z następującej tabeli:

    | Nazwa  | Atrybut źródłowy  | Przestrzeń nazw |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Rola | user.assignedroles | `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration | "Podaj wartość z prze900 sekund (15 minut) do 43200 sekund (12 godzin)" |  `https://aws.amazon.com/SAML/Attributes` |
  
   a. Wybierz pozycję **Dodaj nowe oświadczenie** , a następnie w okienku **Zarządzaj oświadczeniami użytkowników** wykonaj następujące czynności:

   ![Zrzut ekranu przedstawiający przyciski "Dodaj nowe oświadczenie" i "Zapisz" w okienku "oświadczenia użytkownika".](common/new-save-attribute.png)

   ![Zrzut ekranu przedstawiający okienko "Zarządzaj oświadczeniami użytkownika".](common/new-attribute-details.png)

   b. W polu **Nazwa** wprowadź nazwę atrybutu.  

   c. W polu **przestrzeń nazw** wprowadź wartość przestrzeni nazw.

   d. W polu **Źródło** wybierz pozycję **atrybut**.

   e. Z listy rozwijanej **atrybut źródłowy** Wybierz atrybut.

   f. Wybierz przycisk **OK**, a następnie wybierz pozycję **Zapisz**.

   >[!NOTE]
   >Aby uzyskać więcej informacji na temat ról w usłudze Azure AD, zobacz [Dodawanie ról aplikacji do aplikacji i odbieranie ich w tokenie](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** , aby pobrać plik XML metadanych Federacji, a następnie zapisz go na komputerze.

   ![Zrzut ekranu przedstawiający link pobierania XML metadanych Federacji w okienku "certyfikat podpisywania SAML".](common/metadataxml.png)

### <a name="configure-aws-sso"></a>Konfigurowanie logowania jednokrotnego AWS

1. W nowym oknie przeglądarki Zaloguj się do witryny firmy AWS jako administrator.

1. Wybierz ikonę **główną AWS** .

   ![Zrzut ekranu przedstawiający ikonę "Strona główna" AWS.][11]

1. W okienku **usługi AWS Services** w obszarze **zabezpieczenia & tożsamość**, wybierz pozycję **IAM (Zarządzanie tożsamościami & dostępu)**.

    ![Zrzut ekranu przedstawiający link "Zarządzanie tożsamościami i dostępem" w okienku "AWS Services".][12]

1. W okienku po lewej stronie wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Utwórz dostawcę**.

    ![Zrzut ekranu przedstawiający przycisk "Utwórz dostawcę".][13]

1. W okienku **Konfiguruj dostawcę** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający okienko "Konfigurowanie dostawcy".][14]

    a. Z listy rozwijanej **Typ dostawcy** wybierz pozycję **SAML**.

    b. W polu **Nazwa dostawcy** wprowadź nazwę dostawcy (na przykład. *WAAD*).

    c. Obok pola **dokument metadanych** wybierz pozycję **Wybierz plik** , aby przekazać pobrany plik XML metadanych Federacji do Azure Portal.

    d. Wybierz **Następny krok**.

1. W okienku **Weryfikuj informacje o dostawcy** wybierz pozycję **Utwórz**.

    ![Zrzut ekranu przedstawiający okienko "Weryfikuj informacje o dostawcy".][15]

1. W okienku po lewej stronie wybierz pozycję **role**, a następnie wybierz pozycję **Utwórz rolę**.

    ![Zrzut ekranu przedstawiający przycisk "Utwórz rolę" w okienku Role.][16]

    > [!NOTE]
    > Łączna długość nazwy zasobu Amazon roli (ARN) i dostawcy SAML ARN dla importowanej roli musi być 240 lub mniej znaków.

1. Na stronie **Tworzenie roli** wykonaj następujące czynności:  

    ![Zrzut ekranu przedstawiający przycisk "zaufany obiekt" Federacji SAML 2,0 na stronie "Tworzenie roli".][19]

    a. W obszarze **Wybierz typ zaufanej jednostki** wybierz pozycję **SAML 2,0 Federation**.

    b. W obszarze **Wybierz dostawcę saml 2,0** wybierz wcześniej utworzony dostawca SAML (na przykład *WAAD*).

    c. Wybierz pozycję **Allow programmatic and AWS Management Console access** (Zezwalaj na dostęp programowy i za pomocą konsoli AWS Management Console).

    d. Wybierz opcję **Dalej: Permissions** (Dalej: uprawnienia).

1. W polu wyszukiwania wprowadź **dostęp administratora**, zaznacz pole wyboru **AdministratorAccess** , a następnie wybierz kolejno pozycje **Dalej: Tagi**.

    ![Zrzut ekranu przedstawiający listę "Policy Name" z wybranymi zasadami AdministratorAccess.](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. W okienku **Dodaj Tagi (opcjonalnie)** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający okienko "Dodawanie tagów (opcjonalnie)".](./media/aws-multi-accounts-tutorial/config2.png)

    a. W polu **klucz** wprowadź nazwę klucza (na przykład *Azureadtest*).

    b. W polu **wartość (opcjonalnie)** wprowadź wartość klucza w następującym formacie: `<accountname-aws-admin>` . Nazwa konta powinna zawierać tylko małe litery.

    c. Wybierz opcję **Dalej: Review** (Dalej: przegląd).

1. W okienku **Przegląd** wykonaj następujące czynności:

    ![Zrzut ekranu okienka Recenzja z wyróżnionymi polami "Nazwa roli" i "opis roli".][34]

    a. W polu **Nazwa roli** wprowadź wartość w następującym formacie: `<accountname-aws-admin>` .

    b. W polu **Opis roli** wprowadź wartość użytą dla nazwy roli.

    c. Wybierz pozycję **Utwórz rolę**.

    d. Utwórz dowolną liczbę ról i zamapuj je na dostawcę tożsamości.

    > [!NOTE]
    > Podobnie można utworzyć inne role, takie jak *AccountName-finanse-admin*, *AccountName-Read-Only-User*, *AccountName-DevOps-User* lub *AccountName-TPM-User*, każdy z dołączonymi do niej innymi zasadami. Te zasady ról można zmienić później, zgodnie z wymaganiami dla każdego konta AWS. Dobrym pomysłem jest zachowanie tych samych zasad dla każdej roli na kontach AWS.

1. Identyfikator konta usługi AWS należy zanotować w okienku właściwości usługi Amazon Elastic Compute Cloud (Amazon EC2) lub na pulpicie nawigacyjnym usługi IAM, jak pokazano na poniższym zrzucie ekranu:

    ![Zrzut ekranu przedstawiający miejsce wyświetlania identyfikatora konta w okienku "Zarządzanie tożsamościami i dostępem".](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Zaloguj się do Azure Portal, a następnie przejdź do pozycji **grupy**.

1. Utwórz nowe grupy o takiej samej nazwie jak w przypadku utworzonych wcześniej ról mapy IAM, a następnie zanotuj wartość w polu **Identyfikator obiektu** każdej z tych nowych grup.

    ![Zrzut ekranu przedstawiający szczegóły konta dla nowej grupy.](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Wyloguj się z bieżącego konta AWS, a następnie zaloguj się do innego konta, w którym chcesz skonfigurować Logowanie jednokrotne za pomocą usługi Azure AD.

1. Po utworzeniu wszystkich ról na kontach zostaną one wyświetlone na liście **role** dla tych kont.

    ![Zrzut ekranu listy role pokazujący nazwę, opis i zaufaną rolę każdej roli.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

Następnie należy przechwycić wszystkie role ARNs i Zaufane jednostki dla wszystkich ról na wszystkich kontach. Należy je ręcznie zmapować z aplikacją usługi Azure AD. W tym celu:

1. Wybierz każdą rolę, aby skopiować jej wartość ARN i zaufaną jednostkę. Będą one potrzebne dla wszystkich ról, które zostaną utworzone w usłudze Azure AD.

    ![Zrzut ekranu przedstawiający okienko Podsumowanie dla ARNs ról i zaufanych jednostek.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Powtórz poprzedni krok dla wszystkich ról we wszystkich kontach, a następnie Zapisz je w pliku tekstowym w następującym formacie: `<Role ARN>,<Trusted entities>` .

1. Otwórz [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer), a następnie wykonaj następujące czynności:

    a. Zaloguj się do witryny Eksploratora Microsoft Graph przy użyciu poświadczeń administratora globalnego lub współadministratora dla dzierżawy.

    b. Potrzebujesz wystarczających uprawnień do tworzenia ról. Wybierz pozycję **Modyfikuj uprawnienia**.

      ![Zrzut ekranu przedstawiający link "Modyfikowanie uprawnień" w okienku uwierzytelnianie w programie Microsoft Graph Explorer.](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Jeśli na liście uprawnień nie masz jeszcze uprawnień, które są wyświetlane na poniższym zrzucie ekranu, wybierz każdą z nich, a następnie wybierz pozycję **Modyfikuj uprawnienia**. 

      ![Zrzut ekranu listy uprawnień Eksploratora Microsoft Graph z wyróżnionymi odpowiednimi uprawnieniami.](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Zaloguj się ponownie w Eksploratorze grafu i zaakceptuj warunki użytkowania lokacji. 

    e. W górnej części okienka wybierz pozycję **Pobierz** dla metody, wybierz pozycję wersja **beta** dla wersji, a następnie w polu zapytanie Wprowadź jedną z następujących wartości: 
    
    * Aby pobrać wszystkie jednostki usługi z dzierżawy, użyj `https://graph.microsoft.com/beta/servicePrincipals` . 
    * Jeśli używasz wielu katalogów, użyj `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` , która zawiera domenę podstawową.

    ![Zrzut ekranu przedstawiający okienko zapytania Eksploratora Microsoft Graph "treść żądania".](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Na liście jednostek usługi Pobierz tę, którą chcesz zmodyfikować. 
    
    Możesz również przeszukać aplikację dla wszystkich wymienionych nazw podmiotów usługi, wybierając klawisze CTRL + F. Aby uzyskać określoną nazwę główną usługi, Dołącz do zapytania identyfikator obiektu jednostki usługi, który został wcześniej skopiowany z okienka właściwości usługi Azure AD, jak pokazano poniżej:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

      ![Zrzut ekranu przedstawiający zapytanie głównej usługi, które zawiera identyfikator obiektu.](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    przykład Wyodrębnij Właściwość appRoles z obiektu jednostki usługi.

    ![Zrzut ekranu przedstawiający kod wyodrębniania właściwości appRoles z obiektu jednostki usługi.](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Teraz musisz wygenerować nowe role dla swojej aplikacji. 

    i. Poniższy kod JSON jest przykładem obiektu appRoles. Utwórz podobny obiekt, aby dodać role, które mają być używane w aplikacji.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Nowe role można dodawać dopiero po dodaniu *msiam_access* dla operacji patch. Możesz również dodać dowolną liczbę ról, w zależności od potrzeb organizacji. Usługa Azure AD wysyła *wartość* tych ról jako wartość żądania w odpowiedzi SAML.

    j. W Eksploratorze Microsoft Graph Zmień metodę z **Get** na **patch**. Aby zastosować poprawki do obiektu jednostki usługi z żądanymi rolami, zaktualizuj właściwość appRoles, taką jak pokazana w poprzednim przykładzie. Wybierz pozycję **Uruchom zapytanie** , aby wykonać operację patch. Komunikat o powodzeniu potwierdza utworzenie roli dla aplikacji AWS.

      ![Zrzut ekranu okienka Eksploratora Microsoft Graph z metodą zmiany na poprawka.](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Po zastosowaniu poprawki jednostki usługi z większą liczbą ról można przypisać użytkowników i grupy do odpowiednich ról. Możesz to zrobić w Azure Portal, przechodząc do aplikacji AWS, a następnie wybierając kartę **Użytkownicy i grupy** u góry.

1. Zalecamy utworzenie nowej grupy dla każdej roli AWS, aby można było przypisać tę konkretną rolę w grupie. To mapowanie jeden-do-jednego oznacza, że jedna grupa jest przypisana do jednej roli. Następnie można dodać członków należących do tej grupy.

1. Po utworzeniu grup wybierz grupę i przypisz ją do aplikacji.

    ![Zrzut ekranu okienka "Użytkownicy i grupy".](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Grupy zagnieżdżone nie są obsługiwane podczas przypisywania grup.

1. Aby przypisać rolę do grupy, wybierz rolę, a następnie wybierz pozycję **Przypisz**.

    ![Zrzut ekranu przedstawiający okienko "Dodawanie przypisania".](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Po przypisaniu ról można je wyświetlić, odświeżając Azure Portal sesję.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Ta sekcja umożliwia testowanie konfiguracji logowania jednokrotnego usługi Azure AD przy użyciu aplikacji Microsoft my Apps.

Po wybraniu kafelka **AWS** w obszarze Moje aplikacje zostanie otwarta strona aplikacji AWS z opcją wyboru roli.

![Zrzut ekranu przedstawiający stronę AWS na potrzeby testowania logowania jednokrotnego.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Możesz również zweryfikować odpowiedź SAML, aby zobaczyć role przekazywane jako oświadczenia.

![Zrzut ekranu przedstawiający odpowiedź SAML.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Aby uzyskać więcej informacji o moich aplikacjach, zobacz artykuł [Logowanie i uruchamianie aplikacji z poziomu portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu AWS można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Informacje o sposobie wymuszania kontroli sesji przy użyciu Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
