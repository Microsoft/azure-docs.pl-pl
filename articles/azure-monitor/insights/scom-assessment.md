---
title: Oceń System Center Operations Manager z Azure Monitor
description: Możesz użyć rozwiązania System Center Operations Manager Health Check, aby ocenić ryzyko i kondycję środowisk w regularnych odstępach czasu.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2018
ms.openlocfilehash: 685bf35bfb6d6e85d70bd1f1b446370d66a320f2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573399"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optymalizowanie środowiska za pomocą rozwiązania System Center Operations Manager Health Check (wersja zapoznawcza)

![Symbol System Center Operations Manager Health Check](./media/scom-assessment/scom-assessment-symbol.png)

Możesz użyć rozwiązania System Center Operations Manager Health Check do oceny ryzyka i kondycji grupy zarządzania System Center Operations Manager w regularnych odstępach czasu. Ten artykuł ułatwia Instalowanie, Konfigurowanie i używanie rozwiązania, dzięki czemu można podejmować działania naprawcze w celu uzyskania potencjalnych problemów.

To rozwiązanie zapewnia priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera. Zalecenia są podzielone na cztery obszary koncentracji, które pomagają szybko zrozumieć ryzyko i podejmować działania naprawcze.

Zalecenia zostały utworzone na podstawie wiedzy i doświadczenia zdobytych przez inżynierów firmy Microsoft z tysięcy wizyt klientów. Każde zalecenie zawiera wskazówki dotyczące przyczyny problemu oraz sposobu implementacji sugerowanych zmian.

Możesz wybrać obszary koncentracji, które są najważniejsze dla Twojej organizacji, i śledzić postęp w kierunku działania wolnego i zdrowego środowiska.

Po dodaniu rozwiązania i wykonaniu oceny informacje podsumowujące dotyczące obszarów koncentracji są wyświetlane na pulpicie nawigacyjnym **System Center Operations Manager Health Check** dla infrastruktury. W poniższych sekcjach opisano sposób użycia informacji na pulpicie nawigacyjnym **System Center Operations Manager Health Check** , w którym można wyświetlać i podejmować zalecane akcje dla środowiska Operations Manager.

![Kafelek rozwiązania System Center Operations Manager](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Pulpit nawigacyjny System Center Operations Manager Health Check](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Instalowanie i konfigurowanie rozwiązania

Rozwiązanie współpracuje z programem Microsoft System Center 2012 Operations Manager Service Pack 1, Microsoft System Center 2012 R2 Operations Manager, Microsoft System Center 2016 Operations Manager, Microsoft System Center 2016 Operations Manager i Microsoft System Center Operations Manager 1807. Na każdym serwerze zarządzania musi być zainstalowana obsługiwana wersja programu .NET Framework 4.6.2.

Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie.

- Aby móc korzystać z rozwiązania sprawdzania kondycji w Log Analytics, musisz mieć zainstalowane rozwiązanie. Zainstaluj rozwiązanie z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

- Po dodaniu rozwiązania do obszaru roboczego na kafelku **System Center Operations Manager Health Check** na pulpicie nawigacyjnym zostanie wyświetlony komunikat o konieczności dodatkowej konfiguracji. Kliknij kafelek i postępuj zgodnie z krokami konfiguracji wymienionymi na stronie

  ![System Center Operations Manager kafelka pulpitu nawigacyjnego](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Konfigurację System Center Operations Manager można wykonać za pomocą skryptu, wykonując kroki opisane na stronie Konfiguracja rozwiązania w Log Analytics.

 Aby skonfigurować ocenę za pomocą konsoli operacje Operations Manager, wykonaj poniższe kroki w następującej kolejności:
1. [Ustaw konto Uruchom jako dla System Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
2. Konfigurowanie reguły System Center Operations Manager Health Check

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>Szczegóły zbierania danych w rozwiązaniu System Center Operations Manager Health Check

Rozwiązanie System Center Operations Manager Health Check zbiera dane z następujących źródeł:

* Rejestr
* Instrumentacja zarządzania Windows (WMI)
* Dziennik zdarzeń
* Dane pliku
* Bezpośrednio z Operations Manager przy użyciu programu PowerShell i zapytań SQL, z serwera zarządzania, który został określony.  

Dane są zbierane na serwerze zarządzania i przekazywane do Log Analytics co siedem dni.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Konta Uruchom jako programu Operations Manager dla usługi Log Analytics

Log Analytics kompilacje w pakietach administracyjnych dla obciążeń w celu zapewnienia usług dodawania wartości. Każde obciążenie wymaga uprawnień specyficznych dla obciążenia do uruchamiania pakietów administracyjnych w innym kontekście zabezpieczeń, na przykład na koncie użytkownika domeny. Skonfiguruj Operations Manager konto Uruchom jako z poświadczeniami uprzywilejowanymi. Aby uzyskać dodatkowe informacje, zobacz [jak utworzyć konto Uruchom jako](/previous-versions/system-center/system-center-2012-R2/hh321655(v=sc.12)) w dokumentacji Operations Manager.

Poniższe informacje służą do ustawiania Operations Manager konta Uruchom jako dla System Center Operations Manager Health Check.

### <a name="set-the-run-as-account"></a>Ustawianie konta Uruchom jako

Przed kontynuowaniem konto Uruchom jako musi spełniać następujące wymagania:

* Konto użytkownika domeny, które jest członkiem lokalnej grupy administratorów na wszystkich serwerach obsługujących serwer zarządzania rolami Operations Manager, SQL Server hostowanie operacyjnych, magazynu danych i bazy danych usług ACS, raportowania, konsoli sieci Web i serwera bramy.
* Rola administratora programu Operations Manager dla ocenianej grupy zarządzania
* Jeśli konto nie ma uprawnień administratora systemu SQL, wykonaj [skrypt](#sql-script-to-grant-granular-permissions-to-the-run-as-account) , aby udzielić szczegółowych uprawnień do konta w każdym wystąpieniu SQL Server, w którym znajduje się jedna lub wszystkie bazy danych Operations Manager.

1. W konsoli Operations Manager wybierz przycisk nawigacji **Administracja** .
2. W obszarze **Konfiguracja Uruchom jako** kliknij pozycję **konta**.
3. W kreatorze **tworzenia konta Uruchom jako** na stronie **wprowadzenie** kliknij przycisk **dalej**.
4. Na stronie **Właściwości ogólne** wybierz pozycję **Windows** na liście **Typ konta Uruchom jako** .
5. Wpisz nazwę wyświetlaną w polu tekstowym **Nazwa wyświetlana** i opcjonalnie wpisz opis w polu **Opis** , a następnie kliknij przycisk **dalej**.
6. Na stronie **zabezpieczenia dystrybucji** **Wybierz pozycję bezpieczniejsze.**
7. Kliknij pozycję **Utwórz**.  

Teraz, gdy tworzone jest konto Uruchom jako, musi ono być docelowe serwery zarządzania w grupie zarządzania i skojarzone ze wstępnie zdefiniowanym profilem Uruchom jako, aby przepływy pracy były uruchamiane przy użyciu poświadczeń.  

1. W obszarze **Konfiguracja Uruchom jako**, **konta**, w okienku wyników kliknij dwukrotnie utworzone wcześniej konto.
2. Na karcie **dystrybucja** kliknij pozycję **Dodaj** w polu **wybrane komputery** i Dodaj serwer zarządzania, do którego ma zostać dystrybuowane konto.  Dwa razy kliknij przycisk **OK** , aby zapisać zmiany.
3. W obszarze **Konfiguracja Uruchom jako** kliknij pozycję **Profile**.
4. Wyszukaj *profil oceny programu SCOM*.
5. Nazwa profilu powinna być następująca: *Microsoft System Center Operations Manager Health Check profil Uruchom jako*.
6. Kliknij prawym przyciskiem myszy i zaktualizuj jej właściwości, a następnie Dodaj ostatnio utworzone konto Uruchom jako utworzone wcześniej.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Skrypt SQL umożliwiający przyznanie szczegółowych uprawnień do konta Uruchom jako

Wykonaj następujący skrypt SQL, aby udzielić wymaganych uprawnień do konta Uruchom jako w wystąpieniu SQL Server używanym przez Operations Manager hostowanie operacyjnej, magazynu danych i bazy danych ACS.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>Konfigurowanie reguły kontroli kondycji

Pakiet administracyjny rozwiązania System Center Operations Manager Health Check zawiera regułę o nazwie *Uruchom test kondycji programu Microsoft System Center Operations Manager*. Ta reguła jest odpowiedzialna za uruchamianie kontroli kondycji. Aby włączyć regułę i skonfigurować częstotliwość, Użyj poniższych procedur.

Domyślnie reguła sprawdzania kondycji programu Microsoft System Center Operations Manager jest wyłączona. Aby uruchomić kontrolę kondycji, należy włączyć regułę na serwerze zarządzania programu. Skorzystaj z poniższych instrukcji.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Włączanie reguły dla określonego serwera zarządzania

1. W obszarze roboczym **Tworzenie** w konsoli operacje Operations Manager w okienku **reguły** Wyszukaj regułę *Sprawdzanie kondycji uruchamiania w programie Microsoft System Center Operations Manager* .
2. W wynikach wyszukiwania wybierz ten, który zawiera *Typ tekstu: serwer zarządzania*.
3. Kliknij prawym przyciskiem myszy regułę, a następnie kliknij pozycję **zastąpienia**  >  **dla określonego obiektu klasy: serwer zarządzania**.
4.  Na liście dostępne serwery zarządzania wybierz serwer zarządzania, na którym ma zostać uruchomiona reguła.  Powinien to być ten sam serwer zarządzania, który został wcześniej skonfigurowany do skojarzenia konta Uruchom jako z.
5.  Upewnij się, że dla wartości parametru **Enabled** została zmieniona wartość override na **true** .<br><br> ![parametr override](./media/scom-assessment/rule.png)

    Mimo że w tym oknie należy skonfigurować częstotliwość uruchamiania przy użyciu następnej procedury.

#### <a name="configure-the-run-frequency"></a>Konfigurowanie częstotliwości uruchamiania

Ocena jest konfigurowana do uruchamiania co 10 080 minut (lub siedem dni). Można przesłonić wartość do wartości minimalnej wynoszącej 1440 minut (lub jeden dzień). Wartość reprezentuje minimalny odstęp czasu wymagany między kolejnymi przebiegami oceny. Aby przesłonić interwał, wykonaj poniższe czynności.

1. W obszarze roboczym **Tworzenie** w konsoli programu Operations Manager Wyszukaj regułę *Sprawdzanie kondycji uruchamiania reguły w programie Microsoft System Center Operations Manager* w sekcji **reguły** .
2. W wynikach wyszukiwania wybierz ten, który zawiera *Typ tekstu: serwer zarządzania*.
3. Kliknij prawym przyciskiem myszy regułę, a następnie kliknij pozycję **Zastąp regułę**  >  **dla wszystkich obiektów klasy: serwer zarządzania**.
4. Zmień wartość parametru **Interval** na żądaną wartość interwału. W poniższym przykładzie wartość jest ustawiona na 1440 minut (jeden dzień).<br><br> ![parametr interwału](./media/scom-assessment/interval.png)<br>  

    Jeśli wartość jest równa mniej niż 1440 minut, reguła jest uruchamiana w przedziale jeden dzień. W tym przykładzie reguła ignoruje wartość interwału i jest uruchamiana z częstotliwością jeden dzień.


## <a name="understanding-how-recommendations-are-prioritized"></a>Opis priorytetyzacji zaleceń

Każda z zaleceń otrzymuje wartość ważenia, która identyfikuje względną ważność zalecenia. Wyświetlane są tylko 10 najważniejszych zaleceń.

### <a name="how-weights-are-calculated"></a>Sposób obliczania wag

Wagi są wartościami zagregowanymi opartymi na trzech kluczowych czynnikach:

- *Prawdopodobieństwo* zidentyfikowania problemu spowoduje problemy. Wyższe prawdopodobieństwo jest równe większym ogólnym wynikom zalecenia.
- *Wpływ* problemu w organizacji, jeśli spowoduje to wystąpienie problemu. Wyższy wpływ jest równy większym ogólnym wynikom zalecenia.
- *Nakład pracy* wymagany do wdrożenia zalecenia. Wyższy nakład pracy odpowiada mniejszemu ogólnemu wynikowi zalecenia.

Waga dla każdego zalecenia jest wyrażona jako wartość procentowa łącznego wyniku dostępnego dla każdego obszaru koncentracji uwagi. Na przykład jeśli zalecenie dotyczące obszaru koncentracji dostępności i ciągłości działania firmy ma wynik 5%, implementacja tego zalecenia spowoduje zwiększenie ogólnej dostępności i oceny ciągłości działania firmy o 5%.

### <a name="focus-areas"></a>Obszary fokusu

**Dostępność i ciągłość** działania — w tym obszarze przedstawiono zalecenia dotyczące dostępności usług, odporności infrastruktury i ochrony firmy.

**Wydajność i skalowalność** — ten obszar skupiania zawiera zalecenia ułatwiające rozwój infrastruktury IT w organizacji, upewnij się, że środowisko IT spełnia bieżące wymagania dotyczące wydajności i jest w stanie reagować na zmieniające się potrzeby związane z infrastrukturą.

**Uaktualnianie, migracja i wdrażanie** — ten obszar koncentracji zawiera zalecenia ułatwiające uaktualnianie, migrowanie i wdrażanie SQL Server w istniejącej infrastrukturze.

**Operacje i monitorowanie** — ten obszar koncentracji pokazuje zalecenia ułatwiające usprawnienie operacji IT, implementowanie konserwacji zapobiegawczej i maksymalizowanie wydajności.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Czy należy dążyć do osiągnięcia wyniku 100% w każdym obszarze fokusu?

Niekoniecznie. Zalecenia są oparte na wiedzy i doświadczeniach zdobytych przez inżynierów firmy Microsoft w tysiącach wizyt klientów. Jednak żadne dwie infrastruktury serwera nie są takie same, a konkretne zalecenia mogą być bardziej lub mniej odpowiednie dla użytkownika. Na przykład niektóre zalecenia dotyczące zabezpieczeń mogą być mniej istotne, jeśli maszyny wirtualne nie są dostępne w Internecie. Niektóre zalecenia dotyczące dostępności mogą być mniej istotne w przypadku usług zapewniających zbieranie i raportowanie danych ad hoc o niskim priorytecie. Problemy, które są ważne dla dorosłych firm, mogą być mniej ważne do uruchomienia. Możesz chcieć określić, które obszary koncentracji są priorytetami, a następnie sprawdzić, jak wyniki zmieniają się wraz z upływem czasu.

Każde zalecenie zawiera wskazówki dotyczące przyczyny tego znaczenia. Skorzystaj z tych wskazówek, aby sprawdzić, czy wdrożenie zalecenia jest odpowiednie dla Ciebie, z uwzględnieniem charakteru usług IT i potrzeb firmy dla Twojej organizacji.

## <a name="use-health-check-focus-area-recommendations"></a>Użyj zaleceń dotyczących obszaru fokusu sprawdzania kondycji

Aby móc korzystać z rozwiązania sprawdzania kondycji w Log Analytics, musisz mieć zainstalowane rozwiązanie. Aby dowiedzieć się więcej o instalowaniu rozwiązań, zobacz [Instalowanie rozwiązania do zarządzania](./solutions.md). Po zainstalowaniu programu można wyświetlić podsumowanie zaleceń przy użyciu kafelka System Center Operations Manager Health Check na stronie **Przegląd** dla obszaru roboczego w Azure Portal.

Wyświetl podsumowanie ocen zgodności dla infrastruktury, a następnie zapoznaj się z zaleceniami.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aby wyświetlić zalecenia dotyczące obszaru koncentracji uwagi i podjąć działania naprawcze
1. Zaloguj się do Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com) .
2. W witrynie Azure Portal kliknij pozycję **Więcej usług** w lewym dolnym rogu. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
3. W okienku subskrypcje Log Analytics wybierz obszar roboczy, a następnie kliknij element menu **podsumowania obszaru roboczego** .  
4. Na stronie **Przegląd** kliknij kafelek **System Center Operations Manager Health Check** .
5. Na stronie **System Center Operations Manager Health Check** Przejrzyj informacje podsumowujące w jednym z fragmentów obszaru fokus, a następnie kliknij jeden, aby wyświetlić zalecenia dotyczące tego obszaru.
6. Na dowolnej stronie obszaru fokusu można wyświetlić zalecenia z priorytetami wykonane dla danego środowiska. Kliknij zalecenie w obszarze powiązane **obiekty** , aby wyświetlić szczegóły dotyczące przyczyny tego zalecenia.<br><br> ![obszar koncentracji uwagi](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. W **sugerowanych akcjach** można wykonać akcje naprawcze. Gdy element został rozkierowany, późniejsze oceny będą rejestrować te zalecane akcje, a Ocena zgodności ulegnie zwiększeniu. Poprawione elementy są wyświetlane jako **obiekty zakończone**.

## <a name="ignore-recommendations"></a>Ignorowanie zaleceń

Jeśli masz zalecenia, które chcesz zignorować, możesz utworzyć plik tekstowy, którego Log Analytics używa, aby zapobiec wyświetlaniu zaleceń w wynikach oceny.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Aby zidentyfikować zalecenia, które chcesz zignorować
1. W Azure Portal na stronie obszaru roboczego Log Analytics wybranego obszaru roboczego kliknij element menu **przeszukiwanie dzienników** .
2. Użyj następującego zapytania, aby wyświetlić listę zaleceń, których nie powiodło się w przypadku komputerów w danym środowisku.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Jeśli Twój obszar roboczy został uaktualniony do [nowego języka zapytań log Analytics](../logs/log-query-overview.md), powyższe zapytanie zmieni się na poniższe.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Oto zrzut ekranu przedstawiający zapytanie przeszukiwania dzienników:<br><br> ![przeszukiwanie dzienników](./media/scom-assessment/scom-log-search.png)<br>

3. Wybierz zalecenia, które chcesz zignorować. W następnej procedurze użyjesz wartości dla RecommendationId.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Aby utworzyć plik tekstowy IgnoreRecommendations.txt i używać go

1. Utwórz plik o nazwie IgnoreRecommendations.txt.
2. Wklej lub wpisz każdą RecommendationId każdego zalecenia, które Log Analytics mają być ignorowane w osobnym wierszu, a następnie Zapisz i zamknij plik.
3. Umieść plik w następującym folderze na każdym komputerze, na którym ma Log Analytics ignorowanie zaleceń.
4. Na serwerze zarządzania Operations Manager- *dysk_systemowy*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Aby sprawdzić, czy zalecenia są ignorowane

1. Po następnym zaplanowanym przebiegu oceny domyślnie co siedem dni określone zalecenia są oznaczone jako ignorowane i nie będą wyświetlane na pulpicie nawigacyjnym sprawdzania kondycji.
2. Aby wyświetlić listę wszystkich ignorowanych zaleceń, można użyć poniższych zapytań wyszukiwania w dzienniku.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Jeśli Twój obszar roboczy został uaktualniony do [nowego języka zapytań log Analytics](../logs/log-query-overview.md), powyższe zapytanie zmieni się na poniższe.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Jeśli zdecydujesz się później, aby zobaczyć zignorowane zalecenia, Usuń wszelkie IgnoreRecommendations.txt pliki lub Usuń z nich RecommendationIDs.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>Rozwiązanie System Center Operations Manager Health Check — często zadawane pytania

*Dodano rozwiązanie sprawdzania kondycji do obszaru roboczego Log Analytics. Ale nie widzę zaleceń. Dlaczego nie?* Po dodaniu rozwiązania wykonaj następujące kroki, aby wyświetlić zalecenia na pulpicie nawigacyjnym Log Analytics.  

- [Ustaw konto Uruchom jako dla System Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
- [Konfigurowanie reguły System Center Operations Manager Health Check](#configure-the-health-check-rule)


*Czy istnieje sposób skonfigurowania, jak często jest przeprowadzane sprawdzanie?* Tak. Zobacz [Konfigurowanie częstotliwości uruchamiania](#configure-the-run-frequency).

*Jeśli po dodaniu rozwiązania System Center Operations Manager Health Check zostanie odnaleziony inny serwer, zostanie on sprawdzony?* Tak, po przeprowadzeniu odnajdywania jest ono domyślnie zaznaczone co siedem dni.

*Jaka jest nazwa procesu, który wykonuje zbieranie danych?* AdvisorAssessment.exe

*Gdzie działa proces AdvisorAssessment.exe?* AdvisorAssessment.exe jest uruchamiany w ramach procesu HealthService serwera zarządzania, na którym włączono regułę sprawdzania kondycji. Korzystając z tego procesu, odnajdywanie całego środowiska jest realizowane za pośrednictwem zdalnego zbierania danych.

*Jak długo trwa zbieranie danych?* Zbieranie danych na serwerze trwa około jednej godziny. Może to trwać dłużej w środowiskach, które mają wiele wystąpień Operations Manager lub baz danych.

*Co zrobić, jeśli ustawiłem interwał oceny na mniej niż 1440 minut?* Ocena została wstępnie skonfigurowana do uruchomienia co najwyżej raz dziennie. Jeśli przesłonisz wartość interwału do wartości mniejszej niż 1440 minut, w ocenie będzie stosowana wartość interwału 1440 minut.

*Jak sprawdzić, czy występują błędy wymagań wstępnych?* Jeśli sprawdzanie kondycji zostało uruchomione i nie widzisz wyników, prawdopodobnie niektóre z wymagań wstępnych dla sprawdzenia nie powiodły się. Możesz wykonywać zapytania: `Operation Solution=SCOMAssessment` i `SCOMAssessmentRecommendation FocusArea=Prerequisites` w przeszukiwaniu dzienników, aby zobaczyć wymagania wstępne dotyczące niepowodzenia.

*Występuje `Failed to connect to the SQL Instance (….).` komunikat z błędami wymagań wstępnych. Jaki jest problem?* AdvisorAssessment.exe, proces służący do zbierania danych, jest uruchamiany w ramach procesu HealthService na serwerze zarządzania. W ramach kontroli kondycji proces próbuje nawiązać połączenie z SQL Server, w którym znajduje się Operations Manager baza danych. Ten błąd może wystąpić, gdy reguły zapory blokują połączenie z wystąpieniem SQL Server.

*Jakiego typu dane są zbierane?* Zbierane są następujące typy danych: — dane usługi WMI — dane rejestru — dane dziennika zdarzeń Operations Manager dane za pomocą programu Windows PowerShell, zapytań SQL i modułu zbierającego informacje o plikach.

*Dlaczego należy skonfigurować konto Uruchom jako?* W Operations Manager są uruchamiane różne zapytania SQL. Aby można było uruchomić je, należy użyć konta Uruchom jako z wymaganymi uprawnieniami. Ponadto do wykonywania zapytań w usłudze WMI wymagane są poświadczenia administratora lokalnego.

*Dlaczego są wyświetlane tylko 10 najważniejszych zaleceń?* Zamiast zapewniać wyczerpującą, przeciążą listę zadań, zalecamy najpierw skupić się na rozwiązaniu zaleceń z priorytetami. Po wprowadzeniu dodatkowych rekomendacji staną się dostępne. Jeśli wolisz wyświetlić listę Szczegółowa, możesz wyświetlić wszystkie zalecenia przy użyciu funkcji wyszukiwania dzienników.

*Czy istnieje sposób na ignorowanie rekomendacji?* Tak, zobacz sekcję [Ignorowanie zaleceń](#ignore-recommendations).


## <a name="next-steps"></a>Następne kroki

- [Wyszukaj dzienniki](../logs/log-query-overview.md) , aby dowiedzieć się, jak analizować szczegółowe System Center Operations Manager Health Check dane i zalecenia.

