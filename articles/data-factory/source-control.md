---
title: Kontrola źródła
description: Dowiedz się, jak skonfigurować kontrolę źródła w Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: dcstwh
ms.author: weetok
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/02/2020
ms.openlocfilehash: 75073cd2f0dae9cac12175677427671101813a90
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355226"
---
# <a name="source-control-in-azure-data-factory"></a>Kontrola źródła w Azure Data Factory
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Domyślnie autorzy interfejsu użytkownika Azure Data Factory środowiska (UX) bezpośrednio w usłudze Data Factory. To środowisko ma następujące ograniczenia:

- Usługa Data Factory nie zawiera repozytorium do przechowywania obiektów JSON dla zmian. Jedynym sposobem zapisania zmian jest kliknięcie przycisku **Publikuj wszystko** , a wszystkie zmiany są publikowane bezpośrednio w usłudze Data Factory.
- Usługa Data Factory nie jest zoptymalizowana pod kątem współpracy i kontroli wersji.

Aby zapewnić lepszą obsługę tworzenia, Azure Data Factory umożliwia skonfigurowanie repozytorium git przy użyciu Azure Repos lub GitHub. Git to system kontroli wersji, który umożliwia łatwiejsze śledzenie zmian i współpracę. W tym artykule opisano sposób konfigurowania i pracy w repozytorium Git oraz wyróżniania najlepszych praktyk i przewodnika dotyczącego rozwiązywania problemów.

> [!NOTE]
> Azure Data Factory integracja z usługą git jest dostępna tylko dla przedsiębiorstwa usługi GitHub w chmurze Azure Government.

Aby dowiedzieć się więcej o tym, jak Azure Data Factory integruje się z usługą git, Zobacz 15-minutowy film wideo poniżej:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4GNKv]

## <a name="advantages-of-git-integration"></a>Korzyści wynikające z integracji z usługą Git

Poniżej znajduje się lista zalet integracji usługi git zapewnia środowisko tworzenia oprogramowania:

-   **Kontrola źródła:** Ponieważ obciążenia usługi Fabryka danych stają się kluczowe, warto zintegrować fabrykę z usługą git, aby korzystać z kilku korzyści z kontroli źródła, takich jak następujące:
    -   Możliwość śledzenia i inspekcji zmian.
    -   Możliwość odwracania zmian, które wprowadziły błędy.
-   **Zapisywanie częściowe:** Podczas tworzenia względem usługi Data Factory nie można zapisywać zmian jako wersji roboczej, a wszystkie publikowanie muszą przekazywać sprawdzanie poprawności fabryki danych. Niezależnie od tego, czy potoki nie zakończyły się, czy po prostu nie chcesz utracić zmian w przypadku awarii komputera, integracja z usługą git pozwala na przyrostowe zmiany zasobów usługi Data Factory niezależnie od tego, jaki stan się znajdują. Skonfigurowanie repozytorium Git umożliwia zapisanie zmian, co pozwala na publikowanie tylko w przypadku przetestowania zmian.
-   **Współpraca i kontrola:** Jeśli masz wielu członków zespołu, którzy współpracują z tą samą fabryką, możesz chcieć zezwolić członkom zespołu wspólnie ze sobą za pośrednictwem procesu przeglądu kodu. Możesz również skonfigurować fabrykę, tak aby nie każdy współautor miał równe uprawnienia. Niektórzy członkowie zespołu mogą wprowadzać zmiany tylko za pośrednictwem usługi git, a tylko niektóre osoby w zespole mogą publikować zmiany w fabryce.
-   **Lepsza**  ciągłej integracji/ciągłego wdrażania:  W przypadku wdrażania w wielu środowiskach przy użyciu [procesu ciągłego dostarczania](continuous-integration-deployment.md)integracja z usługą git sprawia, że niektóre akcje są łatwiejsze. Oto niektóre z tych akcji:
    -   Skonfiguruj potok wydania do automatycznego wyzwalania po wprowadzeniu zmian w fabryce "dev".
    -   Dostosuj właściwości w fabryce, które są dostępne jako parametry w szablonie Menedżer zasobów. Może być przydatne, aby zachować tylko wymagany zestaw właściwości jako parametry i mieć całkowicie zakodowane wszystko.
-   **Lepsza wydajność:** Średnia Fabryka z integracją narzędzia Git ładuje się 10 razy szybciej niż w przypadku tworzenia do usługi Data Factory. To ulepszenie wydajności wynika z tego, że zasoby są pobierane za pośrednictwem usługi git.

> [!NOTE]
> Tworzenie bezpośrednio przy użyciu usługi Data Factory jest wyłączone w Azure Data Factory środowisku użytkownika podczas konfigurowania repozytorium git. Zmiany wprowadzone za pośrednictwem programu PowerShell lub zestawu SDK są publikowane bezpośrednio w usłudze Data Factory i nie są wprowadzane do usługi git.

## <a name="connect-to-a-git-repository"></a>Nawiązywanie połączenia z repozytorium git

Istnieją cztery różne sposoby łączenia repozytorium git z fabryką danych w przypadku Azure Repos i GitHub. Po nawiązaniu połączenia z repozytorium git można wyświetlić konfigurację i zarządzać nią w [centrum zarządzania](author-management-hub.md) w obszarze **Konfiguracja narzędzia Git** w sekcji **Kontrola źródła** .

### <a name="configuration-method-1-home-page"></a>Konfiguracja Metoda 1: Strona główna

Na stronie głównej Azure Data Factory wybierz pozycję **Konfiguruj repozytorium kodu**.

![Konfigurowanie repozytorium kodu ze strony głównej](media/author-visually/configure-repo.png)

### <a name="configuration-method-2-authoring-canvas"></a>Konfiguracja Metoda 2: Kanwa tworzenia

Na kanwie tworzenia Azure Data Factory środowiska użytkownika wybierz **Data Factory** menu rozwijanego, a następnie wybierz pozycję **Konfiguruj repozytorium kodu**.

![Konfigurowanie ustawień repozytorium kodu na podstawie tworzenia](media/author-visually/configure-repo-2.png)

### <a name="configuration-method-3-management-hub"></a>Konfiguracja — Metoda 3: centrum zarządzania

Przejdź do centrum zarządzania w obszarze środowiska ADF. Wybierz pozycję **konfiguracja Git** w sekcji **Kontrola źródła** . Jeśli nie masz połączenia z repozytorium, kliknij pozycję **Konfiguruj repozytorium kodu**.

![Konfigurowanie ustawień repozytorium kodu z poziomu centrum zarządzania](media/author-visually/configure-repo-3.png)

### <a name="configuration-method-4-during-factory-creation"></a>Metoda konfiguracji 4: podczas tworzenia fabryki

Podczas tworzenia nowej fabryki danych w Azure Portal można skonfigurować informacje o repozytorium Git na karcie **Konfiguracja usługi git** .

> [!NOTE]
> Podczas konfigurowania usługi Git w witrynie Azure Portal ustawienia, takie jak nazwa projektu i Nazwa repozytorium, muszą być wprowadzane ręcznie zamiast części listy rozwijanej.

![Konfigurowanie ustawień repozytorium kodu w witrynie Azure Portal](media/author-visually/configure-repo-4.png)

## <a name="author-with-azure-repos-git-integration"></a>Tworzenie za pomocą integracji usługi Azure Repos z usługą Git

Tworzenie wizualne za pomocą integracji Azure Repos git obsługuje kontrolę źródła i współpracę w zakresie pracy w potokach usługi Data Factory. Fabrykę danych można skojarzyć z Azure Repos repozytorium organizacji Git na potrzeby kontroli źródła, współpracy, obsługi wersji i tak dalej. Pojedyncza organizacja usługi git Azure Repos może mieć wiele repozytoriów, ale repozytorium Azure Repos git można skojarzyć tylko z jedną fabryką danych. Jeśli nie masz Azure Repos organizacji lub repozytorium, postępuj zgodnie z [tymi instrukcjami](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) , aby utworzyć zasoby.

> [!NOTE]
> Pliki skryptów i danych można przechowywać w Azure Repos repozytorium git. Należy jednak ręcznie przekazać pliki do usługi Azure Storage. Potok fabryki danych nie przekazuje automatycznie skryptów lub plików danych przechowywanych w repozytorium Azure Repos git do usługi Azure Storage.

### <a name="azure-repos-settings"></a>Ustawienia Azure Repos

![Konfigurowanie ustawień repozytorium kodu](media/author-visually/repo-settings.png)

W okienku Konfiguracja są wyświetlane następujące Azure Repos ustawienia repozytorium kodu:

| Ustawienie | Opis | Wartość |
|:--- |:--- |:--- |
| **Typ repozytorium** | Typ repozytorium kodu Azure Repos.<br/> | Azure DevOps Git lub GitHub |
| **Azure Active Directory** | Nazwa dzierżawy usługi Azure AD. | `<your tenant name>` |
| **Organizacja Azure Repos** | Nazwa organizacji Azure Repos. Nazwę organizacji Azure Repos można znaleźć pod adresem `https://{organization name}.visualstudio.com` . Możesz [zalogować się do organizacji Azure Repos](https://www.visualstudio.com/team-services/git/) , aby uzyskać dostęp do profilu programu Visual Studio i zobaczyć repozytoria i projekty. | `<your organization name>` |
| **ProjectName** | Nazwa projektu Azure Repos. Nazwę projektu Azure Repos można znaleźć pod adresem `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **Nr repozytorium** | Nazwa repozytorium kodu Azure Repos. Projekty Azure Repos zawierają repozytoria Git umożliwiające zarządzanie kodem źródłowym w miarę rozwoju projektu. Można utworzyć nowe repozytorium lub użyć istniejącego repozytorium, które już istnieje w projekcie. | `<your Azure Repos code repository name>` |
| **Rozgałęzienie współpracy** | Gałąź współpracy Azure Repos, która jest używana do publikowania. Domyślnie jego `master` . Zmień to ustawienie, jeśli chcesz opublikować zasoby z innej gałęzi. | `<your collaboration branch name>` |
| **Folder główny** | Folder główny w gałęzi współpracy Azure Repos. | `<your root folder name>` |
| **Importuj istniejące zasoby Data Factory do repozytorium** | Określa, czy zaimportować istniejące zasoby usługi Fabryka danych z **kanwy tworzenia** środowiska użytkownika do Azure Repos repozytorium git. Zaznacz pole, aby zaimportować zasoby usługi Fabryka danych do skojarzonego repozytorium Git w formacie JSON. Ta akcja eksportuje poszczególne zasoby pojedynczo (to oznacza, że połączone usługi i zestawy danych są eksportowane do oddzielnych notacji JSON). Gdy to pole nie zostanie zaznaczone, istniejące zasoby nie zostaną zaimportowane. | Wybrane (domyślnie) |
| **Gałąź, do której ma zostać zaimportowany zasób** | Określa, w którym rozgałęzieniu są importowane zasoby usługi Fabryka danych (potoki, zestawy danych, połączone usługi itp.). Zasoby można importować do jednej z następujących gałęzi: a. Współpraca b. Utwórz nowy c. Użyj istniejącej |  |

> [!NOTE]
> Jeśli używasz przeglądarki Microsoft Edge i nie widzisz żadnych wartości na liście rozwijanej konta usługi Azure DevOps, Dodaj port https://*. VisualStudio. com do listy zaufanych witryn.

### <a name="use-a-different-azure-active-directory-tenant"></a>Korzystanie z innej dzierżawy Azure Active Directory

Repozytorium git Azure Repos może znajdować się w innej dzierżawie Azure Active Directory. Aby określić inną dzierżawę usługi Azure AD, musisz mieć uprawnienia administratora w używanej subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Zmienianie administratora subskrypcji](../cost-management-billing/manage/add-change-subscription-administrator.md#to-assign-a-user-as-an-administrator)

> [!IMPORTANT]
> Aby nawiązać połączenie z innym Azure Active Directory, zalogowany użytkownik musi być częścią usługi Active Directory. 

### <a name="use-your-personal-microsoft-account"></a>Korzystanie z konto Microsoft osobistych

Aby skorzystać z osobistego konto Microsoft na potrzeby integracji z usługą git, możesz połączyć swoje osobiste repozytorium platformy Azure z Active Directoryą swojej organizacji.

1. Dodaj osobistą konto Microsoft do Active Directory organizacji jako gość. Aby uzyskać więcej informacji, zobacz [dodawanie Azure Active Directory użytkowników współpracy B2B w Azure Portal](../active-directory/external-identities/add-users-administrator.md).

2. Zaloguj się do Azure Portal przy użyciu konto Microsoft osobistych. Następnie przejdź do Active Directory organizacji.

3. Przejdź do sekcji Azure DevOps, w której możesz teraz zobaczyć Twoje osobiste repozytorium. Wybierz repozytorium i Połącz się z Active Directory.

Po wykonaniu tych kroków konfiguracyjnych repozytorium osobiste jest dostępne po skonfigurowaniu integracji narzędzia Git w interfejsie użytkownika Data Factory.

Aby uzyskać więcej informacji na temat łączenia Azure Repos z Active Directory organizacji, zobacz [łączenie organizacji usługi Azure DevOps w celu Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Tworzenie za pomocą integracji z usługą GitHub

Tworzenie wizualne dzięki integracji z usługą GitHub obsługuje kontrolę źródła i współpracę w ramach potoków usługi Data Factory. Możesz skojarzyć fabrykę danych z repozytorium konta usługi GitHub na potrzeby kontroli źródła, współpracy i przechowywania wersji. Pojedyncze konto usługi GitHub może mieć wiele repozytoriów, ale repozytorium GitHub może być skojarzone tylko z jedną fabryką danych. Jeśli nie masz konta lub repozytorium usługi GitHub, postępuj zgodnie z [tymi instrukcjami](https://github.com/join) , aby utworzyć zasoby.

Integracja z usługą GitHub Data Factory obsługuje zarówno publiczną witrynę GitHub (czyli [https://github.com](https://github.com) ), jak i witrynę GitHub Enterprise. Można używać publicznych i prywatnych repozytoriów GitHub z Data Factory, jak długo masz uprawnienia do odczytu i zapisu do repozytorium w serwisie GitHub.

Aby skonfigurować repozytorium GitHub, musisz mieć uprawnienia administratora dla subskrypcji platformy Azure, której używasz.

### <a name="github-settings"></a>Ustawienia usługi GitHub

![Ustawienia repozytorium GitHub](media/author-visually/github-integration-image2.png)

W okienku Konfiguracja są wyświetlane następujące ustawienia repozytorium GitHub:

| **Ustawienie** | **Opis**  | **Wartość**  |
|:--- |:--- |:--- |
| **Typ repozytorium** | Typ repozytorium kodu Azure Repos. | GitHub |
| **Korzystanie z usługi GitHub Enterprise** | Zaznacz pole wyboru usługi GitHub Enterprise | niezaznaczony (domyślnie) |
| **Adres URL przedsiębiorstwa usługi GitHub** | Główny adres URL przedsiębiorstwa usługi GitHub (musi być adresem HTTPS dla lokalnego serwera usługi GitHub Enterprise). Na przykład: `https://github.mydomain.com`. Wymagane tylko wtedy, gdy wybrano korzystanie z usługi **GitHub Enterprise** | `<your GitHub enterprise url>` |                                                           
| **Konto w serwisie GitHub** | Nazwa konta usługi GitHub. Tę nazwę można znaleźć z protokołu https: \/ /GitHub.com/{account name}/{Repository Name}. Przechodzenie na stronę z prośbą o wprowadzenie poświadczeń OAuth usługi GitHub na Twoje konto w usłudze GitHub. | `<your GitHub account name>` |
| **Nazwa repozytorium**  | Nazwa repozytorium kodu usługi GitHub. Konta usługi GitHub zawierają repozytoria Git do zarządzania kodem źródłowym. Można utworzyć nowe repozytorium lub użyć istniejącego repozytorium, które już znajduje się na Twoim koncie. | `<your repository name>` |
| **Rozgałęzienie współpracy** | Gałąź współpracy GitHub, która jest używana do publikowania. Domyślnie jego główna. Zmień to ustawienie, jeśli chcesz opublikować zasoby z innej gałęzi. | `<your collaboration branch>` |
| **Folder główny** | Twój folder główny w gałęzi współpracy usługi GitHub. |`<your root folder name>` |
| **Importuj istniejące zasoby Data Factory do repozytorium** | Określa, czy zaimportować istniejące zasoby usługi Fabryka danych z kanwy tworzenia środowiska użytkownika do repozytorium GitHub. Zaznacz pole, aby zaimportować zasoby usługi Fabryka danych do skojarzonego repozytorium Git w formacie JSON. Ta akcja eksportuje poszczególne zasoby pojedynczo (to oznacza, że połączone usługi i zestawy danych są eksportowane do oddzielnych notacji JSON). Gdy to pole nie zostanie zaznaczone, istniejące zasoby nie zostaną zaimportowane. | Wybrane (domyślnie) |
| **Gałąź, do której ma zostać zaimportowany zasób** | Określa, w którym rozgałęzieniu są importowane zasoby usługi Fabryka danych (potoki, zestawy danych, połączone usługi itp.). Zasoby można importować do jednej z następujących gałęzi: a. Współpraca b. Utwórz nowy c. Użyj istniejącej |  |

### <a name="github-organizations"></a>Organizacje GitHub

Połączenie z organizacją GitHub wymaga od organizacji udzielenia uprawnienia do Azure Data Factory. Użytkownik z uprawnieniami administratora w organizacji musi wykonać poniższe kroki, aby umożliwić usłudze Data Factory nawiązywanie połączenia.

#### <a name="connecting-to-github-for-the-first-time-in-azure-data-factory"></a>Nawiązywanie połączenia z usługą GitHub po raz pierwszy w Azure Data Factory

Jeśli łączysz się z usługą GitHub z Azure Data Factory po raz pierwszy, wykonaj następujące kroki, aby nawiązać połączenie z organizacją usługi GitHub.

1. W okienku Konfiguracja usługi git wprowadź nazwę organizacji w polu konto usługi *GitHub* . Zostanie wyświetlony monit o zalogowanie się do usługi GitHub. 
1. Zaloguj się przy użyciu poświadczeń użytkownika.
1. Użytkownik zostanie poproszony o autoryzowanie Azure Data Factory jako aplikacji o nazwie *AzureDataFactory*. Na tym ekranie zostanie wyświetlona opcja udzielenia uprawnienia do automatycznego uzyskiwania dostępu do organizacji. Jeśli nie widzisz opcji udzielenia uprawnienia, poprosimy administratora o ręczne przyznanie uprawnienia za pomocą usługi GitHub.

Po wykonaniu tych kroków fabryka będzie mogła łączyć się z repozytoriami publicznymi i prywatnymi w organizacji. Jeśli nie możesz nawiązać połączenia, spróbuj wyczyścić pamięć podręczną przeglądarki i ponowić próbę.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Nawiązano już połączenie z usługą GitHub przy użyciu konta osobistego

Jeśli masz już połączenie z usługą GitHub i masz przyznane uprawnienia dostępu do konta osobistego, postępuj zgodnie z poniższymi instrukcjami, aby udzielić uprawnień do organizacji. 

1. Przejdź do witryny GitHub i Otwórz pozycję **Ustawienia**.

    ![Otwórz ustawienia usługi GitHub](media/author-visually/github-settings.png)

1. Wybierz pozycję **aplikacje**. Na karcie **autoryzowane aplikacje OAuth** powinna zostać wyświetlona wartość *AzureDataFactory*.

    ![Wybieranie aplikacji OAuth](media/author-visually/github-organization-select-application.png)

1. Wybierz aplikację i Udziel aplikacji dostępu do swojej organizacji.

    ![Udzielanie dostępu](media/author-visually/github-organization-grant.png)

Po wykonaniu tych kroków fabryka będzie mogła łączyć się z repozytoriami publicznymi i prywatnymi w organizacji. 

### <a name="known-github-limitations"></a>Znane ograniczenia dotyczące usługi GitHub

- Pliki skryptów i danych można przechowywać w repozytorium GitHub. Należy jednak ręcznie przekazać pliki do usługi Azure Storage. Potok Data Factory nie przekazuje automatycznie skryptów lub plików danych przechowywanych w repozytorium GitHub do usługi Azure Storage.

- Platforma GitHub Enterprise z wersją starszą niż 2.14.0 nie działa w przeglądarce Microsoft Edge.

- Integracja usługi GitHub z Data Factory narzędziami autorskimi programu działa tylko w ogólnie dostępnej wersji programu Data Factory.


- Z pojedynczej gałęzi GitHub można pobrać maksymalnie 1 000 jednostek dla każdego typu zasobu (na przykład potoków i zestawów danych). Jeśli ten limit zostanie osiągnięty, sugerowane jest podzielenie zasobów na osobne fabryki. To ograniczenie nie istnieje w repozytorium Git usługi Azure DevOps.

## <a name="version-control"></a>Kontrola wersji

Systemy kontroli wersji (znane także jako _Kontrola źródła_) umożliwiają deweloperom współpracę w kodzie i śledzenie zmian wprowadzonych w bazie kodu. Kontrola źródła to podstawowe narzędzie dla projektów z obsługą kilku deweloperów.

### <a name="creating-feature-branches"></a>Tworzenie gałęzi funkcji

Każde Azure Repos repozytorium git skojarzone z fabryką danych ma rozgałęzienie współpracy. ( `main` jest to domyślna gałąź współpracy). Użytkownicy mogą również tworzyć gałęzie funkcji, klikając pozycję **+ Nowa gałąź** na liście rozwijanej rozgałęzienie. Gdy zostanie wyświetlone okienko nowe rozgałęzienie, wprowadź nazwę gałęzi funkcji.

![Utwórz nową gałąź](media/author-visually/new-branch.png)

Gdy wszystko będzie gotowe do scalenia zmian z gałęzi funkcji z gałęzią współpracy, kliknij listę rozwijaną rozgałęzienie i wybierz pozycję **Utwórz żądanie ściągnięcia**. Ta akcja spowoduje przejście do Azure Repos git, gdzie można zgłaszać żądania ściągnięcia, przeprowadzać przeglądy kodu i scalać zmiany w gałęzi współpracy. (wartość `master` domyślna). Możliwe jest tylko publikowanie w usłudze Data Factory z poziomu gałęzi współpracy. 

![Utwórz nowe żądanie ściągnięcia](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Konfigurowanie ustawień publikowania

Domyślnie Fabryka danych generuje szablony Menedżer zasobów opublikowanej fabryki i zapisuje je w gałęzi o nazwie `adf_publish` . Aby skonfigurować niestandardową gałąź publikowania, Dodaj `publish_config.json` plik do folderu głównego w gałęzi współpracy. Podczas publikowania, ADF odczytuje ten plik, szuka pola `publishBranch` i zapisuje wszystkie szablony Menedżer zasobów w określonej lokalizacji. Jeśli gałąź nie istnieje, Fabryka danych utworzy ją automatycznie. Przykład tego, jak wygląda następujący plik:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Azure Data Factory może mieć tylko jedną gałąź publikowania w danym momencie. Po określeniu nowej gałęzi publikowania Data Factory nie usuwa poprzedniej gałęzi publikacji. Jeśli chcesz usunąć poprzednią gałąź publikowania, usuń ją ręcznie.

> [!NOTE]
> Data Factory odczytuje tylko `publish_config.json` plik podczas ładowania fabryki. Jeśli w portalu masz już załadowane fabryki, Odśwież przeglądarkę, aby zmiany zaczęły obowiązywać.

### <a name="publish-code-changes"></a>Publikuj zmiany kodu

Po scaleniu zmian w gałęzi współpracy (jest to `master` ustawienie domyślne) kliknij przycisk **Opublikuj** , aby ręcznie opublikować zmiany kodu w gałęzi głównej do usługi Data Factory.

![Publikuj zmiany w usłudze Data Factory](media/author-visually/publish-changes.png)

Zostanie otwarte okienko boczne, w którym można potwierdzić, że gałąź publikowania i oczekujące zmiany są poprawne. Po sprawdzeniu zmian kliknij przycisk **OK** , aby potwierdzić publikowanie.

![Potwierdź poprawną gałąź publikacji](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> Gałąź główna nie jest reprezentatywna dla zawartości wdrożonej w usłudze Data Factory. Gałąź główna *musi* być opublikowana ręcznie w usłudze Data Factory.

## <a name="best-practices-for-git-integration"></a>Najlepsze rozwiązania związane z integracją z usługą git

### <a name="permissions"></a>Uprawnienia

Zwykle nie chcesz, aby każdy członek zespołu miał uprawnienia do aktualizowania Data Factory. Zalecane są następujące ustawienia uprawnień:

*   Wszyscy członkowie zespołu powinni mieć uprawnienia do odczytu do Data Factory.
*   Tylko wybrany zestaw osób powinien zezwalać na publikowanie w Data Factory. W tym celu muszą mieć rolę **współautor Data Factory** w **grupie zasobów** zawierającej Data Factory. Aby uzyskać więcej informacji o uprawnieniach, zobacz [role i uprawnienia dla Azure Data Factory](concepts-roles-permissions.md).

Zaleca się, aby nie zezwalać na bezpośrednie ewidencjonowanie gałęzi współpracy. To ograniczenie może pomóc zapobiec wystąpieniu błędów, ponieważ każde zaewidencjonowanie przejdzie przez proces przeglądu żądania ściągnięcia opisany w temacie [Tworzenie gałęzi funkcji](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Korzystanie z haseł z Azure Key Vault

Zalecane jest używanie Azure Key Vault do przechowywania wszelkich parametrów połączenia lub haseł lub uwierzytelniania tożsamości zarządzanej dla Data Factory połączonych usług. Ze względów bezpieczeństwa Fabryka danych nie przechowuje wpisów tajnych w usłudze git. Wszelkie zmiany w połączonych usługach zawierające wpisy tajne, takie jak hasła, są natychmiast publikowane w usłudze Azure Data Factory.

Korzystanie z uwierzytelniania Key Vault lub MSI upraszcza również ciągłą integrację i wdrażanie, ponieważ nie trzeba podawać tych kluczy tajnych podczas wdrażania szablonu Menedżer zasobów.

## <a name="troubleshooting-git-integration"></a>Rozwiązywanie problemów dotyczących integracji z platformą Git

### <a name="stale-publish-branch"></a>Nieodświeżona gałąź publikowania

Jeśli gałąź publikowania nie jest zsynchronizowana z gałęzią główną i zawiera nieaktualne zasoby pomimo niedawnego publikowania, spróbuj wykonać następujące czynności:

1. Usuń bieżące repozytorium git
1. Skonfiguruj ponownie usługę git przy użyciu tych samych ustawień, ale upewnij się, że wybrano opcję **Importuj istniejące zasoby Data Factory do repozytorium** , a następnie wybierz pozycję **Nowa gałąź**
1. Utwórz żądanie ściągnięcia, aby scalić zmiany w gałęzi współpracy 

Poniżej przedstawiono przykładowe sytuacje, które mogą spowodować nieodświeżoną gałąź publikowania:
- Użytkownik ma wiele rozgałęzień. W jednej gałęzi funkcji usuwa połączoną usługę, która nie jest skojarzona z usługą AKV (AKV połączone usługi są publikowane bezpośrednio, niezależnie od tego, czy znajdują się w usłudze git, czy nie), i nigdy nie są scalone z gałęzią do współpracy.
- Użytkownik zmodyfikował fabrykę danych przy użyciu zestawu SDK lub programu PowerShell
- Użytkownik przeniósł wszystkie zasoby do nowej gałęzi i próbował przeprowadzić publikowanie po raz pierwszy. Połączone usługi należy tworzyć ręcznie podczas importowania zasobów.
- Użytkownik przekazuje ręcznie AKV połączonej usługi lub Integration Runtime JSON. Odwołują się do tego zasobu z innego zasobu, np. zestawu danych, połączonej usługi lub potoku. Połączona usługa nieAKVa utworzona za pomocą środowiska użytkownika jest publikowana natychmiast, ponieważ poświadczenia muszą być szyfrowane. W przypadku przekazania zestawu danych, który odwołuje się do połączonej usługi i próby opublikowania, środowisko użytkownika zezwoli na to, ponieważ istnieje w środowisku usługi git. Zostanie on odrzucony w czasie publikowania, ponieważ nie istnieje w usłudze Data Factory.

## <a name="switch-to-a-different-git-repository"></a>Przechodzenie do innego repozytorium git

Aby przełączyć się do innego repozytorium git, przejdź do strony Konfiguracja usługi Git w centrum zarządzania pod **kontrolą źródła**. Wybierz pozycję **Rozłącz**. 

![Ikona git](media/author-visually/remove-repository.png)

Wprowadź nazwę fabryki danych i kliknij przycisk **Potwierdź** , aby usunąć repozytorium git skojarzone z fabryką danych.

![Usuń skojarzenie z bieżącym repozytorium git](media/author-visually/remove-repository-2.png)

Po usunięciu skojarzenia z bieżącym repozytorium można skonfigurować ustawienia Git w taki sposób, aby korzystało z innego repozytorium, a następnie zaimportować istniejące zasoby Data Factory do nowego repozytorium.

> [!IMPORTANT]
> Usunięcie konfiguracji usługi git z fabryki danych nie powoduje usunięcia żadnych elementów z repozytorium. Fabryka będzie zawierać wszystkie opublikowane zasoby. Możesz kontynuować edytowanie fabryki bezpośrednio do usługi.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat monitorowania potoków i zarządzania nimi, zobacz temat [monitorowanie potoków i zarządzanie nimi programowo](monitor-programmatically.md).
* Aby wdrożyć ciągłą integrację i wdrażanie, zobacz [ciągłej integracji i dostarczania (Ci/CD) w Azure Data Factory](continuous-integration-deployment.md).
