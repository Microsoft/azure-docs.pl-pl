---
title: Kontrola źródła w programie Synapse Studio
description: Dowiedz się, jak skonfigurować kontrolę źródła w usłudze Azure Synapse Studio
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: a5cb5831090987f1b620593843ddba817d8e68d4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188126"
---
# <a name="source-control-in-azure-synapse-studio"></a>Kontrola źródła w usłudze Azure Synapse Studio

Domyślnie autorzy usługi Azure Synapse Studio bezpośrednio w usłudze Synapse. Jednak to środowisko ma następujące ograniczenia:

- Synapse Studio nie obejmuje tymczasowego magazynu do przechowywania zmian. Jedynym sposobem zapisywania i udostępniania zmian jest **Publikowanie** i wszystkie zmiany są publikowane bezpośrednio w usłudze Synapse.

- Synapse Studio nie jest zoptymalizowana pod kątem współpracy i kontroli wersji.

Aby zapewnić możliwość kontroli źródła, program Synapse Studio umożliwia skojarzenie obszaru roboczego z repozytorium git, Azure DevOps lub GitHub. W tym artykule opisano sposób konfigurowania i pracy w obszarze roboczym usługi Synapse z włączonym repozytorium git. Ponadto wyróżniamy niektóre najlepsze rozwiązania i wskazówki dotyczące rozwiązywania problemów.

> [!NOTE]
> Integracja usługi Azure Synapse Studio Git nie jest dostępna w chmurze Azure Government.

## <a name="configure-git-repository-in-synapse-studio"></a>Konfigurowanie repozytorium Git w programie Synapse Studio 

Po uruchomieniu programu Synapse Studio można skonfigurować repozytorium Git w obszarze roboczym. Obszar roboczy programu Synapse Studio może być skojarzony tylko z jednym repozytorium git jednocześnie. 

### <a name="configuration-method-1-authoring-canvas"></a>Konfiguracja Metoda 1: Kanwa tworzenia

Na kanwie tworzenia Synapse Studio zaznacz menu rozwijane **Synapse Live** , a następnie wybierz pozycję **Konfiguruj repozytorium kodu**.

![Konfigurowanie ustawień repozytorium kodu na podstawie tworzenia](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>Konfiguracja Metoda 2. zarządzanie centrum

Przejdź do centrum zarządzania programu Synapse Studio. Wybierz pozycję **konfiguracja Git** w sekcji **Kontrola źródła** . Jeśli nie masz połączenia z repozytorium, kliknij przycisk **Konfiguruj**.

![Konfigurowanie ustawień repozytorium kodu z poziomu centrum zarządzania](media/configure-repo-2.png)

> [!NOTE]
> Użytkownicy otrzymani jako role współautor obszaru roboczego, właściciel lub wyższego poziomu mogą konfigurować, edytować ustawienia i rozłączać repozytorium Git w usłudze Azure Synapse Studio 

W obszarze roboczym możesz połączyć repozytorium usługi Azure DevOps lub GitHub.

## <a name="connect-with-azure-devops-git"></a>Nawiązywanie połączenia z usługą Azure DevOps git 

Możesz skojarzyć obszar roboczy Synapse z repozytorium Azure DevOps w celu kontroli źródła, współpracy, obsługi wersji i tak dalej. Jeśli nie masz repozytorium Azure DevOps, postępuj zgodnie z [tymi instrukcjami](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) , aby najpierw utworzyć zasoby repozytorium.

### <a name="azure-devops-git-repository-settings"></a>Ustawienia repozytorium git usługi Azure DevOps

Podczas nawiązywania połączenia z repozytorium git wybierz typ repozytorium jako Azure DevOps git, a następnie wybierz jedną dzierżawę usługi Azure AD z listy rozwijanej, a następnie kliknij przycisk **Kontynuuj**.

![Konfigurowanie ustawień repozytorium kodu](media/connect-with-azuredevops-repo-selected.png)

W okienku Konfiguracja są wyświetlane następujące ustawienia usługi Azure DevOps git:

| Ustawienie | Opis | Wartość |
|:--- |:--- |:--- |
| **Typ repozytorium** | Typ repozytorium kodu Azure Repos.<br/> | Azure DevOps Git lub GitHub |
| **Azure Active Directory** | Nazwa dzierżawy usługi Azure AD. | `<your tenant name>` |
| **Konto usługi Azure DevOps** | Nazwa organizacji Azure Repos. Nazwę organizacji Azure Repos można znaleźć pod adresem `https://{organization name}.visualstudio.com` . Możesz [zalogować się do organizacji Azure Repos](https://www.visualstudio.com/team-services/git/) , aby uzyskać dostęp do profilu programu Visual Studio i zobaczyć repozytoria i projekty. | `<your organization name>` |
| **ProjectName** | Nazwa projektu Azure Repos. Nazwę projektu Azure Repos można znaleźć pod adresem `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **Nr repozytorium** | Nazwa repozytorium kodu Azure Repos. Projekty Azure Repos zawierają repozytoria Git umożliwiające zarządzanie kodem źródłowym w miarę rozwoju projektu. Można utworzyć nowe repozytorium lub użyć istniejącego repozytorium, które już istnieje w projekcie. | `<your Azure Repos code repository name>` |
| **Rozgałęzienie współpracy** | Gałąź współpracy Azure Repos, która jest używana do publikowania. Domyślnie jego `master` . Zmień to ustawienie, jeśli chcesz opublikować zasoby z innej gałęzi. Możesz wybrać istniejące gałęzie lub utworzyć nowe | `<your collaboration branch name>` |
| **Folder główny** | Folder główny w gałęzi współpracy Azure Repos. | `<your root folder name>` |
| **Importuj istniejące zasoby do repozytorium** | Określa, czy zaimportować istniejące zasoby z programu Synapse Studio do repozytorium Azure Repos git. Zaznacz pole wyboru w celu zaimportowania zasobów obszaru roboczego (z wyjątkiem pul) do skojarzonego repozytorium Git w formacie JSON. Ta akcja eksportuje poszczególne zasoby pojedynczo. Gdy to pole nie zostanie zaznaczone, istniejące zasoby nie zostaną zaimportowane. | Zaznaczone (domyślnie) |
| **Importuj zasób do tej gałęzi** | Wybierz gałąź, do której zostaną zaimportowane zasoby (skrypt SQL, Notes, definicja zadania platformy Spark, zestaw danych, przepływu danych itp.). 

Możesz również użyć linku repozytorium, aby szybko wskazać repozytorium git, z którym chcesz nawiązać połączenie. 

### <a name="use-a-different-azure-active-directory-tenant"></a>Korzystanie z innej dzierżawy Azure Active Directory

Repozytorium git Azure Repos może znajdować się w innej dzierżawie Azure Active Directory. Aby określić inną dzierżawę usługi Azure AD, musisz mieć uprawnienia administratora w używanej subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Zmienianie administratora subskrypcji](https://docs.microsoft.com/azure/cost-management-billing/manage/add-change-subscription-administrator#assign-a-subscription-administrator)

> [!IMPORTANT]
> Aby nawiązać połączenie z innym Azure Active Directory, zalogowany użytkownik musi być częścią usługi Active Directory. 

### <a name="use-your-personal-microsoft-account"></a>Korzystanie z konto Microsoft osobistych

Aby skorzystać z osobistego konto Microsoft na potrzeby integracji z usługą git, możesz połączyć swoje osobiste repozytorium platformy Azure z Active Directoryą swojej organizacji.

1. Dodaj osobistą konto Microsoft do Active Directory organizacji jako gość. Aby uzyskać więcej informacji, zobacz [dodawanie Azure Active Directory użytkowników współpracy B2B w Azure Portal](https://docs.microsoft.com/azure/active-directory/external-identities/add-users-administrator).

2. Zaloguj się do Azure Portal przy użyciu konto Microsoft osobistych. Następnie przejdź do Active Directory organizacji.

3. Przejdź do sekcji Azure DevOps, w której możesz teraz zobaczyć Twoje osobiste repozytorium. Wybierz repozytorium i Połącz się z Active Directory.

Po wykonaniu tych kroków konfiguracyjnych Twoje repozytorium osobiste będzie dostępne po skonfigurowaniu integracji usługi Git w programie Synapse Studio.

Aby uzyskać więcej informacji na temat łączenia Azure Repos z Active Directory organizacji, zobacz [łączenie organizacji usługi Azure DevOps w celu Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="connect-with-github"></a>Łączenie z usługą GitHub 

 Możesz skojarzyć obszar roboczy z repozytorium GitHub na potrzeby kontroli źródła, współpracy i przechowywania wersji. Jeśli nie masz konta lub repozytorium usługi GitHub, postępuj zgodnie z [tymi instrukcjami](https://github.com/join) , aby utworzyć zasoby.

Integracja usługi GitHub z programem Synapse Studio obsługuje zarówno publiczną witrynę GitHub (czyli [https://github.com](https://github.com) ), jak i witrynę GitHub Enterprise. Można używać publicznych i prywatnych repozytoriów GitHub, o ile masz uprawnienia do odczytu i zapisu w repozytorium w usłudze GitHub.

### <a name="github-settings"></a>Ustawienia usługi GitHub

Podczas nawiązywania połączenia z repozytorium git najpierw wybierz typ repozytorium jako GitHub, a następnie podaj konto usługi GitHub lub adres URL serwera GitHub Enterprise, jeśli używasz programu GitHub Enterprise Server, a następnie kliknij przycisk **Kontynuuj**.

![Ustawienia repozytorium GitHub](media/connect-with-github-repo-1.png)

W okienku Konfiguracja są wyświetlane następujące ustawienia repozytorium GitHub:

| **Ustawienie** | **Opis**  | **Wartość**  |
|:--- |:--- |:--- |
| **Typ repozytorium** | Typ repozytorium kodu Azure Repos. | GitHub |
| **Korzystanie z usługi GitHub Enterprise** | Zaznacz pole wyboru usługi GitHub Enterprise | niezaznaczony (domyślnie) |
| **Adres URL przedsiębiorstwa usługi GitHub** | Główny adres URL przedsiębiorstwa usługi GitHub (musi być adresem HTTPS dla lokalnego serwera usługi GitHub Enterprise). Przykład: `https://github.mydomain.com`. Wymagane tylko wtedy, gdy wybrano korzystanie z usługi **GitHub Enterprise** | `<your GitHub enterprise url>` |                                                           
| **Konto usługi GitHub** | Nazwa konta usługi GitHub. Tę nazwę można znaleźć z protokołu https: \/ /GitHub.com/{account name}/{Repository Name}. Przechodzenie na stronę z prośbą o wprowadzenie poświadczeń OAuth usługi GitHub na Twoje konto w usłudze GitHub. | `<your GitHub account name>` |
| **Nazwa repozytorium**  | Nazwa repozytorium kodu usługi GitHub. Konta usługi GitHub zawierają repozytoria Git do zarządzania kodem źródłowym. Można utworzyć nowe repozytorium lub użyć istniejącego repozytorium, które już znajduje się na Twoim koncie. | `<your repository name>` |
| **Rozgałęzienie współpracy** | Gałąź współpracy GitHub, która jest używana do publikowania. Domyślnie jego główna. Zmień to ustawienie, jeśli chcesz opublikować zasoby z innej gałęzi. | `<your collaboration branch>` |
| **Folder główny** | Twój folder główny w gałęzi współpracy usługi GitHub. |`<your root folder name>` |
| **Importuj istniejące zasoby do repozytorium** | Określa, czy zaimportować istniejące zasoby z programu Synapse Studio do repozytorium git. Zaznacz pole wyboru w celu zaimportowania zasobów obszaru roboczego (z wyjątkiem pul) do skojarzonego repozytorium Git w formacie JSON. Ta akcja eksportuje poszczególne zasoby pojedynczo. Gdy to pole nie zostanie zaznaczone, istniejące zasoby nie zostaną zaimportowane. | Wybrane (domyślnie) |
| **Importuj zasób do tej gałęzi** | Wybierz gałąź, do której zostaną zaimportowane zasoby (skrypt SQL, Notes, definicja zadania platformy Spark, zestaw danych, przepływu danych itp.). 

### <a name="github-organizations"></a>Organizacje GitHub

Połączenie z organizacją GitHub wymaga od organizacji udzielenia uprawnienia do Synapse Studio. Użytkownik z uprawnieniami administratora w organizacji musi wykonać poniższe czynności.

#### <a name="connecting-to-github-for-the-first-time"></a>Nawiązywanie połączenia z usługą GitHub po raz pierwszy

Jeśli łączysz się z usługą GitHub z programu Synapse Studio po raz pierwszy, wykonaj następujące kroki, aby nawiązać połączenie z organizacją usługi GitHub.

1. W okienku Konfiguracja usługi git wprowadź nazwę organizacji w polu konto usługi *GitHub* . Zostanie wyświetlony monit o zalogowanie się do usługi GitHub. 

1. Zaloguj się przy użyciu poświadczeń użytkownika.

1. Użytkownik zostanie poproszony o autoryzowanie Synapse jako aplikacji o nazwie *Azure Synapse*. Na tym ekranie zostanie wyświetlona opcja udzielenia uprawnienia do Synapse w celu uzyskania dostępu do organizacji. Jeśli nie widzisz opcji udzielenia uprawnienia, poprosimy administratora o ręczne przyznanie uprawnienia za pomocą usługi GitHub.

Po wykonaniu tych kroków obszar roboczy będzie mógł połączyć się z repozytoriami publicznymi i prywatnymi w organizacji. Jeśli nie możesz nawiązać połączenia, spróbuj wyczyścić pamięć podręczną przeglądarki i ponowić próbę.

## <a name="version-control"></a>Kontrola wersji

Systemy kontroli wersji (znane także jako _Kontrola źródła_) umożliwiają deweloperom współpracę w kodzie i śledzenie zmian. Kontrola źródła to podstawowe narzędzie dla projektów z obsługą kilku deweloperów.

### <a name="creating-feature-branches"></a>Tworzenie gałęzi funkcji

Każde repozytorium git skojarzone z programem Synapse Studio ma rozgałęzienie współpracy. ( `main` lub `master` jest domyślną gałęzią współpracy). Użytkownicy mogą również tworzyć gałęzie funkcji, klikając pozycję **+ Nowa gałąź** na liście rozwijanej rozgałęzienie. Gdy zostanie wyświetlone okienko nowe rozgałęzienie, wprowadź nazwę gałęzi funkcji.

![Utwórz nową gałąź](media/create-new-branch.png)

Gdy wszystko będzie gotowe do scalenia zmian z gałęzi funkcji z gałęzią współpracy, kliknij listę rozwijaną rozgałęzienie i wybierz pozycję **Utwórz żądanie ściągnięcia**. Ta akcja spowoduje przejście do dostawcy usługi git, gdzie można zgłaszać żądania ściągnięcia, przeprowadzać przeglądy kodu i scalać zmiany w gałęzi współpracy. Możliwe jest tylko publikowanie w usłudze Synapse z poziomu gałęzi współpracy. 

![Utwórz nowe żądanie ściągnięcia](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Konfigurowanie ustawień publikowania

Domyślnie program Synapse Studio generuje szablony obszarów roboczych i zapisuje je w rozgałęzieniu o nazwie `workspace_publish` . Aby skonfigurować niestandardową gałąź publikowania, Dodaj `publish_config.json` plik do folderu głównego w gałęzi współpracy. Podczas publikowania program Synapse Studio odczytuje ten plik, szuka pola `publishBranch` i zapisuje pliki szablonów obszaru roboczego do określonej lokalizacji. Jeśli gałąź nie istnieje, Synapse Studio utworzy ją automatycznie. Przykład tego, jak wygląda następujący plik:

```json
{
    "publishBranch": "workspace_publish"
}
```

Usługa Azure Synapse Studio może mieć tylko jedną gałąź publikowania jednocześnie. Po określeniu nowej gałęzi publikowania Poprzednia gałąź publikacji nie zostanie usunięta. Jeśli chcesz usunąć poprzednią gałąź publikowania, usuń ją ręcznie.

### <a name="publish-code-changes"></a>Publikuj zmiany kodu

Po scaleniu zmian w gałęzi współpracy kliknij przycisk **Opublikuj** , aby ręcznie opublikować zmiany kodu w gałęzi współpracy do usługi Synapse.

![Publikowanie zmian](media/gitmode-publish.png)

Zostanie otwarte okienko boczne, w którym można potwierdzić, że gałąź publikowania i oczekujące zmiany są poprawne. Po sprawdzeniu zmian kliknij przycisk **OK** , aby potwierdzić publikowanie.

![Potwierdź poprawną gałąź publikacji](media/publish-change.png)

> [!IMPORTANT]
> Rozgałęzienie współpracy nie jest reprezentatywne dla zawartości wdrożonej w usłudze. Zmiany w gałęzi współpracy *muszą* być publikowane ręcznie.

## <a name="switch-to-a-different-git-repository"></a>Przechodzenie do innego repozytorium git

Aby przełączyć się do innego repozytorium git, przejdź do strony Konfiguracja usługi Git w centrum zarządzania pod **kontrolą źródła**. Wybierz pozycję **Rozłącz**. 

![Ikona git](media/remove-repository.png)

Wprowadź nazwę obszaru roboczego, a następnie kliknij przycisk **Rozłącz** , aby usunąć repozytorium git skojarzone z Twoim obszarem roboczym.

Po usunięciu skojarzenia z bieżącym repozytorium można skonfigurować ustawienia Git w taki sposób, aby korzystało z innego repozytorium, a następnie zaimportować istniejące zasoby do nowego repozytorium.

> [!IMPORTANT]
> Usunięcie konfiguracji usługi git z obszaru roboczego nie powoduje usunięcia żadnych elementów z repozytorium. Obszar roboczy Synapse będzie zawierać wszystkie opublikowane zasoby. Możesz kontynuować edytowanie obszaru roboczego bezpośrednio w usłudze.

## <a name="best-practices-for-git-integration"></a>Najlepsze rozwiązania związane z integracją z usługą git

-   **Uprawnienia**. Gdy repozytorium git jest połączone z obszarem roboczym, każda osoba, która ma dostęp do repozytorium git z dowolną rolą w obszarze roboczym, będzie mogła aktualizować artefakty, takie jak skrypt SQL, Notes, definicja zadania platformy Spark, zestaw danych, przepływu danych i potok w trybie git. Zwykle nie chcesz, aby każdy członek zespołu miał uprawnienia do aktualizowania obszaru roboczego. Przyznaj uprawnienia repozytorium git tylko dla autorów artefaktów obszaru roboczego Synapse. 
-   **Współpraca**. Zaleca się, aby nie zezwalać na bezpośrednie ewidencjonowanie gałęzi współpracy. To ograniczenie może pomóc zapobiec wystąpieniu błędów, ponieważ każde zaewidencjonowanie przejdzie przez proces przeglądu żądania ściągnięcia opisany w temacie [Tworzenie gałęzi funkcji](source-control.md#creating-feature-branches).
-   **Synapse tryb na żywo**. Po opublikowaniu w trybie git wszystkie zmiany zostaną odzwierciedlone w trybie Synapse na żywo. W trybie Synapse na żywo publikowanie jest wyłączone. Można też wyświetlać i uruchamiać artefakty w trybie na żywo, jeśli masz przyznane odpowiednie uprawnienia. 
-   **Edytuj artefakty w programie Studio**. Synapse Studio jest jedynym miejscem, w którym można włączyć kontrolę źródła obszaru roboczego i automatycznie synchronizować zmiany w usłudze git. Wszelkie zmiany za pomocą zestawu SDK i programu PowerShell nie będą synchronizowane z usługą git. Zalecamy, aby podczas włączania usługi git zawsze edytować artefakt w programie Studio.

## <a name="troubleshooting-git-integration"></a>Rozwiązywanie problemów z integracją narzędzia Git

### <a name="access-to-git-mode"></a>Dostęp do trybu git 

Jeśli udzielono uprawnienia do repozytorium git usługi GitHub połączonego z Twoim obszarem roboczym, ale nie możesz uzyskać dostępu do trybu git: 

1. Wyczyść pamięć podręczną i Odśwież stronę. 

1. Zaloguj się do swojego konta usługi GitHub.

### <a name="stale-publish-branch"></a>Nieodświeżona gałąź publikowania

Jeśli gałąź publikowania nie jest zsynchronizowana z gałęzią współpracy i zawiera nieaktualne zasoby pomimo niedawnego publikowania, spróbuj wykonać następujące czynności:

1. Usuń bieżące repozytorium git

1. Skonfiguruj ponownie git przy użyciu tych samych ustawień, ale upewnij się, że zaznaczono pole wyboru **Importuj istniejące zasoby do repozytorium** i wybierz tę samą gałąź.  

1. Utwórz żądanie ściągnięcia, aby scalić zmiany w gałęzi współpracy 

## <a name="unsupported-features"></a>Nieobsługiwane funkcje

- Synapse Studio nie zezwala na wybór zatwierdzeń lub selektywne Publikowanie zasobów. 
- Synapse Studio nie obsługuje dostosowywania komunikatu zatwierdzania.
- Po zaprojektowaniu Akcja usuwania w programie Studio zostanie przekazana bezpośrednio do usługi git

## <a name="next-steps"></a>Następne kroki

* Aby wdrożyć ciągłą integrację i wdrażanie, zobacz [ciągłej integracji i dostarczania (Ci/CD)](continuous-integration-deployment.md).