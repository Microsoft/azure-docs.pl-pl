---
title: Kontrola źródła w programie Synapse Studio
description: Dowiedz się, jak skonfigurować kontrolę źródła w programie Azure Synapse Studio
author: liud
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 8f1b459c2644472463004c231f5827ff653d2da1
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567846"
---
# <a name="source-control-in-azure-synapse-studio"></a>Kontrola źródła w programie Azure Synapse Studio

Domyślnie autorzy Azure Synapse Studio bezpośrednio w usłudze Synapse. Jeśli potrzebujesz współpracy przy użyciu narzędzia Git do kontroli źródła, usługa Synapse Studio umożliwia skojarzenie obszaru roboczego z repozytorium Git, Azure DevOps lub GitHub. 

W tym artykule opisano sposób konfigurowania i pracy w obszarze roboczym usługi Synapse z włączonym repozytorium Git. Ponadto wyróżnimy niektóre najlepsze rozwiązania i przewodnik rozwiązywania problemów.

> [!NOTE]
> Azure Synapse Studio git nie jest dostępna w chmurze Azure Government Cloud.

## <a name="configure-git-repository-in-synapse-studio"></a>Konfigurowanie repozytorium Git w usłudze Synapse Studio 

Po uruchomieniu Synapse Studio możesz skonfigurować repozytorium git w obszarze roboczym. Obszar Synapse Studio może być skojarzony tylko z jednym repozytorium Git na raz. 

### <a name="configuration-method-1-global-bar"></a>Metoda konfiguracji 1: pasek globalny

Na pasku Synapse Studio wybierz menu rozwijane **Synapse Live,** a następnie wybierz pozycję **Skonfiguruj repozytorium kodu.**

![Konfigurowanie ustawień repozytorium kodu podczas tworzenia](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>Metoda konfiguracji 2: Zarządzanie centrum

Przejdź do centrum Zarządzanie Synapse Studio. Wybierz **pozycję Konfiguracja usługi Git** w sekcji **Kontrola** źródła. Jeśli nie masz połączonego repozytorium, kliknij pozycję **Konfiguruj.**

![Konfigurowanie ustawień repozytorium kodu z centrum zarządzania](media/configure-repo-2.png)

> [!NOTE]
> Użytkownicy z uprawnieniami współautora obszaru roboczego, właściciela lub wyższego poziomu mogą konfigurować, edytować ustawienia i rozłączać repozytorium Git w programie Azure Synapse Studio 

W obszarze roboczym Azure DevOps repozytorium GitHub lub repozytorium GitHub.

## <a name="connect-with-azure-devops-git"></a>Nawiązywanie połączenia z Azure DevOps Git 

Obszar roboczy synapse można skojarzyć z repozytorium Azure DevOps na temat kontroli źródła, współpracy, wersji i tak dalej. Jeśli nie masz repozytorium Azure DevOps, [postępuj](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) zgodnie z tymi instrukcjami, aby utworzyć zasoby repozytorium.

### <a name="azure-devops-git-repository-settings"></a>Azure DevOps repozytorium Git

Podczas nawiązywania połączenia z repozytorium git najpierw wybierz typ repozytorium jako Azure DevOps git, a następnie wybierz jedną dzierżawę usługi Azure AD z listy rozwijanej i kliknij przycisk **Kontynuuj.**

![Konfigurowanie ustawień repozytorium kodu](media/connect-with-azuredevops-repo-selected.png)

W okienku konfiguracji są Azure DevOps ustawienia usługi Git:

| Ustawienie | Opis | Wartość |
|:--- |:--- |:--- |
| **Typ repozytorium** | Typ repozytorium Azure Repos kodu.<br/> | Azure DevOps Git lub GitHub |
| **Azure Active Directory** | Nazwa dzierżawy usługi Azure AD. | `<your tenant name>` |
| **Azure DevOps konto** | Nazwa Azure Repos organizacji. Swoją nazwę organizacji Azure Repos znaleźć pod `https://{organization name}.visualstudio.com` adresem . Możesz [zalogować się do swojej organizacji Azure Repos,](https://www.visualstudio.com/team-services/git/) aby uzyskać dostęp do profilu aplikacji Visual Studio oraz wyświetlić repozytoria i projekty. | `<your organization name>` |
| **Nazwaprojektu** | Nazwa Azure Repos projektu. Nazwę projektu można Azure Repos pod adresem `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **RepositoryName** | Nazwa Azure Repos repozytorium kodu. Azure Repos projektów zawierają repozytoria Git do zarządzania kodem źródłowym w miarę rozwoju projektu. Możesz utworzyć nowe repozytorium lub użyć istniejącego repozytorium, które już istnieje w projekcie. | `<your Azure Repos code repository name>` |
| **Gałąź współpracy** | Gałąź Azure Repos współpracy, która jest używana do publikowania. Domyślnie jest to jego `master` . Zmień to ustawienie, jeśli chcesz opublikować zasoby z innej gałęzi. Możesz wybrać istniejące gałęzie lub utworzyć nowe | `<your collaboration branch name>` |
| **Folder główny** | Folder główny w gałęzi Azure Repos współpracy. | `<your root folder name>` |
| **Importowanie istniejących zasobów do repozytorium** | Określa, czy chcesz zaimportować istniejące zasoby z Synapse Studio do Azure Repos Git. Zaznacz pole wyboru, aby zaimportować zasoby obszaru roboczego (z wyjątkiem pul) do skojarzonego repozytorium Git w formacie JSON. Ta akcja eksportuje każdy zasób indywidualnie. Jeśli to pole nie jest zaznaczone, istniejące zasoby nie są importowane. | Zaznaczone (ustawienie domyślne) |
| **Importowanie zasobu do tej gałęzi** | Wybierz gałąź, do której mają być importowane zasoby (skrypt SQL, notes, definicja zadania platformy Spark, zestaw danych, przepływ danych itp.). 

Możesz również użyć linku repozytorium, aby szybko wskazać repozytorium git, z którym chcesz nawiązać połączenie. 

### <a name="use-a-different-azure-active-directory-tenant"></a>Używanie innej dzierżawy Azure Active Directory dzierżawy

Repozytorium Azure Repos Git może się różnić od Azure Active Directory dzierżawy. Aby określić inną dzierżawę usługi Azure AD, musisz mieć uprawnienia administratora w używanej subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Zmienianie administratora subskrypcji](../../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)

> [!IMPORTANT]
> Aby nawiązać Azure Active Directory, zalogowany użytkownik musi być częścią tej usługi Active Directory. 

### <a name="use-your-personal-microsoft-account"></a>Korzystanie z osobistych konto Microsoft

Aby użyć osobistego konta konto Microsoft integracji z usługą Git, możesz połączyć osobiste repozytorium platformy Azure z usługą Active Directory organizacji.

1. Dodaj osobisty konto Microsoft do usługi Active Directory organizacji jako gościa. Aby uzyskać więcej informacji, zobacz [Add Azure Active Directory B2B collaboration users in the Azure Portal](../../active-directory/external-identities/add-users-administrator.md)(Dodawanie użytkowników współpracy B2B w Azure Portal).

2. Zaloguj się do aplikacji Azure Portal przy użyciu osobistych konto Microsoft. Następnie przejdź do usługi Active Directory organizacji.

3. Przejdź do Azure DevOps, w której jest teraz dostępne twoje osobiste repo. Wybierz repo i połącz się z usługą Active Directory.

Po tych krokach konfiguracji Twoje osobiste repozytorium jest dostępne podczas konfigurowania integracji z usługą Git w Synapse Studio.

Aby uzyskać więcej informacji na Azure Repos z usługą Active Directory organizacji, zobacz Łączenie organizacji [z usługą Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="connect-with-github"></a>Nawiązywanie połączenia z repozytorium GitHub 

 Obszar roboczy można skojarzyć z repozytorium GitHub w celu kontroli źródła, współpracy i wersjonarowania. Jeśli nie masz konta lub repozytorium GitHub, postępuj zgodnie z tymi instrukcjami, [aby](https://github.com/join) utworzyć zasoby.

Integracja usługi GitHub z usługą Synapse Studio obsługuje zarówno publiczną usługę GitHub (czyli ), jak [https://github.com](https://github.com) i GitHub Enterprise. Możesz używać zarówno publicznych, jak i prywatnych repozytoriów GitHub, o ile masz uprawnienia do odczytu i zapisu w repozytorium w usłudze GitHub.

### <a name="github-settings"></a>Ustawienia usługi GitHub

Podczas nawiązywania połączenia z repozytorium Git najpierw wybierz typ repozytorium jako GitHub, a następnie podaj konto usługi GitHub lub adres URL serwera GitHub Enterprise Server, jeśli używasz programu GitHub Enterprise Server, a następnie kliknij **przycisk** Kontynuuj.

![Ustawienia repozytorium GitHub](media/connect-with-github-repo-1.png)

Okienko konfiguracji zawiera następujące ustawienia repozytorium GitHub:

| **Ustawienie** | **Opis**  | **Wartość**  |
|:--- |:--- |:--- |
| **Typ repozytorium** | Typ repozytorium Azure Repos kodu. | GitHub |
| **Korzystanie z GitHub Enterprise** | Pole wyboru, aby wybrać GitHub Enterprise | unselected (wartość domyślna) |
| **GitHub Enterprise URL** | Główny GitHub Enterprise URL (musi być adresem HTTPS dla lokalnego GitHub Enterprise serwera). Na przykład: `https://github.mydomain.com`. Wymagane tylko w **przypadku GitHub Enterprise** użyj | `<your GitHub enterprise url>` |                                                           
| **Konto usługi GitHub** | Nazwa konta usługi GitHub. Tę nazwę można znaleźć pod adresem https: \/ /github.com/{nazwa konta}/{nazwa repozytorium}. Przejście do tej strony monituje o wprowadzenie poświadczeń OAuth usługi GitHub na koncie usługi GitHub. | `<your GitHub account name>` |
| **Nazwa repozytorium**  | Nazwa repozytorium kodu GitHub. Konta usługi GitHub zawierają repozytoria Git do zarządzania kodem źródłowym. Możesz utworzyć nowe repozytorium lub użyć istniejącego repozytorium, które już istnieje na Twoim koncie. | `<your repository name>` |
| **Gałąź współpracy** | Gałąź współpracy usługi GitHub, która jest używana do publikowania. Domyślnie jest to jego wzorzec. Zmień to ustawienie, jeśli chcesz opublikować zasoby z innej gałęzi. | `<your collaboration branch>` |
| **Folder główny** | Folder główny w gałęzi współpracy usługi GitHub. |`<your root folder name>` |
| **Importowanie istniejących zasobów do repozytorium** | Określa, czy chcesz zaimportować istniejące zasoby z Synapse Studio do repozytorium Git. Zaznacz pole wyboru, aby zaimportować zasoby obszaru roboczego (z wyjątkiem pul) do skojarzonego repozytorium Git w formacie JSON. Ta akcja eksportuje każdy zasób osobno. Jeśli to pole nie jest zaznaczone, istniejące zasoby nie są importowane. | Wybrane (ustawienie domyślne) |
| **Importowanie zasobu do tej gałęzi** | Wybierz gałąź, do której mają być importowane zasoby (skrypt SQL, notes, definicja zadania platformy Spark, zestaw danych, przepływ danych itp.). 

### <a name="github-organizations"></a>Organizacje usługi GitHub

Nawiązywanie połączenia z organizacją usługi GitHub wymaga, aby organizacja udzielała uprawnień do Synapse Studio. Użytkownik z uprawnieniami ADMINISTRATORA w organizacji musi wykonać poniższe kroki.

#### <a name="connecting-to-github-for-the-first-time"></a>Nawiązywanie połączenia z serwisem GitHub po raz pierwszy

Jeśli łączysz się z serwisem GitHub z Synapse Studio po raz pierwszy, wykonaj następujące kroki, aby nawiązać połączenie z organizacją usługi GitHub.

1. W okienku konfiguracji usługi Git wprowadź nazwę organizacji w polu *Konto usługi GitHub.* Zostanie wyświetlony monit o zalogowanie się do usługi GitHub. 

1. Zaloguj się przy użyciu poświadczeń użytkownika.

1. Zostanie poproszony o autoryzowanie synapse jako aplikacji o nazwie *Azure Synapse*. Na tym ekranie zobaczysz opcję udzielenia ujmowania u usługę Synapse uprawnień dostępu do organizacji. Jeśli nie widzisz opcji udzielenia uprawnień, poproś administratora o ręczne udzielenie uprawnień za pośrednictwem usługi GitHub.

Po zakończeniu tych kroków obszar roboczy będzie mógł łączyć się zarówno z repozytoriami publicznymi, jak i prywatnymi w organizacji. Jeśli nie możesz nawiązać połączenia, spróbuj wyczyścić pamięć podręczną przeglądarki i ponów próbę.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Już nałączono połączenie z usługą GitHub przy użyciu konta osobistego

Jeśli masz już połączenie z usługą GitHub i masz uprawnienia dostępu tylko do konta osobistego, wykonaj poniższe kroki, aby udzielić uprawnień organizacji.

1. Przejdź do witryny GitHub i otwórz **pozycję Ustawienia**.

    ![Otwieranie ustawień usługi GitHub](media/github-settings.png)

1. Wybierz **pozycję Aplikacje.** Na karcie **Autoryzowane aplikacje OAuth** powinien zostać wyświetlony *Azure Synapse*.

    ![Autoryzowanie aplikacji OAuth](media/authorize-app.png)

1. Wybierz *Azure Synapse* i przyznaj dostęp do swojej organizacji.

    ![Przyznawanie uprawnień organizacji](media/grant-organization-permission.png)

Po ukończeniu tych kroków obszar roboczy będzie mógł łączyć się zarówno z repozytoriami publicznymi, jak i prywatnymi w organizacji.

## <a name="version-control"></a>Kontrola wersji

Systemy kontroli wersji (nazywane również _kontrolą źródła)_ umożliwiają deweloperom współpracę nad kodem i śledzenie zmian. Kontrola źródła to podstawowe narzędzie dla projektów dla wielu deweloperów.

### <a name="creating-feature-branches"></a>Tworzenie gałęzi funkcji

Każde repozytorium Git skojarzone z skojarzoną Synapse Studio ma gałąź współpracy. ( `main` lub `master` jest domyślną gałęzią współpracy). Użytkownicy mogą również tworzyć gałęzie funkcji, klikając **pozycję + Nowa gałąź** na liście rozwijanej gałęzi. Gdy pojawi się okienko nowej gałęzi, wprowadź nazwę gałęzi funkcji.

![Tworzenie nowej gałęzi](media/create-new-branch.png)

Gdy wszystko będzie gotowe do scalenia zmian z gałęzi funkcji z gałęzią współpracy, kliknij pozycję listy rozwijanej gałęzi i wybierz **pozycję Utwórz żądanie ściągnięcie.** Ta akcja przenosi cię do dostawcy usługi Git, gdzie możesz zgłaszać żądania ściągnięć, przeglądać kod i scalać zmiany z gałęzią współpracy. Publikowanie w usłudze Synapse można publikować tylko z gałęzi współpracy. 

![Tworzenie nowego żądania ściągnnięcia](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Konfigurowanie ustawień publikowania

Domyślnie program Synapse Studio szablony obszarów roboczych i zapisuje je w gałęzi o nazwie `workspace_publish` . Aby skonfigurować niestandardową gałąź publikowania, `publish_config.json` dodaj plik do folderu głównego w gałęzi współpracy. Podczas publikowania Synapse Studio ten plik, wyszukuje pole i zapisuje pliki szablonów obszaru `publishBranch` roboczego w określonej lokalizacji. Jeśli gałąź nie istnieje, Synapse Studio automatycznie ją utworzy. Przykładowy wygląd tego pliku znajduje się poniżej:

```json
{
    "publishBranch": "workspace_publish"
}
```

Azure Synapse Studio może mieć tylko jedną gałąź publikowania na raz. Po określeniu nowej gałęzi publikowania poprzednia gałąź publikowania nie zostałaby usunięta. Jeśli chcesz usunąć poprzednią gałąź publikowania, usuń ją ręcznie.


### <a name="publish-code-changes"></a>Publikowanie zmian kodu

Po scaleniu zmian do gałęzi  współpracy kliknij przycisk Publikuj, aby ręcznie opublikować zmiany kodu w gałęzi współpracy w usłudze Synapse.

![Publikowanie zmian](media/gitmode-publish.png)

Zostanie otwarte okienko boczne z potwierdzeniem, że gałąź publikowania i oczekujące zmiany są poprawne. Po zweryfikowaniu zmian kliknij przycisk **OK,** aby potwierdzić opublikowanie.

![Potwierdzanie poprawnej gałęzi publikowania](media/publish-change.png)

> [!IMPORTANT]
> Gałąź współpracy nie jest reprezentatywna dla tego, co jest wdrożone w usłudze. Zmiany w gałęzi współpracy *muszą zostać* opublikowane ręcznie.

## <a name="switch-to-a-different-git-repository"></a>Przełączanie do innego repozytorium Git

Aby przełączyć się do innego repozytorium Git, przejdź do strony konfiguracji usługi Git w centrum zarządzania w obszarze **Kontrola źródła**. Wybierz pozycję **Rozłącz.** 

![Ikona usługi Git](media/remove-repository.png)

Wprowadź nazwę obszaru roboczego i kliknij pozycję **Rozłącz,** aby usunąć repozytorium Git skojarzone z obszarem roboczym.

Po usunięciu skojarzenia z bieżącym repozytorium można skonfigurować ustawienia usługi Git do używania innego repozytorium, a następnie zaimportować istniejące zasoby do nowego repozytorium.

> [!IMPORTANT]
> Usunięcie konfiguracji usługi Git z obszaru roboczego nie powoduje usunięcia niczego z repozytorium. Obszar roboczy synapse będzie zawierać wszystkie opublikowane zasoby. Obszar roboczy można nadal edytować bezpośrednio w usłudze.

## <a name="best-practices-for-git-integration"></a>Najlepsze rozwiązania dotyczące integracji z usługą Git

-   **Uprawnienia**. Po połączeniu repozytorium Git z obszarem roboczym każda osoba, która ma dostęp do repozytorium git z dowolną rolą w Twoim obszarze roboczym, będzie mogła aktualizować artefakty, takie jak skrypt SQL, notes, definicja zadania spark, zestaw danych, przepływ danych i potok w trybie git. Zazwyczaj nie chcesz, aby każdy członek zespołu miał uprawnienia do aktualizowania obszaru roboczego. Przyznaj uprawnienie do repozytorium git tylko autorom artefaktów obszaru roboczego usługi Synapse. 
-   **Współpraca**. Nie zaleca się zezwalania na bezpośrednie zaewidencje do gałęzi współpracy. To ograniczenie może pomóc w zapobieganiu usterce, ponieważ każde zaewidencjonienie przechodzi przez proces przeglądu żądania ściągnnięcia opisany w tece [Tworzenie gałęzi funkcji.](source-control.md#creating-feature-branches)
-   **Tryb na żywo usługi Synapse**. Po opublikowaniu w trybie git wszystkie zmiany zostaną odzwierciedlone w trybie na żywo usługi Synapse. W trybie na żywo usługi Synapse publikowanie jest wyłączone. Możesz również wyświetlać i uruchamiać artefakty w trybie na żywo, jeśli masz przyznane odpowiednie uprawnienia. 
-   **Edytuj artefakty w programie Studio.** Program Synapse Studio to jedyne miejsce, w którym można włączyć kontrolę źródła obszaru roboczego i automatycznie synchronizować zmiany w usłudze Git. Wszelkie zmiany za pośrednictwem zestawu SDK, programu PowerShell, nie zostaną zsynchronizowane z usługą git. Zalecamy, aby zawsze edytować artefakt w programie Studio, gdy usługa git jest włączona.

## <a name="troubleshooting-git-integration"></a>Rozwiązywanie problemów z integracją z usługą Git

### <a name="access-to-git-mode"></a>Dostęp do trybu git 

Jeśli masz przyznane uprawnienie do repozytorium GitHub połączonego z obszarem roboczym, ale nie możesz uzyskać dostępu do trybu Git: 

1. Wyczyść pamięć podręczną i odśwież stronę. 

1. Zaloguj się do konta usługi GitHub.

### <a name="stale-publish-branch"></a>Nieodświeżone gałęzie publikowania

Jeśli gałąź publikowania nie jest zsynchronizowana z gałęzią współpracy i zawiera aktualne zasoby pomimo ostatniej publikacji, spróbuj wykonać następujące czynności:

1. Usuwanie bieżącego repozytorium Git

1. Skonfiguruj ponownie system Git przy użyciu tych samych ustawień, ale upewnij się, że zaznaczono pole wyboru **Importuj** istniejące zasoby do repozytorium i wybierz tę samą gałąź.  

1. Utwórz żądanie ściągnięcia, aby scalić zmiany w gałęzi współpracy 

## <a name="unsupported-features"></a>Nieobsługiwane funkcje

- Synapse Studio nie zezwala na wybiórcze wybieranie zatwierdzeń ani selektywne publikowanie zasobów. 
- Synapse Studio nie obsługuje dostosowywania komunikatu zatwierdzenia.
- Zgodnie z projektem akcja usuwania w programie Studio zostanie zatwierdzona bezpośrednio w usłudze git

## <a name="next-steps"></a>Następne kroki

* Aby zaimplementować ciągłą integrację i ciągłe wdrażanie, zobacz [Ciągła integracja i ciągłe dostarczanie (CI/CD).](continuous-integration-deployment.md)