---
title: 'Samouczek: integracja Azure Active Directory z usługą Amazon Web Services (AWS) w celu połączenia wielu kont | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między usługą Azure AD i Amazon Web Services (AWS) (starszy samouczek).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51be98654950ba290fa83f77eccdae4d6f549891
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81603825"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws-legacy-tutorial"></a>Samouczek: integracja Azure Active Directory z Amazon Web Services (AWS) (starszy samouczek)

W tym samouczku dowiesz się, jak zintegrować Azure Active Directory (Azure AD) z Amazon Web Services (AWS) (starszy samouczek).

Integracja usług Amazon Web Services (AWS) z usługą Azure AD daje następujące korzyści:

- Możliwość kontrolowania za pomocą usługi Azure AD kto ma dostęp do usług Amazon Web Services (AWS).
- Możesz umożliwić użytkownikom automatyczne uzyskiwanie zalogowanych do Amazon Web Services (AWS) (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Usługi Amazon Web Services (AWS) na liście wyników](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Należy pamiętać, że połączenie jednej aplikacji AWS ze wszystkimi kontami AWS nie jest rozwiązaniem zalecanym. Zamiast tego zalecamy użycie [tej](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) metody w celu skonfigurowania wielu wystąpień konta AWS na wielu wystąpieniach aplikacji AWS w usłudze Azure AD. Tego podejścia należy używać tylko w przypadku, gdy istnieje kilka AWS kont i ról, ten model nie jest skalowalny, ponieważ konta AWS i role wewnątrz tych kont rosną. Takie podejście nie korzysta z funkcji importowania ról AWS przy użyciu aprowizacji użytkowników usługi Azure AD, dlatego należy ręcznie dodać/zaktualizować/usunąć role. Inne ograniczenia dotyczące tego podejścia można znaleźć poniżej.

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
* Po skonfigurowaniu Amazon Web Services (AWS) można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Dodawanie usług Amazon Web Services (AWS) z galerii

Aby skonfigurować integrację usług Amazon Web Services (AWS) z usługą Azure AD, należy dodać usługi Amazon Web Services (AWS) z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Amazon Web Services (AWS)** w polu wyszukiwania.
1. Wybierz pozycję **Amazon Web Services (AWS)** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

1. Po dodaniu aplikacji przejdź do strony **Właściwości** i skopiuj **Identyfikator obiektu**.

    ![Usługi Amazon Web Services (AWS) na liście wyników](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą Amazon Web Services (AWS) na podstawie użytkownika testowego o nazwie "Britta Simon".

Aby logowanie jednokrotne działało, usługa Azure AD musi znać, co odpowiedni użytkownik w Amazon Web Services (AWS) należy do użytkownika w usłudze Azure AD. Innymi słowy, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Amazon Web Services (AWS).

W Amazon Web Services (AWS) Przypisz wartość **Nazwa użytkownika** w usłudze Azure AD jako wartość parametru **username** , aby określić relację łącza.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w usługach Amazon Web Services (AWS), należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w usługach Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączasz Logowanie jednokrotne w usłudze Azure AD w Azure Portal i skonfiguruj Logowanie jednokrotne w aplikacji Amazon Web Services (AWS).

**Aby skonfigurować logowanie jednokrotne usługi Azure AD w usługach Amazon Web Services (AWS), wykonaj następujące kroki:**

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji usług **Amazon Web Services (AWS)** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja języka SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure i kliknij przycisk **Zapisz**.

5. Aplikacja usług Amazon Web Services (AWS) oczekuje asercji SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty i oświadczenia użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij przycisk **Edytuj** , aby otworzyć okno dialogowe **atrybuty użytkownika & oświadczenia** .

    ![image (obraz)](common/edit-attribute.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    | Nazwa  | Atrybut źródłowy  | Przestrzeń nazw |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Rola            | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration             | „podaj wartość z zakresu od 900 sekund (15 minut) do 43200 sekund (12 godzin)” |  `https://aws.amazon.com/SAML/Attributes` |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image (obraz)](common/new-save-attribute.png)

    ![image (obraz)](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. W polu tekstowym **przestrzeń nazw** wpisz wartość przestrzeni nazw wyświetlaną dla tego wiersza.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK** .

    przykład Kliknij pozycję **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij pozycję **Pobierz** , aby pobrać **plik XML metadanych Federacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usług Amazon Web Services (AWS)

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

1. Na stronie **Create role** (Tworzenie roli) wykonaj następujące kroki:  

    ![Konfigurowanie logowania jednokrotnego — zaufanie][19]

    a. Wybierz pozycję **SAML 2.0 federation** (Federacja SAML 2.0) w obszarze **Select type of trusted entity** (Wybierz typ zaufanej jednostki).

    b. W obszarze **Wybieranie dostawcy saml 2,0**wybierz wcześniej utworzony **dostawca SAML** (na przykład: *WAAD*)

    c. Wybierz pozycję **Allow programmatic and AWS Management Console access** (Zezwalaj na dostęp programowy i za pomocą konsoli AWS Management Console).
  
    d. Kliknij przycisk **Dalej: uprawnienia**.

1. Wyszukaj **dostęp administratora** na pasku wyszukiwania i zaznacz pole wyboru **AdministratorAccess** , a następnie kliknij przycisk **Dalej: Tagi**.

    ![Wybieranie dostępu administratora](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. W sekcji **Dodaj Tagi (opcjonalne)** wykonaj następujące czynności:

    ![Wybieranie dostępu administratora](./media/aws-multi-accounts-tutorial/config2.png)

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

    ![Wybieranie dostępu administratora](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Teraz Zaloguj się do [Azure Portal](https://portal.azure.com/) i przejdź do **grup**.

1. Utwórz nowe grupy o takiej samej nazwie jak w przypadku utworzonych wcześniej ról mapy IAM i zanotuj **identyfikatory obiektów** tych nowych grup.

    ![Wybieranie dostępu administratora](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Wyloguj się z bieżącego konta AWS i zaloguj się przy użyciu innego konta, w którym chcesz skonfigurować Logowanie jednokrotne za pomocą usługi Azure AD.

1. Po utworzeniu wszystkich ról na kontach zostaną one wyświetlone na liście **role** dla tych kont.

    ![Konfiguracja ról](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

1. Musimy przechwycić wszystkie role ARN i Zaufane jednostki dla wszystkich ról na wszystkich kontach, które muszą być mapowane ręcznie przy użyciu aplikacji usługi Azure AD.

1. Kliknij role, aby skopiować wartości **ARN ról** i **zaufanych jednostek** . Te wartości są wymagane dla wszystkich ról, które należy utworzyć w usłudze Azure AD.

    ![Konfiguracja ról](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Wykonaj powyższy krok dla wszystkich ról we wszystkich kontach i Zapisz wszystkie z nich w formacie **role ARN, Zaufane jednostki** w Notatniku.

1. Otwórz [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) w innym oknie.

    a. Zaloguj się do witryny Eksploratora Microsoft Graph przy użyciu poświadczeń administratora globalnego/współadministratora dla Twojej dzierżawy.

    b. Musisz mieć wystarczające uprawnienia do tworzenia ról. Kliknij przycisk **Modyfikuj uprawnienia** , aby uzyskać wymagane uprawnienia.

    ![Eksplorator Microsoft Graph — okno dialogowe](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Wybierz następujące uprawnienia z listy (jeśli nie masz tych jeszcze) i kliknij pozycję "Modyfikuj uprawnienia" 

    ![Eksplorator Microsoft Graph — okno dialogowe](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Spowoduje to wyświetlenie żądania zalogowania się i zaakceptowanie zgody. Po zaakceptowaniu zgody użytkownik jest ponownie zalogowany do Microsoft Graph Explorer.

    e. Zmień listę rozwijaną wersji na wersję **beta**. Aby pobrać wszystkie jednostki usługi z dzierżawy, należy użyć następującego zapytania:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Jeśli używasz wielu katalogów, możesz użyć następującego wzorca, który ma domenę podstawową.`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Eksplorator Microsoft Graph — okno dialogowe](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Z listy pobranych jednostek usługi Pobierz tę, którą chcesz zmodyfikować. Możesz również użyć klawiszy Ctrl + F, aby przeszukać aplikację ze wszystkich obiektów serviceprincipals. Możesz użyć następującego zapytania, używając **identyfikatora obiektu** skopiowanego ze strony właściwości usługi Azure AD, aby uzyskać dostęp do odpowiedniej jednostki usługi.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Eksplorator Microsoft Graph — okno dialogowe](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    przykład Wyodrębnij Właściwość appRoles z obiektu jednostki usługi.

    ![Eksplorator Microsoft Graph — okno dialogowe](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

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
    > Nowe role można dodawać tylko po **msiam_access** dla operacji patch. Ponadto możesz dodać dowolną liczbę ról zgodnie z potrzebami organizacji. Usługa Azure AD wyśle **wartość** tych ról jako wartość żądania w odpowiedzi SAML.

    j. Wróć do Eksploratora Microsoft Graph i Zmień metodę z **Get** na **patch**. Poprawka obiektu jednostki usługi w celu uzyskania żądanych ról przez zaktualizowanie właściwości appRoles podobnej do przedstawionej powyżej w przykładzie. Kliknij przycisk **Uruchom zapytanie** , aby wykonać operację patch. Komunikat o powodzeniu potwierdza utworzenie roli dla aplikacji Amazon Web Services.

    ![Eksplorator Microsoft Graph — okno dialogowe](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Po zastosowaniu poprawki jednostki usługi z większą liczbą ról można przypisywać użytkowników/grupy do odpowiednich ról. Można to zrobić, przechodząc do portalu i przechodząc do aplikacji Amazon Web Services. Kliknij kartę **Użytkownicy i grupy** w górnej części strony.

1. Zalecamy utworzenie nowych grup dla każdej roli AWS, aby można było przypisać tę konkretną rolę w tej grupie. Należy pamiętać, że jest to jedno do jednego mapowania dla jednej grupy do jednej roli. Następnie można dodać członków należących do tej grupy.

1. Po utworzeniu grup wybierz grupę i przypisz ją do aplikacji.

    ![Skonfiguruj Dodawanie logowania jednokrotnego](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Grupy zagnieżdżone nie są obsługiwane podczas przypisywania grup.

1. Aby przypisać rolę do grupy, wybierz rolę, a następnie kliknij przycisk **Przypisz** w dolnej części strony.

    ![Skonfiguruj Dodawanie logowania jednokrotnego](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Należy pamiętać, że należy odświeżyć sesję w Azure Portal, aby zobaczyć nowe role.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Amazon Web Services (AWS) w panelu dostępu należy uzyskać stronę aplikacji Amazon Web Services (AWS) z opcją, aby wybrać rolę.

![Skonfiguruj Dodawanie logowania jednokrotnego](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Możesz również zweryfikować odpowiedź SAML, aby zobaczyć role przekazywane jako oświadczenia.

![Skonfiguruj Dodawanie logowania jednokrotnego](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Jak skonfigurować Inicjowanie obsługi przy użyciu interfejsów API programu MS Graph](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
* [Jak chronić Amazon Web Services (AWS) z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/protect-aws)

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