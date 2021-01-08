---
title: 'Samouczek: integracja Azure Active Directory z usługą Amazon Web Services (AWS) w celu połączenia wielu kont | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między usługą Azure AD i Amazon Web Services (AWS) (starszy samouczek).
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
ms.openlocfilehash: e6b4175f4f47c9dd378bec84da2575c079a2079f
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014437"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws-legacy-tutorial"></a>Samouczek: integracja Azure Active Directory z Amazon Web Services (AWS) (starszy samouczek)

W tym samouczku dowiesz się, jak zintegrować Azure Active Directory (Azure AD) z Amazon Web Services (AWS) (starszy samouczek).

Integracja usług Amazon Web Services (AWS) z usługą Azure AD daje następujące korzyści:

- Możliwość kontrolowania za pomocą usługi Azure AD kto ma dostęp do usług Amazon Web Services (AWS).
- Możesz umożliwić użytkownikom automatyczne uzyskiwanie zalogowanych do Amazon Web Services (AWS) (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

![Usługi Amazon Web Services (AWS) na liście wyników](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Należy pamiętać, że połączenie jednej aplikacji AWS ze wszystkimi kontami AWS nie jest rozwiązaniem zalecanym. Zamiast tego zalecamy użycie [tej](./amazon-web-service-tutorial.md) metody w celu skonfigurowania wielu wystąpień konta AWS na wielu wystąpieniach aplikacji AWS w usłudze Azure AD. Tego podejścia należy używać tylko w przypadku, gdy istnieje kilka AWS kont i ról, ten model nie jest skalowalny, ponieważ konta AWS i role wewnątrz tych kont rosną. Takie podejście nie korzysta z funkcji importowania ról AWS przy użyciu aprowizacji użytkowników usługi Azure AD, dlatego należy ręcznie dodać/zaktualizować/usunąć role. Inne ograniczenia dotyczące tego podejścia można znaleźć poniżej.

**Należy pamiętać, że nie zaleca się używania tego podejścia z następujących powodów:**

* Musisz użyć podejścia Microsoft Graph Explorer, aby poprawić wszystkie role do aplikacji. Nie zalecamy użycia metody pliku manifestu.

* Widzimy, że po dodaniu ~ 1200 ról aplikacji dla pojedynczej aplikacji AWS każda operacja w aplikacji rozpoczęła generowanie błędów związanych z rozmiarem. Istnieje stały limit rozmiaru obiektu aplikacji.

* Musisz ręcznie zaktualizować rolę, ponieważ role zostaną dodane do dowolnego konta, które jest podejściem zastępującym i nie dołączaj niestety. Ponadto, jeśli Twoje konta są rosnące, to n x n relacji z kontami i rolami.

* Wszystkie konta usługi AWS będą używać tego samego pliku XML metadanych Federacji i w czasie przerzucania certyfikatów, które trzeba wykonać w celu uzyskania tego ogromnego ćwiczenia, aby zaktualizować certyfikat na wszystkich kontach AWS w tym samym czasie

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługami Amazon Web Services (AWS), są potrzebne następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usług Amazon Web Services (AWS) z włączonym logowaniem jednokrotnym

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego do testowania czynności opisanych w tym samouczku.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska usługi Azure AD w wersji próbnej, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługi Amazon Web Services (AWS) obsługują logowanie jednokrotne inicjowane przez **dostawcę usługi i dostawcę tożsamości**

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Dodawanie usług Amazon Web Services (AWS) z galerii

Aby skonfigurować integrację usług Amazon Web Services (AWS) z usługą Azure AD, należy dodać usługi Amazon Web Services (AWS) z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Amazon Web Services (AWS)** w polu wyszukiwania.
1. Wybierz pozycję **Amazon Web Services (AWS)** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

1. Po dodaniu aplikacji przejdź do strony **Właściwości** i skopiuj **Identyfikator obiektu**.

    ![Identyfikator obiektu](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Amazon Web Services (AWS) na podstawie użytkownika testowego o nazwie "Britta Simon".

Aby logowanie jednokrotne działało, usługa Azure AD musi znać, co odpowiedni użytkownik w Amazon Web Services (AWS) należy do użytkownika w usłudze Azure AD. Innymi słowy, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Amazon Web Services (AWS).

W Amazon Web Services (AWS) Przypisz wartość **Nazwa użytkownika** w usłudze Azure AD jako wartość parametru **username** , aby określić relację łącza.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą Amazon Web Services (AWS), wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Amazon Web Services (AWS) logowanie JEDNOkrotne](#configure-amazon-web-services-aws-sso)** — aby skonfigurować ustawienia pojedynczego Sign-On po stronie aplikacji.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji włączasz Logowanie jednokrotne w usłudze Azure AD w Azure Portal i skonfiguruj Logowanie jednokrotne w aplikacji Amazon Web Services (AWS).

**Aby skonfigurować logowanie jednokrotne usługi Azure AD w usługach Amazon Web Services (AWS), wykonaj następujące kroki:**

1. W Azure Portal na stronie integracja aplikacji **Amazon Web Services (AWS)** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę **ołówka** , aby otworzyć okno dialogowe **podstawowe ustawienia SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja języka SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure i kliknij przycisk **Zapisz**.

5. Aplikacja usług Amazon Web Services (AWS) oczekuje asercji SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty i oświadczenia użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij przycisk **Edytuj** , aby otworzyć okno dialogowe **atrybuty użytkownika &** oknie dialogowym oświadczenia.

    ![Zrzut ekranu przedstawia atrybuty użytkownika z kontrolką edycji o nazwie.](common/edit-attribute.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    | Nazwa  | Atrybut źródłowy  | Przestrzeń nazw |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Rola | user.assignedroles | `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration | „podaj wartość z zakresu od 900 sekund (15 minut) do 43200 sekund (12 godzin)” |  `https://aws.amazon.com/SAML/Attributes` |

    1. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

        ![Zrzut ekranu przedstawia oświadczenia użytkownika z Dodaj nowe oświadczenie i Zapisz wywołane.](common/new-save-attribute.png)

        ![Zrzut ekranu przedstawia Zarządzanie oświadczeniami użytkowników, w których można wprowadzać wartości opisane w tym kroku.](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. W polu tekstowym **przestrzeń nazw** wpisz wartość przestrzeni nazw wyświetlaną dla tego wiersza.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK** .

    przykład Kliknij pozycję **Zapisz**.

    >[!NOTE]
    >Aby uzyskać więcej informacji na temat ról w usłudze Azure AD, zobacz [tutaj](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui).

7. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** kliknij pozycję **Pobierz** , aby pobrać **plik XML metadanych Federacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Amazon Web Services (AWS)

1. W innym oknie przeglądarki zaloguj się do usług Amazon Web Services (AWS) jako administrator.

1. Kliknij pozycję **AWS Home** (Strona główna usług AWS).

    ![Konfigurowanie logowania jednokrotnego — strona główna][11]

1. Kliknij pozycję **Identity and Access Management** (Zarządzanie tożsamościami i dostępem).

    ![Konfigurowanie logowania jednokrotnego — tożsamość][12]

1. Kliknij pozycję **Identity Providers** (Dostawcy tożsamości), a następnie pozycję **Create Provider** (Utwórz dostawcę).

    ![Konfigurowanie logowania jednokrotnego — dostawca][13]

1. Na stronie okna dialogowego **Configure Provider** (Konfigurowanie dostawcy) wykonaj następujące kroki:

    ![Okno dialogowe Configure Single Sign-On (Konfigurowanie logowania jednokrotnego)][14]

    a. Dla pozycji **Provider Type** (Typ dostawcy) wybierz opcję **SAML**.

    b. W polu tekstowym **Nazwa dostawcy** wpisz nazwę dostawcy (na przykład: *WAAD*).

    c. Aby przekazać **plik metadanych** pobrany z witryny Azure Portal, kliknij pozycję **Choose File** (Wybierz plik).

    d. Kliknij pozycję **Next Step** (Następny krok).

1. Na stronie okna dialogowego **Verify Provider Information** (Weryfikowanie informacji o dostawcy) kliknij pozycję **Create** (Utwórz).

    ![Konfigurowanie logowania jednokrotnego — weryfikowanie][15]

1. Kliknij pozycję **Roles** (Role), a następnie **Create role** (Utwórz rolę).

    ![Konfigurowanie logowania jednokrotnego — role][16]

    > [!NOTE]
    > Połączona długość roli ARN i dostawca SAML ARN dla importowanej roli nie może zawierać więcej niż 240 znaków.

1. Na stronie **Create role** (Tworzenie roli) wykonaj następujące kroki:  

    ![Konfigurowanie logowania jednokrotnego — zaufanie][19]

    a. Wybierz pozycję **SAML 2.0 federation** (Federacja SAML 2.0) w obszarze **Select type of trusted entity** (Wybierz typ zaufanej jednostki).

    b. W obszarze **Wybieranie dostawcy saml 2,0** wybierz wcześniej utworzony **dostawca SAML** (na przykład: *WAAD*)

    c. Wybierz pozycję **Allow programmatic and AWS Management Console access** (Zezwalaj na dostęp programowy i za pomocą konsoli AWS Management Console).

    d. Kliknij przycisk **Dalej: uprawnienia**.

1. Wyszukaj **dostęp administratora** na pasku wyszukiwania i zaznacz pole wyboru **AdministratorAccess** , a następnie kliknij przycisk **Dalej: Tagi**.

    ![Zrzut ekranu przedstawia AdministratorAccess wybrany jako nazwa zasad.](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. W sekcji **Dodaj Tagi (opcjonalne)** wykonaj następujące czynności:

    ![Dodawanie tagów](./media/aws-multi-accounts-tutorial/config2.png)

    a. W polu tekstowym **klucz** wpisz nazwę klucza dla przykładu: Azureadtest.

    b. W polu tekstowym **wartość (opcjonalnie)** wprowadź wartość klucza przy użyciu następującego formatu `accountname-aws-admin` . Nazwa konta powinna zawierać tylko małe litery.

    c. Kliknij przycisk **Dalej: przegląd**.

1. W oknie dialogowym **Review** (Przegląd) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego — przegląd][34]

    a. W polu tekstowym **Nazwa roli** wprowadź wartość w poniższym wzorcu `accountname-aws-admin` .

    b. W polu tekstowym **Opis roli** wprowadź tę samą wartość, która była używana jako nazwa roli.

    c. Kliknij pozycję **Create Role** (Utwórz rolę).

    d. Utwórz potrzebną liczbę ról i zamapuj je na dostawcę tożsamości.

    > [!NOTE]
    > Podobnie Twórz pozostałe inne role, takie jak AccountName-finanse-admin, AccountName-Read-Only-User, AccountName-DevOps-User, AccountName-TPM-User z różnymi zasadami do dołączenia. Później te zasady ról można zmienić zgodnie z wymaganiami na konto AWS, ale zawsze lepiej jest zachować te same zasady dla każdej roli na kontach AWS.

1. Zwróć uwagę na identyfikator konta dla tego konta AWS z właściwości EC2 lub pulpitu nawigacyjnego IAM, jak pokazano poniżej:

    ![Zrzut ekranu pokazuje, gdzie konto D pojawia się w oknie A.](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Teraz Zaloguj się do Azure Portal i przejdź do **grup**.

1. Utwórz nowe grupy o takiej samej nazwie jak w przypadku utworzonych wcześniej ról mapy IAM i zanotuj **identyfikatory obiektów** tych nowych grup.

    ![Wybierz pozycję Administrator Access1](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Wyloguj się z bieżącego konta AWS i zaloguj się przy użyciu innego konta, w którym chcesz skonfigurować Logowanie jednokrotne za pomocą usługi Azure AD.

1. Po utworzeniu wszystkich ról na kontach zostaną one wyświetlone na liście **role** dla tych kont.

    ![Zrzut ekranu przedstawia listę ról o nazwie roli, opisie i zaufanych jednostkach.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

1. Musimy przechwycić wszystkie role ARN i Zaufane jednostki dla wszystkich ról na wszystkich kontach, które muszą być mapowane ręcznie przy użyciu aplikacji usługi Azure AD.

1. Kliknij role, aby skopiować wartości **ARN ról** i **zaufanych jednostek** . Te wartości są wymagane dla wszystkich ról, które należy utworzyć w usłudze Azure AD.

    ![Role Setup2](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Wykonaj powyższy krok dla wszystkich ról we wszystkich kontach i Zapisz wszystkie z nich w formacie **role ARN, Zaufane jednostki** w Notatniku.

1. Otwórz [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) w innym oknie.

    1. Zaloguj się do witryny Eksploratora Microsoft Graph przy użyciu poświadczeń administratora globalnego/współadministratora dla Twojej dzierżawy.

    1. Musisz mieć wystarczające uprawnienia do tworzenia ról. Kliknij przycisk **Modyfikuj uprawnienia** , aby uzyskać wymagane uprawnienia.

        ![BOX1 okna dialogowego Eksploratora Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    1. Wybierz następujące uprawnienia z listy (jeśli nie masz tych jeszcze) i kliknij pozycję "Modyfikuj uprawnienia" 

        ![Box2 okna dialogowego Eksploratora Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    1. Spowoduje to wyświetlenie żądania zalogowania się i zaakceptowanie zgody. Po zaakceptowaniu zgody użytkownik jest ponownie zalogowany do Microsoft Graph Explorer.

    1. Zmień listę rozwijaną wersji na wersję **beta**. Aby pobrać wszystkie jednostki usługi z dzierżawy, należy użyć następującego zapytania: `https://graph.microsoft.com/beta/servicePrincipals` . Jeśli używasz wielu katalogów, możesz użyć następującego wzorca, który ma domenę podstawową: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` .

        ![Box3 okna dialogowego Eksploratora Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    1. Z listy pobranych jednostek usługi Pobierz tę, którą chcesz zmodyfikować. Możesz również użyć klawiszy Ctrl + F, aby przeszukać aplikację ze wszystkich obiektów serviceprincipals. Możesz użyć następującego zapytania, używając **identyfikatora obiektu jednostki usługi** , który został skopiowany ze strony właściwości Azure AD, aby przejść do odpowiedniej jednostki usługi.

        `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

        ![Box4 okna dialogowego Eksploratora Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    1. Wyodrębnij Właściwość appRoles z obiektu jednostki usługi.

        ![Box5 okna dialogowego Eksploratora Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    1. Teraz musisz wygenerować nowe role dla swojej aplikacji. 

    1. Poniższy kod JSON jest przykładem obiektu appRoles. Utwórz podobny obiekt, aby dodać role, które mają być używane w aplikacji.

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
    > Nowe role można dodawać tylko po **msiam_access** dla operacji patch. Ponadto możesz dodać dowolną liczbę ról zgodnie z potrzebami organizacji. Usługa Azure AD wyśle **wartość** tych ról jako wartość żądania w odpowiedzi SAML.

    1. Wróć do Eksploratora Microsoft Graph i Zmień metodę z **Get** na **patch**. Poprawka obiektu jednostki usługi w celu uzyskania żądanych ról przez zaktualizowanie właściwości appRoles podobnej do przedstawionej powyżej w przykładzie. Kliknij przycisk **Uruchom zapytanie** , aby wykonać operację patch. Komunikat o powodzeniu potwierdza utworzenie roli dla aplikacji Amazon Web Services.

        ![Box6 okna dialogowego Eksploratora Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Po zastosowaniu poprawki jednostki usługi z większą liczbą ról można przypisywać użytkowników/grupy do odpowiednich ról. Można to zrobić, przechodząc do portalu i przechodząc do aplikacji Amazon Web Services. Kliknij kartę **Użytkownicy i grupy** w górnej części strony.

1. Zalecamy utworzenie nowych grup dla każdej roli AWS, aby można było przypisać tę konkretną rolę w tej grupie. Należy pamiętać, że jest to jedno do jednego mapowania dla jednej grupy do jednej roli. Następnie można dodać członków należących do tej grupy.

1. Po utworzeniu grup wybierz grupę i przypisz ją do aplikacji.

    ![Konfigurowanie jednego Sign-On ADD1](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Grupy zagnieżdżone nie są obsługiwane podczas przypisywania grup.

1. Aby przypisać rolę do grupy, wybierz rolę, a następnie kliknij przycisk **Przypisz** w dolnej części strony.

    ![Konfigurowanie jednego Sign-On ADD2](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Należy pamiętać, że należy odświeżyć sesję w Azure Portal, aby zobaczyć nowe role.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Ta sekcja umożliwia testowanie konfiguracji logowania jednokrotnego usługi Azure AD przy użyciu aplikacji Moje aplikacje.

Po kliknięciu kafelka Amazon Web Services (AWS) w obszarze Moje aplikacje należy uzyskać stronę aplikacji Amazon Web Services (AWS) z opcją, aby wybrać rolę.

![Test Single Sign-ON1](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Możesz również zweryfikować odpowiedź SAML, aby zobaczyć role przekazywane jako oświadczenia.

![Testuj jednokrotne — On2](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Amazon Web Services (AWS) można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

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