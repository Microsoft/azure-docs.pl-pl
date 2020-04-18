---
title: Kontrola źródła
description: Dowiedz się, jak skonfigurować kontrolę źródła w usłudze Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/09/2019
ms.openlocfilehash: 3007865c15ceb03b104282c29179ec59a8196b38
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604600"
---
# <a name="source-control-in-azure-data-factory"></a>Kontrola źródła w fabryce danych platformy Azure
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Środowisko interfejsu użytkownika usługi Azure Data Factory (UX) ma dwa środowiska dostępne do tworzenia wizualnego:

- Autor bezpośrednio z usługą Data Factory
- Autor z integracją z witryną Azure Repos Git lub GitHub

> [!NOTE]
> Tylko tworzenie bezpośrednio z usługą Data Factory jest obsługiwane w chmurze azure dla instytucji rządowych.

## <a name="author-directly-with-the-data-factory-service"></a>Autor bezpośrednio z usługą Data Factory

Podczas tworzenia bezpośrednio z usługą Fabryka danych, jedynym sposobem, aby zapisać zmiany jest za pomocą przycisku **Publikuj wszystko.** Po kliknięciu wszystkie wprowadzone zmiany są publikowane bezpośrednio w usłudze Data Factory. 

![Tryb publikowania](media/author-visually/data-factory-publish.png)

Tworzenie bezpośrednio z usługą Data Factory ma następujące ograniczenia:

- Usługa Fabryka danych nie zawiera repozytorium do przechowywania jednostek JSON dla zmian.
- Usługa Data Factory nie jest zoptymalizowana pod kątem współpracy lub kontroli wersji.

> [!NOTE]
> Tworzenie bezpośrednio z usługą Fabryka danych jest wyłączona w ux usługi Azure Data Factory, gdy repozytorium Git jest skonfigurowany. Zmiany można wnosić bezpośrednio do usługi za pośrednictwem programu PowerShell lub SDK.

## <a name="author-with-azure-repos-git-integration"></a>Tworzenie za pomocą integracji usługi Azure Repos z usługą Git

Wizualne tworzenie za pomocą integracji Usługi Azure Repos Git obsługuje kontrolę źródła i współpracę do pracy nad potokami fabryki danych. Fabrykę danych można skojarzyć z repozytorium organizacji repozytorium organizacji repozytorium usługi Azure Repozytorium Git w celu kontroli źródła, współpracy, przechowywania wersji itd. Pojedyncza organizacja Git repozytorium platformy Azure może mieć wiele repozytoriów, ale repozytorium Git repozytorium repozytorium repozytorium usługi Azure Repozytorium może być skojarzone tylko z jedną fabryką danych. Jeśli nie masz organizacji lub repozytorium usługi Azure Repository, postępuj zgodnie z [tymi instrukcjami,](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) aby utworzyć zasoby.

> [!NOTE]
> Pliki skryptów i danych można przechowywać w repozytorium Git repozytorium repozytorium repozytorium usługi Azure Repo. Jednak należy ręcznie przekazać pliki do usługi Azure Storage. Potok usługi Data Factory nie przekazuje automatycznie plików skryptów ani danych przechowywanych w repozytorium Git repozytorium repozytorium usługi Azure do usługi Azure Storage.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Konfigurowanie repozytorium Git repozytorium repozytorium repozytorium platformy Azure za pomocą usługi Azure Data Factory

Repozytorium Git repozytorium repozytorium repozytorium repozytorium repozytorium usługi Azure Repo można skonfigurować za pomocą fabryki danych za pomocą dwóch metod.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Metoda konfiguracji 1: Strona główna usługi Azure Data Factory

Na stronie głównej usługi Azure Data Factory wybierz pozycję **Konfigurowanie repozytorium kodów**.

![Konfigurowanie repozytorium kodu repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metoda konfiguracji 2: Kanwa do tworzenia środowiska użytkownika
Na kanwie tworzenia środowiska użytkownika usługi Azure Data Factory UX wybierz menu rozwijane **Fabryka danych,** a następnie wybierz pozycję **Konfiguruj repozytorium kodu**.

![Konfigurowanie ustawień repozytorium kodu do tworzenia środowiska użytkownika](media/author-visually/configure-repo-2.png)

Obie metody otwierają okienko konfiguracji ustawień repozytorium.

![Konfigurowanie ustawień repozytorium kodu](media/author-visually/repo-settings.png)

W okienku konfiguracji są wyświetlane następujące ustawienia repozytorium kodu repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium

| Ustawienie | Opis | Wartość |
|:--- |:--- |:--- |
| **Typ repozytorium** | Typ repozytorium kodu repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium repozytor<br/> | Azure DevOps Git lub GitHub |
| **Azure Active Directory** | Twoja nazwa dzierżawy usługi Azure AD. | `<your tenant name>` |
| **Organizacja odchudy platformy Azure** | Nazwa organizacji usługi Azure Repos. Nazwę organizacji Usługi Azure Repos `https://{organization name}.visualstudio.com`można znaleźć pod adresem . Możesz [zalogować się do organizacji Reppos platformy Azure,](https://www.visualstudio.com/team-services/git/) aby uzyskać dostęp do profilu programu Visual Studio i wyświetlić repozytoria i projekty. | `<your organization name>` |
| **Nazwaprojektu** | Nazwa projektu usługi Azure Repos. Nazwę projektu usługi Azure Repos `https://{organization name}.visualstudio.com/{project name}`można znaleźć pod adresem . | `<your Azure Repos project name>` |
| **Nazwa repozytorium** | Nazwa repozytorium kodu repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium rep Projekty repozytorium platformy Azure zawierają repozytoria Git do zarządzania kodem źródłowym w miarę rozwoju projektu. Można utworzyć nowe repozytorium lub użyć istniejącego repozytorium, które jest już w projekcie. | `<your Azure Repos code repository name>` |
| **Oddział współpracy** | Twoja gałąź współpracy repo platformy Azure, która jest używana do publikowania. Domyślnie jest `master`to . Zmień to ustawienie w przypadku, gdy chcesz opublikować zasoby z innej gałęzi. | `<your collaboration branch name>` |
| **Folder główny** | Twój folder główny w gałęzi współpracy z repozytorium platformy Azure. | `<your root folder name>` |
| **Importowanie istniejących zasobów fabryki danych do repozytorium** | Określa, czy zasoby fabryczne istniejących danych mają być importowane z **obszaru roboczego tworzenia środowiska** użytkownika do repozytorium Git repozytorium repozytorium git repozytorium repozytorium usługi Azure Repos. Zaznacz to pole, aby zaimportować zasoby fabryki danych do skojarzonego repozytorium Git w formacie JSON. Ta akcja eksportuje każdy zasób indywidualnie (oznacza to, że połączone usługi i zestawy danych są eksportowane do oddzielnych sieci JSON). Gdy to pole nie jest zaznaczone, istniejące zasoby nie są importowane. | Zaznaczone (domyślnie) |
| **Gałąź do importowania zasobu do** | Określa, do której gałęzi są importowane zasoby fabryki danych (potoki, zestawy danych, usługi połączone itp.). Zasoby można importować do jednej z następujących gałęzi: a. Współpraca b. Tworzenie nowych c. Użyj istniejącego |  |

> [!NOTE]
> Jeśli używasz usługi Microsoft Edge i nie widzisz żadnych wartości w rozwijaniu konta usługi Azure DevOps, dodaj https://*.visualstudio.com do listy zaufanych witryn.

### <a name="use-a-different-azure-active-directory-tenant"></a>Używanie innej dzierżawy usługi Azure Active Directory

Repozytorium Git usługi Azure Repos możesz utworzyć w innej dzierżawie usługi Azure Active Directory. Aby określić inną dzierżawę usługi Azure AD, musisz mieć uprawnienia administratora w używanej subskrypcji platformy Azure.

### <a name="use-your-personal-microsoft-account"></a>Korzystanie z osobistego konta Microsoft

Aby użyć osobistego konta Microsoft do integracji z gitem, możesz połączyć osobiste repozytorium usługi Azure z usługą Active Directory w organizacji.

1. Dodaj osobiste konto Microsoft do usługi Active Directory organizacji jako gość. Aby uzyskać więcej informacji, zobacz [Dodawanie użytkowników współpracy usługi Azure Active Directory B2B w witrynie Azure portal](../active-directory/b2b/add-users-administrator.md).

2. Zaloguj się do witryny Azure portal za pomocą osobistego konta Microsoft. Następnie przełącz się do usługi Active Directory w organizacji.

3. Przejdź do sekcji Azure DevOps, gdzie teraz widzisz swoje osobiste repozytorium. Wybierz repozytorium i połącz się z usługą Active Directory.

Po wykonaniu tych kroków konfiguracji osobiste repozytorium jest dostępne po skonfigurowaniu integracji git w interfejsie użytkownika fabryki danych.

Aby uzyskać więcej informacji na temat łączenia aplikacji Reppos platformy Azure z usługą Active Directory w organizacji, zobacz [Łączenie organizacji programu Azure DevOps z usługą Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Tworzenie za pomocą integracji z usługą GitHub

Wizualne tworzenie za pomocą integracji GitHub obsługuje kontrolę źródła i współpracę do pracy nad potokami fabryki danych. Fabrykę danych można skojarzyć z repozytorium kont Usługi GitHub w celu kontroli źródła, współpracy, przechowywania wersji. Pojedyncze konto GitHub może mieć wiele repozytoriów, ale repozytorium GitHub może być skojarzone tylko z jedną fabryką danych. Jeśli nie masz konta lub repozytorium Usługi GitHub, postępuj zgodnie z [tymi instrukcjami,](https://github.com/join) aby utworzyć zasoby.

Integracja github z fabryką danych obsługuje zarówno [https://github.com](https://github.com)publiczny system GitHub (czyli) oraz program GitHub Enterprise. Można używać zarówno publicznych, jak i prywatnych repozytoriów GitHub z fabryką danych, o ile masz uprawnienia do odczytu i zapisu do repozytorium w usłudze GitHub.

Aby skonfigurować repozytorium GitHub, musisz mieć uprawnienia administratora dla subskrypcji platformy Azure, której używasz.

Aby zapoznać się z dziewięciominutowym wprowadzeniem i prezentacją tej funkcji, obejrzyj następujący film:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Konfigurowanie repozytorium Usługi GitHub za pomocą usługi Azure Data Factory

Repozytorium GitHub można skonfigurować za pomocą fabryki danych za pomocą dwóch metod.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Metoda konfiguracji 1: Strona główna usługi Azure Data Factory

Na stronie głównej usługi Azure Data Factory wybierz pozycję **Konfigurowanie repozytorium kodów**.

![Konfigurowanie repozytorium kodu repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metoda konfiguracji 2: Kanwa do tworzenia środowiska użytkownika

Na kanwie tworzenia środowiska użytkownika usługi Azure Data Factory UX wybierz menu rozwijane **Fabryka danych,** a następnie wybierz pozycję **Konfiguruj repozytorium kodu**.

![Konfigurowanie ustawień repozytorium kodu do tworzenia środowiska użytkownika](media/author-visually/configure-repo-2.png)

Obie metody otwierają okienko konfiguracji ustawień repozytorium.

![Ustawienia repozytorium GitHub](media/author-visually/github-integration-image2.png)

W okienku konfiguracji są wyświetlane następujące ustawienia repozytorium GitHub:

| **Ustawienie** | **Opis**  | **Wartość**  |
|:--- |:--- |:--- |
| **Typ repozytorium** | Typ repozytorium kodu repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium repozytorium repozytor | GitHub |
| **Korzystanie z gitHub Enterprise** | Zaznacz pole wyboru, aby wybrać gitHub Enterprise | niezaznaczone (domyślnie) |
| **GitHub Enterprise URL** | Główny adres URL usługi GitHub Enterprise (musi to być protokół HTTPS dla lokalnego serwera GitHub Enterprise). Na przykład: https://github.mydomain.com. Wymagane tylko wtedy, gdy wybrano opcję **Użyj gitHub Enterprise** | `<your GitHub enterprise url>` |                                                           
| **Konto GitHub** | Twoja nazwa konta GitHub. Tę nazwę można znaleźć na\/stronie https: /github.com/{account name}/{nazwa repozytorium}. Przejście do tej strony powoduje wyświetlenie monitu o wprowadzenie poświadczeń GitHub OAuth do konta Usługi GitHub. | `<your GitHub account name>` |
| **Nazwa repozytorium**  | Nazwa repozytorium kodu GitHub. Konta GitHub zawierają repozytoria Git do zarządzania kodem źródłowym. Możesz utworzyć nowe repozytorium lub użyć istniejącego repozytorium, które jest już na Twoim koncie. | `<your repository name>` |
| **Oddział współpracy** | Gałąź współpracy gitHub, która jest używana do publikowania. Domyślnie jest mistrzem. Zmień to ustawienie w przypadku, gdy chcesz opublikować zasoby z innej gałęzi. | `<your collaboration branch>` |
| **Folder główny** | Folder główny w gałęzi współpracy GitHub. |`<your root folder name>` |
| **Importowanie istniejących zasobów fabryki danych do repozytorium** | Określa, czy zasoby fabryczne istniejących danych mają być importowane z obszaru roboczego tworzenia środowiska użytkownika do repozytorium Usługi GitHub. Zaznacz to pole, aby zaimportować zasoby fabryki danych do skojarzonego repozytorium Git w formacie JSON. Ta akcja eksportuje każdy zasób indywidualnie (oznacza to, że połączone usługi i zestawy danych są eksportowane do oddzielnych sieci JSON). Gdy to pole nie jest zaznaczone, istniejące zasoby nie są importowane. | Zaznaczone (domyślnie) |
| **Gałąź do importowania zasobu do** | Określa, do której gałęzi są importowane zasoby fabryki danych (potoki, zestawy danych, usługi połączone itp.). Zasoby można importować do jednej z następujących gałęzi: a. Współpraca b. Tworzenie nowych c. Użyj istniejącego |  |

### <a name="known-github-limitations"></a>Znane ograniczenia GitHub

- Pliki skryptów i danych można przechowywać w repozytorium GitHub. Jednak należy ręcznie przekazać pliki do usługi Azure Storage. Potok usługi Data Factory nie przekazuje automatycznie plików skryptów ani danych przechowywanych w repozytorium Usługi GitHub do usługi Azure Storage.

- GitHub Enterprise w wersji starszej niż 2.14.0 nie działa w przeglądarce Microsoft Edge.

- Integracja gitHub z narzędziami do tworzenia wizualnych fabryki danych działa tylko w ogólnie dostępnej wersji usługi Data Factory.

- Maksymalnie 1000 jednostek na typ zasobu (takich jak potoki i zestawy danych) można pobrać z jednej gałęzi GitHub. Jeśli ten limit zostanie osiągnięty, zaleca się podzielić zasoby na oddzielne fabryki. Usługa Azure DevOps Git nie ma tego ograniczenia.

## <a name="switch-to-a-different-git-repo"></a>Przełączanie do innego repozytorium Git

Aby przełączyć się na inne repozytorium Git, kliknij ikonę **Ustawienia repozytorium Git** w prawym górnym rogu strony przeglądu fabryki danych. Jeśli nie widzisz ikony, wyczyść lokalną pamięć podręczną przeglądarki. Wybierz ikonę, aby usunąć skojarzenie z bieżącym repozytorium.

![Ikona aplikacji Git](media/author-visually/remove-repo.png)

Po wyświetleniu okienka Ustawienia repozytorium wybierz pozycję **Usuń git**. Wprowadź nazwę fabryki danych i kliknij **potwierdź,** aby usunąć repozytorium Git skojarzone z fabryką danych.

![Usuń skojarzenie z bieżącym repozytorium Git](media/author-visually/remove-repo2.png)

Po usunięciu skojarzenia z bieżącym repozytorium można skonfigurować ustawienia Git, aby używały innego repozytorium, a następnie zaimportować istniejące zasoby fabryki danych do nowego repozytorium. 

## <a name="version-control"></a>Kontrola wersji

Systemy kontroli wersji (znane również jako _kontrola źródła)_ umożliwiają deweloperom współpracę nad kodem i śledzenie zmian wprowadzonych w bazie kodu. Kontrola źródła jest niezbędnym narzędziem dla projektów wielodyskowych.

### <a name="creating-feature-branches"></a>Tworzenie gałęzi obiektów

Każde repozytorium Git repozytorium repozytorium repozytorium usługi Azure Repo, które jest skojarzone z fabryką danych, ma gałąź współpracy. (`master` jest domyślną gałęzią współpracy). Użytkownicy mogą również tworzyć gałęzie obiektów, klikając **pozycję + Nowa gałąź** w rozwijanej gałęzi. Po wyświetleniu nowego okienka gałęzi wprowadź nazwę gałęzi operacji.

![Tworzenie nowej gałęzi](media/author-visually/new-branch.png)

Gdy wszystko będzie gotowe do scalenia zmian z gałęzi obiektu z gałęzią współpracy, kliknij pozycję rozwijaną gałęzi i wybierz pozycję **Utwórz żądanie ściągnięcia**. Ta akcja prowadzi do usługi Azure Repos Git, gdzie można zgłaszać żądania ściągnięcia, do przeglądu kodu i scalać zmiany w gałęzi współpracy. (`master` jest wartością domyślną). Możesz publikować w usłudze Data Factory tylko z gałęzi współpracy. 

![Tworzenie nowego żądania ściągnięcia](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Konfigurowanie ustawień publikowania

Aby skonfigurować gałąź publikowania — czyli gałąź, `publish_config.json` w której są zapisywane szablony Menedżera zasobów — dodaj plik do folderu głównego w gałęzi współpracy. Data Factory odczytuje ten plik, wyszukuje pole `publishBranch`i tworzy nową gałąź (jeśli jeszcze nie istnieje) z podana wartością. Następnie zapisuje wszystkie szablony Menedżera zasobów w określonej lokalizacji. Przykład:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Po określeniu nowej gałęzi publikowania usługa Data Factory nie powoduje usunięcia poprzedniej gałęzi publikowania. Jeśli chcesz usunąć poprzednią gałąź publikowania, usuń ją ręcznie.

> [!NOTE]
> Fabryka danych `publish_config.json` odczytuje plik tylko wtedy, gdy ładuje fabrykę. Jeśli masz już fabrycznie załadowany w portalu, odśwież przeglądarkę, aby zmiany zaczęły obowiązywać.

### <a name="publish-code-changes"></a>Publikowanie zmian kodu

Po scaleniu zmian w gałęzi`master` współpracy (jest to ustawienie domyślne), kliknij przycisk **Publikuj,** aby ręcznie opublikować zmiany kodu w gałęzi głównej w usłudze Data Factory.

![Publikowanie zmian w usłudze Data Factory](media/author-visually/publish-changes.png)

Otworzy się okienko boczne, w którym potwierdzisz, że gałąź publikowania i oczekujące zmiany są poprawne. Po zweryfikowaniu zmian kliknij przycisk **OK,** aby potwierdzić publikację.

![Potwierdź poprawną gałąź publikowania](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> Gałąź główna nie jest reprezentatywna dla tego, co jest wdrażane w usłudze Data Factory. *Gałąź* główną musi zostać opublikowana ręcznie w usłudze Data Factory.

## <a name="advantages-of-git-integration"></a>Korzyści wynikające z integracji z usługą Git

-   **Kontrola źródła**. W miarę jak obciążenia fabryki danych stają się kluczowe, warto zintegrować fabrykę z gitem, aby wykorzystać kilka zalet kontroli źródła, takich jak:
    -   Możliwość śledzenia/inspekcji zmian.
    -   Możliwość powrotu zmian, które wprowadziły błędy.
-   **Częściowe zapisywanie**. Jak zrobić wiele zmian w fabryce, zdasz sobie sprawę, że w zwykłym trybie LIVE, nie można zapisać zmiany jako wersja robocza, ponieważ nie jesteś gotowy, lub nie chcesz stracić zmiany w przypadku awarii komputera. Dzięki integracji z Git możesz nadal zapisywać zmiany przyrostowo i publikować w fabryce tylko wtedy, gdy wszystko będzie gotowe. Git działa jako miejsce postoju dla twojej pracy, dopóki nie przetestujesz swoich zmian w swoim zadowoleniu.
-   **Współpraca i kontrola**. Jeśli masz wielu członków zespołu uczestniczących w tej samej fabryce, możesz pozwolić swoim członkom drużyny współpracować ze sobą za pośrednictwem procesu przeglądu kodu. Można również skonfigurować fabrykę w taki sposób, aby nie każdy współautor fabryki miał uprawnienia do wdrażania w fabryce. Członkowie zespołu mogą po prostu wprowadzać zmiany za pośrednictwem Gita, ale tylko niektóre osoby w zespole mogą "publikować" zmiany w fabryce.
-   **Pokazywanie różnic**. W trybie Git, można zobaczyć ładny dyferncja ładunku, który ma zostać opublikowany w fabryce. Ten diff pokazuje wszystkie zasoby/jednostki, które zostały zmodyfikowane/dodane/usunięte od czasu ostatniego opublikowania w fabryce. Na podstawie tego różnicy można kontynuować dalsze publikowanie lub wrócić i sprawdzić zmiany, a następnie wrócić później.
-   **Lepsze CI/CD**. Jeśli używasz trybu Git, możesz skonfigurować potok wersji, aby automatycznie wyzwalał, gdy tylko zostaną wprowadzone zmiany w fabryce deweloperów. Można również dostosować właściwości w fabryce, które są dostępne jako parametry w szablonie Menedżera zasobów. Może być przydatne, aby zachować tylko wymagany zestaw właściwości jako parametry i mieć wszystko inne zakodowane na twardo.
-   **Lepsza wydajność**. Średnia fabryka ładuje się dziesięć razy szybciej w trybie Git niż w zwykłym trybie LIVE, ponieważ zasoby są pobierane za pośrednictwem Git.

## <a name="best-practices-for-git-integration"></a>Najważniejsze wskazówki dotyczące integracji z Git

### <a name="permissions"></a>Uprawnienia

Zazwyczaj nie chcesz, aby każdy członek zespołu miał uprawnienia do aktualizacji fabryki. Zalecane są następujące ustawienia uprawnień:

*   Wszyscy członkowie zespołu powinni mieć uprawnienia do odczytu do fabryki danych.
*   Tylko wybrane zestaw osób powinno mieć możliwość publikowania w fabryce. Aby to zrobić, muszą mieć rolę **współautora fabryki danych** w grupie zasobów, w których znajduje się fabryka. Aby uzyskać więcej informacji na temat uprawnień, zobacz [Role i uprawnienia dla usługi Azure Data Factory](concepts-roles-permissions.md).
   
Zaleca się, aby nie zezwalać na bezpośrednie meldowanie się w gałęzi współpracy. To ograniczenie może pomóc w zapobieganiu błędom, ponieważ każde zameldowanie przejdzie przez proces przeglądu żądania ściągnięcia opisany w temacie [Tworzenie gałęzi funkcji.](source-control.md#creating-feature-branches)

### <a name="using-passwords-from-azure-key-vault"></a>Używanie haseł z usługi Azure Key Vault

Zaleca się używanie usługi Azure Key Vault do przechowywania dowolnych ciągów połączeń lub haseł dla usług połączonych z fabryką danych. Ze względów bezpieczeństwa nie przechowujemy takich tajnych informacji w usłudze Git, więc wszelkie zmiany w usługach linked są natychmiast publikowane w usłudze Azure Data Factory.

Korzystanie z usługi Key Vault ułatwia również ciągłą integrację i wdrażanie, ponieważ nie trzeba będzie podawać tych wpisów tajnych podczas wdrażania szablonów Menedżera zasobów.

## <a name="troubleshooting-git-integration"></a>Rozwiązywanie problemów z integracją gita

### <a name="stale-publish-branch"></a>Nieaktualne publikowanie gałęzi

Jeśli gałąź publikowania jest niezsynchronizowana z gałęzią główną i zawiera nieaktualne zasoby pomimo niedawnego publikowania, spróbuj wykonać następujące kroki:

1. Usuwanie bieżącego repozytorium Git
1. Ponownie skonfiguruj git z tymi samymi ustawieniami, ale upewnij się, że wybrano **opcję Importuj istniejące zasoby fabryki danych do repozytorium** i wybierz pozycję **Nowa gałąź**
1. Tworzenie żądania ściągnięcia w celu scalenia zmian w gałęzi współpracy 

Poniżej znajduje się kilka przykładów sytuacji, które mogą powodować przestarzałe publikowanie gałęzi:
- Użytkownik ma wiele gałęzi. W jednej gałęzi funkcji usunięto usługę połączoną, która nie jest skojarzona z AKV (usługi połączone nie AKV są publikowane natychmiast, niezależnie od tego, czy znajdują się w gicie, czy nie) i nigdy nie scalili gałęzi funkcji w brnach współpracy.
- Użytkownik zmodyfikował fabrykę danych przy użyciu zestawu SDK lub programu PowerShell
- Użytkownik przeniósł wszystkie zasoby do nowej gałęzi i próbował opublikować po raz pierwszy. Połączone usługi powinny być tworzone ręcznie podczas importowania zasobów.
- Użytkownik przesyła usługę połączona nie AKV lub integration runtime JSON ręcznie. Odwołują się do tego zasobu z innego zasobu, takiego jak zestaw danych, usługa połączona lub potok. Usługa połączona z usługą nieazwycną utworzona za pośrednictwem środowiska użytkownika jest publikowana natychmiast, ponieważ poświadczenia muszą być szyfrowane. Jeśli przekażesz zestaw danych odwołujący się do tej połączonej usługi i spróbujesz opublikować, środowisko użytkownika zezwoli na to, ponieważ istnieje w środowisku git. Zostanie on odrzucony w czasie publikacji, ponieważ nie istnieje w usłudze fabryki danych.

## <a name="provide-feedback"></a>Przekazywanie opinii
Wybierz **opinię,** aby skomentować funkcje lub powiadomić firmę Microsoft o problemach z narzędziem:

![Opinia](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat monitorowania potoków i zarządzania nimi, zobacz [Programowo monitorowanie potoków i zarządzanie nimi](monitor-programmatically.md).
* Aby zaimplementować ciągłą integrację i wdrażanie, zobacz [Ciągła integracja i dostarczanie (CI/CD) w usłudze Azure Data Factory](continuous-integration-deployment.md).
