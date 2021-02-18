---
title: Zoptymalizuj środowisko SQL Server przy użyciu Azure Monitor | Microsoft Docs
description: Za pomocą Azure Monitor można użyć rozwiązania sprawdzania kondycji SQL w celu oceny ryzyka i kondycji środowisk w regularnych odstępach czasu.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: e0a7a5b9e19436deaed45382fe23063fa61edadf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587250"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Zoptymalizuj środowisko SQL za pomocą rozwiązania do sprawdzania kondycji SQL Server w Azure Monitor

![Symbol sprawdzania kondycji SQL](./media/sql-assessment/sql-assessment-symbol.png)

Możesz użyć rozwiązania sprawdzania kondycji SQL, aby ocenić ryzyko i kondycję środowisk serwera w regularnych odstępach czasu. Ten artykuł pomoże Ci zainstalować rozwiązanie, aby móc podejmować działania naprawcze w celu uzyskania potencjalnych problemów.

To rozwiązanie zapewnia priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera. Zalecenia są podzielone na sześć obszarów koncentracji, które pomagają szybko zrozumieć ryzyko i podejmować działania naprawcze.

Zalecenia zostały utworzone na podstawie wiedzy i doświadczenia zdobytych przez inżynierów firmy Microsoft z tysięcy wizyt klientów. Każde zalecenie zawiera wskazówki dotyczące przyczyny problemu oraz sposobu implementacji sugerowanych zmian.

Możesz wybrać obszary koncentracji, które są najważniejsze dla Twojej organizacji, i śledzić postęp w kierunku działania wolnego i zdrowego środowiska.

Po dodaniu rozwiązania i zakończeniu oceny informacje podsumowujące dotyczące obszarów koncentracji są wyświetlane na pulpicie nawigacyjnym **sprawdzania kondycji SQL** dla infrastruktury w środowisku. W poniższych sekcjach opisano, jak korzystać z informacji na pulpicie nawigacyjnym **sprawdzania kondycji SQL** , gdzie można wyświetlać i podejmować zalecane akcje dla infrastruktury SQL Server.

![obraz kafelka kontroli kondycji SQL](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![obraz pulpitu nawigacyjnego sprawdzania kondycji SQL](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Rozwiązanie sprawdzania kondycji SQL wymaga obsługiwanej wersji programu .NET Framework 4.6.2 zainstalowanej na każdym komputerze, na którym zainstalowano Microsoft Monitoring Agent (MMA).  Agent MMA jest używany przez program System Center 2016 — Operations Manager i Operations Manager 2012 R2 i Azure Monitor.  
* Rozwiązanie obsługuje SQL Server w wersji 2012, 2014, 2016, 2017 i 2019.
* Log Analytics obszar roboczy, aby dodać rozwiązanie sprawdzania kondycji SQL z witryny Azure Marketplace w Azure Portal. Aby można było zainstalować rozwiązanie, musisz być administratorem lub współautorem w subskrypcji platformy Azure.

  > [!NOTE]
  > Po dodaniu rozwiązania plik AdvisorAssessment.exe zostanie dodany do serwerów z agentami. Dane konfiguracji są odczytywane, a następnie wysyłane do Azure Monitor w chmurze w celu przetworzenia. Logika jest stosowana do odebranych danych, a usługa w chmurze rejestruje dane.
  >
  >

Aby przeprowadzić kontrolę kondycji na serwerach SQL Server, wymagają one agenta i łączności Azure Monitor przy użyciu jednej z następujących obsługiwanych metod:

1. Zainstaluj [Microsoft Monitoring Agent (MMA)](../agents/agent-windows.md) , jeśli serwer nie jest jeszcze monitorowany przez program System Center 2016 — Operations Manager lub Operations Manager 2012 R2.
2. Jeśli jest monitorowana z programem System Center 2016-Operations Manager lub Operations Manager 2012 R2, a grupa zarządzania nie jest zintegrowana z Azure Monitor, serwer może być wieloadresowy z Log Analytics do zbierania danych i przekazywania ich do usługi i nadal monitorowany przez Operations Manager.  
3. W przeciwnym razie, jeśli grupa zarządzania Operations Manager jest zintegrowana z usługą, należy dodać kontrolery domeny dla zbierania danych przez usługę zgodnie z instrukcjami w obszarze [Dodawanie komputerów zarządzanych przez agentów](../agents/om-agents.md#connecting-operations-manager-to-azure-monitor) po włączeniu rozwiązania w obszarze roboczym.  

Agent na SQL Server, który raportuje do Operations Manager grupy zarządzania, zbiera dane, przesyła dalej do przypisanego do niego serwera zarządzania, a następnie jest wysyłany bezpośrednio z serwera zarządzania do Azure Monitor.  Dane nie są zapisywane w Operations Manager bazach danych.  

Jeśli SQL Server jest monitorowane przez Operations Manager, należy skonfigurować Operations Manager konto Uruchom jako. Aby uzyskać więcej informacji [, zobacz Operations Manager kont Uruchom jako dla Azure monitor](#operations-manager-run-as-accounts-for-log-analytics) poniżej.

## <a name="sql-health-check-data-collection-details"></a>Szczegóły zbierania danych w rozwiązaniu SQL Health Check
Sprawdzenie kondycji SQL zbiera dane z następujących źródeł przy użyciu dostępnego agenta:

* Instrumentacja zarządzania Windows (WMI)
* Rejestr
* Liczniki wydajności
* SQL Server dynamiczne wyniki widoku zarządzania

Dane są zbierane na SQL Server i przekazywane do Log Analytics co siedem dni.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Konta Uruchom jako programu Operations Manager dla usługi Log Analytics
Log Analytics używa agenta Operations Manager i grupy zarządzania do zbierania i wysyłania danych do usługi Log Analytics. Log Analytics kompiluje w pakietach administracyjnych dla obciążeń w celu zapewnienia usług dodawania wartości. Każde obciążenie wymaga uprawnień specyficznych dla obciążenia do uruchamiania pakietów administracyjnych w innym kontekście zabezpieczeń, na przykład na koncie użytkownika domeny. Aby podać informacje dotyczące poświadczeń, należy skonfigurować konto Uruchom jako Operations Manager.

Poniższe informacje służą do ustawiania konta Uruchom jako Operations Manager na potrzeby sprawdzania kondycji SQL.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Ustawianie konta Uruchom jako dla rozwiązania SQL Health Check
 Jeśli używasz już pakietu administracyjnego SQL Server, należy użyć tej konfiguracji Uruchom jako.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Aby skonfigurować konto Uruchom jako dla programu SQL Server w konsoli operacje
> [!NOTE]
> Domyślnie przepływy pracy w pakiecie administracyjnym są uruchamiane w kontekście zabezpieczeń konta systemu lokalnego. Jeśli używasz Microsoft Monitoring Agent połączonego bezpośrednio z usługą, a nie bezpośrednio z grupą zarządzania Operations Manager, Pomiń kroki 1-5 poniżej i uruchom przykład T-SQL lub PowerShell, określając NT NT\SYSTEM jako nazwę użytkownika.
>
>

1. W Operations Manager Otwórz konsolę operacje, a następnie kliknij pozycję **Administracja**.
2. W obszarze **Konfiguracja Uruchom jako** kliknij pozycję **Profile**, a następnie otwórz **SQL Assessment profil Uruchom jako**.
3. Na stronie **Konta Uruchom jako** kliknij pozycję **Dodaj**.
4. Wybierz konto Uruchom jako systemu Windows zawierające poświadczenia potrzebne do SQL Server lub kliknij pozycję **Nowy** , aby je utworzyć.

   > [!NOTE]
   > Typ konta Uruchom jako musi być w systemie Windows. Konto Uruchom jako musi być również częścią lokalnej grupy administratorów na wszystkich serwerach z systemem Windows hostującym wystąpienia SQL Server.
   >
   >
5. Kliknij pozycję **Zapisz**.
6. Zmodyfikuj i wykonaj następujące przykładowe polecenie T-SQL na każdym wystąpieniu SQL Server, aby przyznać minimalnym uprawnieniam wymaganym dla konta Uruchom jako, aby przeprowadzić kontrolę kondycji. Nie trzeba jednak tego robić, jeśli konto Uruchom jako jest już częścią roli serwera sysadmin w SQL Server wystąpieniach.

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Aby skonfigurować konto Uruchom jako SQL przy użyciu programu Windows PowerShell
Otwórz okno programu PowerShell i uruchom następujący skrypt po zaktualizowaniu go wraz z informacjami:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Opis priorytetyzacji zaleceń
Każda z zaleceń otrzymuje wartość ważenia, która identyfikuje względną ważność zalecenia. Wyświetlane są tylko dziesięć najważniejszych zaleceń.

### <a name="how-weights-are-calculated"></a>Sposób obliczania wag
Wagi są wartościami zagregowanymi opartymi na trzech kluczowych czynnikach:

* *Prawdopodobieństwo* zidentyfikowania problemu spowoduje problemy. Wyższe prawdopodobieństwo jest równe większym ogólnym wynikom zalecenia.
* *Wpływ* problemu w organizacji, jeśli spowoduje to wystąpienie problemu. Wyższy wpływ jest równy większym ogólnym wynikom zalecenia.
* *Nakład pracy* wymagany do wdrożenia zalecenia. Wyższy nakład pracy odpowiada mniejszemu ogólnemu wynikowi zalecenia.

Waga dla każdego zalecenia jest wyrażona jako wartość procentowa łącznego wyniku dostępnego dla każdego obszaru koncentracji uwagi. Na przykład jeśli zalecenie w obszarze koncentracji zabezpieczenia i zgodność ma wynik 5%, wdrożenie tego zalecenia spowoduje zwiększenie ogólnego oceny zabezpieczeń i zgodności o 5%.

### <a name="focus-areas"></a>Obszary fokusu
**Bezpieczeństwo i zgodność** — w tym obszarze znajdują się zalecenia dotyczące potencjalnych zagrożeń bezpieczeństwa i naruszeń, zasad firmowych oraz wymagań dotyczących zgodności z przepisami technicznymi i prawnymi.

**Dostępność i ciągłość** działania — w tym obszarze przedstawiono zalecenia dotyczące dostępności usług, odporności infrastruktury i ochrony firmy.

**Wydajność i skalowalność** — ten obszar skupiania zawiera zalecenia ułatwiające rozwój infrastruktury IT w organizacji, upewnij się, że środowisko IT spełnia bieżące wymagania dotyczące wydajności i jest w stanie reagować na zmieniające się potrzeby związane z infrastrukturą.

**Uaktualnianie, migracja i wdrażanie** — w tym obszarze fokusu przedstawiono zalecenia ułatwiające uaktualnianie, migrowanie i wdrażanie SQL Server w istniejącej infrastrukturze.

**Operacje i monitorowanie** — ten obszar koncentracji pokazuje zalecenia ułatwiające usprawnienie operacji IT, implementowanie konserwacji zapobiegawczej i maksymalizowanie wydajności.

**Zarządzanie zmianami i konfiguracją** — ten obszar skupiania zawiera zalecenia pomagające w ochronie codziennych operacji. Upewnij się, że zmiany nie wpłyną negatywnie na infrastrukturę, ustal procedury kontroli zmian oraz do śledzenia i inspekcji konfiguracji systemu.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Czy należy dążyć do osiągnięcia wyniku 100% w każdym obszarze fokusu?
Niekoniecznie. Zalecenia są oparte na wiedzy i doświadczeniach zdobytych przez inżynierów firmy Microsoft w tysiącach wizyt klientów. Jednak żadne dwie infrastruktury serwera nie są takie same, a konkretne zalecenia mogą być bardziej lub mniej odpowiednie dla użytkownika. Na przykład niektóre zalecenia dotyczące zabezpieczeń mogą być mniej istotne, jeśli maszyny wirtualne nie są dostępne w Internecie. Niektóre zalecenia dotyczące dostępności mogą być mniej istotne w przypadku usług zapewniających zbieranie i raportowanie danych ad hoc o niskim priorytecie. Problemy, które są ważne dla dorosłych firm, mogą być mniej ważne do uruchomienia. Możesz chcieć określić, które obszary koncentracji są priorytetami, a następnie sprawdzić, jak wyniki zmieniają się wraz z upływem czasu.

Każde zalecenie zawiera wskazówki dotyczące przyczyny tego znaczenia. Należy użyć tych wskazówek, aby sprawdzić, czy wdrożenie zalecenia jest odpowiednie dla Ciebie, ze względu na charakter usług IT i potrzeby biznesowe Twojej organizacji.

## <a name="use-health-check-focus-area-recommendations"></a>Korzystanie z zaleceń dotyczących obszaru fokusu kontroli kondycji
Aby móc korzystać z rozwiązania do oceny w Azure Monitor, musisz mieć zainstalowane rozwiązanie.  Po zainstalowaniu programu można wyświetlić podsumowanie zaleceń przy użyciu kafelka Sprawdzenie kondycji SQL na stronie **przegląd** Azure Monitor w Azure Portal.

Wyświetl podsumowanie ocen zgodności dla infrastruktury, a następnie zapoznaj się z zaleceniami.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aby wyświetlić zalecenia dotyczące obszaru koncentracji uwagi i podjąć działania naprawcze
1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Monitor**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Monitor**.
3. W sekcji **szczegółowe informacje** w menu wybierz pozycję **więcej**.  
4. Na stronie **Przegląd** kliknij kafelek **Sprawdzanie kondycji SQL** .
5. Na stronie **Sprawdzanie kondycji** Przejrzyj informacje podsumowujące na jednym z bloków obszaru fokusu, a następnie kliknij jeden, aby wyświetlić zalecenia dotyczące tego obszaru.
6. Na dowolnej stronie obszaru fokusu można wyświetlić zalecenia z priorytetami wykonane dla danego środowiska. Kliknij zalecenie w obszarze powiązane **obiekty** , aby wyświetlić szczegóły dotyczące przyczyny tego zalecenia.<br><br> ![obraz przedstawiający zalecenia dotyczące sprawdzania kondycji SQL](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. W **sugerowanych akcjach** można wykonać akcje naprawcze. Gdy element został rozkierowany, późniejsze oceny będą rejestrować te zalecane akcje, a Ocena zgodności ulegnie zwiększeniu. Poprawione elementy są wyświetlane jako **obiekty zakończone**.

## <a name="ignore-recommendations"></a>Ignorowanie zaleceń
Jeśli masz zalecenia, które chcesz zignorować, możesz utworzyć plik tekstowy, który będzie używany przez Azure Monitor, aby zapobiec wyświetlaniu zaleceń w wynikach oceny.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Aby zidentyfikować rekomendacje, które zostaną zignorowane
1. W menu Azure Monitor kliknij pozycję **dzienniki**.
2. Użyj następującego zapytania, aby wyświetlić listę zaleceń, których nie powiodło się w przypadku komputerów w danym środowisku.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Oto zrzut ekranu przedstawiający zapytanie dziennika:<br><br> ![zalecenia zakończone niepowodzeniem](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Wybierz zalecenia, które chcesz zignorować. W następnej procedurze użyjesz wartości dla RecommendationId.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Aby utworzyć plik tekstowy IgnoreRecommendations.txt i używać go
1. Utwórz plik o nazwie IgnoreRecommendations.txt.
2. Wklej lub wpisz każdą RecommendationId każdego zalecenia, które Azure Monitor mają być ignorowane w osobnym wierszu, a następnie Zapisz i zamknij plik.
3. Umieść plik w następującym folderze na każdym komputerze, na którym ma Azure Monitor ignorowanie zaleceń.
   * Na komputerach z Microsoft Monitoring Agent (połączone bezpośrednio lub za pomocą Operations Manager)- *dysk_systemowy*: \Program Files\Microsoft monitoring Agent\Agent
   * Na serwerze zarządzania Operations Manager- *dysk_systemowy*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Na Operations Manager 2016 Management Server- *dysk_systemowy*: \Program Files\Microsoft System Center 2016 \ Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Aby sprawdzić, czy zalecenia są ignorowane
1. Po następnym zaplanowanym przebiegu oceny domyślnie co 7 dni określone zalecenia są oznaczone jako ignorowane i nie będą wyświetlane na pulpicie nawigacyjnym oceny.
2. Aby wyświetlić listę wszystkich ignorowanych zaleceń, można użyć poniższych zapytań wyszukiwania w dzienniku.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Jeśli zdecydujesz się później, aby zobaczyć zignorowane zalecenia, Usuń wszelkie IgnoreRecommendations.txt pliki lub Usuń z nich RecommendationIDs.

## <a name="sql-health-check-solution-faq"></a>Rozwiązanie SQL Health Check — często zadawane pytania

*Jakie testy są wykonywane przez rozwiązanie SQL Assessment?*

* Następujące zapytanie zawiera opis wszystkich aktualnie wykonanych testów:

```Kusto
SQLAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Wyniki można następnie wyeksportować do programu Excel w celu dalszej analizy.


*Jak często jest uruchamiane Sprawdzanie kondycji?*

* Sprawdzanie jest przeprowadzane co siedem dni.

*Czy istnieje sposób skonfigurowania, jak często jest przeprowadzane sprawdzanie?*

* Nie w tej chwili.

*Jeśli po dodaniu rozwiązania sprawdzania kondycji SQL zostanie odnaleziony inny serwer, zostanie on sprawdzony?*

* Tak, po jego znalezieniu jest on sprawdzany w dniu, co siedem dni.

*Jeśli serwer zostanie zlikwidowany, gdy zostanie usunięty ze sprawdzenia kondycji?*

* Jeśli serwer nie przesyła danych przez 3 tygodnie, zostanie usunięty.

*Jaka jest nazwa procesu, który wykonuje zbieranie danych?*

* AdvisorAssessment.exe

*Jak długo trwa zbieranie danych?*

* Rzeczywista zbieranie danych na serwerze trwa około 1 godziny. Może upłynąć dłużej na serwerach, które mają dużą liczbę wystąpień SQL lub baz danych.

*Jakiego typu dane są zbierane?*

* Zbierane są następujące typy danych:
  * WMI
  * Rejestr
  * Liczniki wydajności
  * Dynamiczne widoki zarządzania SQL (DMV).

*Czy istnieje sposób skonfigurowania zbierania danych?*

* Nie w tej chwili.

*Dlaczego należy skonfigurować konto Uruchom jako?*

* W przypadku SQL Server uruchamiana jest niewielka liczba zapytań SQL. Aby można było je uruchomić, należy użyć konta Uruchom jako z uprawnieniami do wyświetlania stanu serwera do bazy danych SQL.  Ponadto w celu wysyłania zapytań do usługi WMI wymagane są poświadczenia administratora lokalnego.

*Dlaczego są wyświetlane tylko 10 najważniejszych zaleceń?*

* Zamiast zapewniać wyczerpującą listę zadań, zalecamy najpierw skupić się na rozwiązaniu zaleceń z priorytetami. Po wprowadzeniu dodatkowych rekomendacji staną się dostępne. Jeśli wolisz wyświetlić listę Szczegółowa, możesz wyświetlić wszystkie zalecenia, korzystając z przeszukiwania dzienników Log Analytics.

*Czy istnieje sposób na ignorowanie rekomendacji?*

* Tak, zobacz sekcję [Ignorowanie rekomendacji](#ignore-recommendations) powyżej.

## <a name="next-steps"></a>Następne kroki
* [Rejestruj zapytania](../logs/log-query-overview.md) , aby dowiedzieć się, jak analizować szczegółowe dane i zalecenia dotyczące kontroli kondycji SQL.

