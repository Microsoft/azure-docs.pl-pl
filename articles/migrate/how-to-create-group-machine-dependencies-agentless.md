---
title: Konfigurowanie analizy zależności bez agentów w ocenie serwera Azure Migrate
description: Skonfiguruj analizę zależności bez agenta w ocenie serwera Azure Migrate.
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 57e5add810cf4fac232bce08fc7ca96df0a7c3a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91667473"
---
# <a name="analyze-machine-dependencies-agentless"></a>Analizowanie zależności maszyny (bez agentów)

W tym artykule opisano sposób konfigurowania analizy zależności bez agentów w Azure Migrate: Ocena serwera. [Analiza zależności](concepts-dependency-visualization.md) pomaga identyfikować i zrozumieć zależności między maszynami w celu oceny i migracji do platformy Azure.


> [!IMPORTANT]
> Wizualizacja zależności bez agenta jest obecnie dostępna w wersji zapoznawczej dla maszyn wirtualnych VMware odnalezionych za pomocą narzędzia do oceny serwera Azure Migrate:.
> Funkcje mogą być ograniczone lub niekompletne.
> Ta wersja zapoznawcza jest objęta wsparciem klienta i może być używana na potrzeby obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Bieżące ograniczenia

- W widoku Analiza zależności nie można obecnie dodawać ani usuwać serwera z grupy.
- Mapa zależności dla grupy serwerów jest obecnie niedostępna.
- Zbieranie danych zależności można skonfigurować współbieżnie dla serwerów 1000. Można analizować większą liczbę serwerów przez sekwencjonowanie w partiach 1000.

## <a name="before-you-start"></a>Przed rozpoczęciem

- [Przejrzyj](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) obsługiwane systemy operacyjne i wymagane uprawnienia.
- Upewnij się, że:
    - Mieć projekt Azure Migrate. Jeśli tego nie zrobisz, [Utwórz](how-to-add-tool-first-time.md) je teraz.
    - Sprawdź, czy [dodano](how-to-assess.md) Azure Migrate: Narzędzie do oceny serwera do projektu.
    - Skonfiguruj [urządzenie Azure Migrate](migrate-appliance.md) w celu odnajdywania maszyn lokalnych. [Skonfiguruj urządzenie](how-to-set-up-appliance-vmware.md) dla maszyn wirtualnych VMware. Urządzenie odnajduje maszyny lokalne i wysyła metadane i dane wydajności do Azure Migrate: Ocena serwera.
- Sprawdź, czy narzędzia VMware (nowsze niż 10,2) są zainstalowane na każdej maszynie wirtualnej, którą chcesz przeanalizować.


## <a name="create-a-user-account-for-discovery"></a>Tworzenie konta użytkownika na potrzeby odnajdywania

Skonfiguruj konto użytkownika, aby Ocena serwera mogła uzyskać dostęp do maszyny wirtualnej w celu odnalezienia zależności. [Dowiedz się więcej](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) o wymaganiach dotyczących konta maszyn wirtualnych z systemami Windows i Linux.


## <a name="add-the-user-account-to-the-appliance"></a>Dodaj konto użytkownika do urządzenia

Dodaj konto użytkownika do urządzenia.

1. Otwórz aplikację zarządzanie urządzeniami. 
2. Przejdź do panelu **Podaj szczegóły programu vCenter** .
3. W obszarze **Znajdź aplikację i zależności na maszynach wirtualnych**kliknij pozycję **Dodaj poświadczenia** .
3. Wybierz **system operacyjny**, podaj przyjazną nazwę konta i hasło w polu **Nazwa użytkownika** / **Password**
6. Kliknij przycisk **Zapisz**.
7. Kliknij przycisk **Zapisz i Rozpocznij odnajdywanie**.

    ![Dodaj konto użytkownika maszyny wirtualnej](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Uruchom odnajdywanie zależności

Wybierz maszyny, na których chcesz włączyć odnajdowanie zależności. 

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. Kliknij ikonę **analiza zależności** .
3. Kliknij pozycję **Dodaj serwery**.
4. Na stronie **Dodawanie serwerów** wybierz urządzenie, które odnajduje odpowiednie maszyny.
5. Z listy maszyna wybierz maszyny.
6. Kliknij pozycję **Dodaj serwery**.

    ![Uruchom odnajdywanie zależności](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Możesz wizualizować zależności na sześć godzin po rozpoczęciu odnajdywania zależności. Jeśli chcesz włączyć kilka maszyn, możesz to zrobić za pomocą [programu PowerShell](#start-or-stop-dependency-discovery-using-powershell) .

## <a name="visualize-dependencies"></a>Wizualizacja zależności

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. Wyszukaj maszynę, którą chcesz wyświetlić.
3. W kolumnie **zależności** kliknij pozycję **Wyświetl zależności.**
4. Zmień okres, dla którego ma zostać wyświetlona mapa, przy użyciu listy rozwijanej czas **trwania** .
5. Rozwiń grupę **klientów** , aby wyświetlić listę maszyn z zależnością na wybranym komputerze.
6. Rozwiń grupę **portów** , aby wyświetlić listę maszyn z zależnością od wybranej maszyny.
7. Aby przejść do widoku mapy dowolnych maszyn zależnych, kliknij nazwę komputera > **Załaduj mapę serwera**

    ![Rozwiń węzeł Grupa portów serwera i Załaduj mapę serwera](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Rozwiń grupę klientów ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Rozwiń wybraną maszynę, aby wyświetlić szczegóły poziomu procesu dla każdej zależności.

    ![Rozwiń serwer, aby wyświetlić procesy](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Informacje o procesie dla zależności nie są zawsze dostępne. Jeśli ta funkcja jest niedostępna, zależność jest przedstawiana z procesem oznaczonym jako "nieznany proces".

## <a name="export-dependency-data"></a>Eksportuj dane zależności

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. Kliknij ikonę **analiza zależności** .
3. Kliknij pozycję **Eksportuj zależności aplikacji**.
4. Na stronie **Eksportuj zależności aplikacji** wybierz urządzenie, które odnajduje odpowiednie maszyny.
5. Wybierz godzinę rozpoczęcia i godzinę zakończenia. Należy pamiętać, że dane można pobrać tylko w ciągu ostatnich 30 dni.
6. Kliknij pozycję **Eksportuj zależność**.

Dane zależności są eksportowane i pobierane w formacie CSV. Pobrany plik zawiera dane zależności między wszystkimi maszynami, na których włączono obsługę analizy zależności. 

![Eksportuj zależności](./media/how-to-create-group-machine-dependencies-agentless/export.png)

### <a name="dependency-information"></a>Informacje o zależnościach

Każdy wiersz w wyeksportowanym woluminie CSV odpowiada zależności zaobserwowanej w określonym gnieździe czasu. 

Poniższa tabela zawiera podsumowanie pól w wyeksportowanym pliku CSV. Zwróć uwagę, że pola Nazwa serwera, aplikacja i proces są wypełniane tylko dla serwerów, na których jest włączona analiza zależności bez agenta.

**Nazwa pola** | **Szczegóły**
--- | --- 
Timeslot | Timeslot, w którym zaobserwowano zależność. <br/> Dane zależności są obecnie przechwytywane za pośrednictwem gniazd 6-godzinnych.
Nazwa serwera źródłowego | Nazwa maszyny źródłowej 
Aplikacja źródłowa | Nazwa aplikacji na maszynie źródłowej 
Proces źródłowy | Nazwa procesu na maszynie źródłowej 
Nazwa serwera docelowego | Nazwa maszyny docelowej
Docelowy adres IP | Adres IP maszyny docelowej
Aplikacja docelowa | Nazwa aplikacji na maszynie docelowej
Proces docelowy | Nazwa procesu na maszynie docelowej 
Port docelowy | Numer portu na maszynie docelowej


## <a name="stop-dependency-discovery"></a>Zatrzymaj odnajdywanie zależności

Wybierz maszyny, na których chcesz zatrzymać odnajdywanie zależności. 

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. Kliknij ikonę **analiza zależności** .
3. Kliknij przycisk **Usuń serwery**.
3. Na stronie **usuwanie serwerów** wybierz **urządzenie** , które odnajduje maszyny wirtualne, na których chcesz zatrzymać odnajdywanie zależności.
4. Z listy maszyna wybierz maszyny.
5. Kliknij przycisk **Usuń serwery**.

Jeśli chcesz zatrzymać zależność od kilku maszyn, możesz to zrobić za pomocą [programu PowerShell](#start-or-stop-dependency-discovery-using-powershell) .


## <a name="start-or-stop-dependency-discovery-using-powershell"></a>Uruchamianie lub zatrzymywanie odnajdywania zależności przy użyciu programu PowerShell

Pobierz moduł programu PowerShell z repozytorium [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) w witrynie GitHub.


### <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia cmdlet Connect-AzAccount.

    ```PowerShell
    Connect-AzAccount
    ```
    W przypadku używania Azure Government Użyj następującego polecenia.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Wybierz subskrypcję, w której utworzono projekt Azure Migrate 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importowanie pobranego modułu AzMig_Dependencies PowerShell

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Włączanie lub wyłączanie zbierania danych zależności

1. Pobierz listę odnalezionych maszyn wirtualnych VMware w projekcie Azure Migrate przy użyciu następujących poleceń. W poniższym przykładzie nazwa projektu to FabrikamDemoProject, a grupa zasobów, do której należy, to FabrikamDemoRG. Lista maszyn zostanie zapisana w FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    W pliku można zobaczyć nazwę wyświetlaną maszyny wirtualnej, bieżący stan kolekcji zależności oraz identyfikator ARM wszystkich odnalezionych maszyn wirtualnych. 

2. Aby włączyć lub wyłączyć zależności, Utwórz wejściowy plik CSV. Plik musi mieć kolumnę z nagłówkiem "ARM ID". Wszystkie dodatkowe nagłówki w pliku CSV zostaną zignorowane. Wolumin CSV można utworzyć przy użyciu pliku wygenerowanego w poprzednim kroku. Utwórz kopię pliku z zachowaniem maszyn wirtualnych, na których chcesz włączyć lub wyłączyć zależności. 

    W poniższym przykładzie analiza zależności jest włączana na liście maszyn wirtualnych w pliku wejściowym FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    W poniższym przykładzie analiza zależności jest wyłączana na liście maszyn wirtualnych w pliku wejściowym FabrikamDemo_VMs_Disable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Wizualizowanie połączeń sieciowych w Power BI

Azure Migrate oferuje szablon Power BI, którego można użyć do wizualizacji połączeń sieciowych wielu serwerów jednocześnie i filtrowania według procesu i serwera. Aby wizualizować, Załaduj Power BI z danymi zależności zgodnie z poniższymi instrukcjami.

1. Pobierz moduł programu PowerShell i szablon Power BI z repozytorium [przykładów Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) w witrynie GitHub.

2. Zaloguj się do platformy Azure, korzystając z poniższych instrukcji: 
- Zaloguj się do subskrypcji platformy Azure za pomocą polecenia cmdlet Connect-AzAccount.

    ```PowerShell
    Connect-AzAccount
    ```

- W przypadku używania Azure Government Użyj następującego polecenia.

    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

- Wybierz subskrypcję, w której utworzono projekt Azure Migrate 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importowanie pobranego modułu AzMig_Dependencies PowerShell

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Uruchom następujące polecenie. To polecenie pobiera dane zależności w woluminie CSV i przetwarza je w celu wygenerowania listy unikatowych zależności, które mogą być używane do wizualizacji w Power BI. W poniższym przykładzie nazwa projektu to FabrikamDemoProject, a grupa zasobów, do której należy, to FabrikamDemoRG. Zależności zostaną pobrane dla maszyn odnalezionych przez FabrikamAppliance. Unikatowe zależności zostaną zapisane w FabrikamDemo_Dependencies.csv

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. Otwórz pobrany szablon Power BI

6. Załaduj pobrane dane zależności w Power BI.
    - Otwórz szablon w Power BI.
    - Kliknij pozycję **Pobierz dane** na pasku narzędzi. 
    - Wybierz pozycję **tekst/CSV** ze wspólnych źródeł danych.
    - Wybierz pobrany plik zależności.
    - Kliknij przycisk **Załaduj**.
    - Zostanie wyświetlona tabela zostanie zaimportowana z nazwą pliku CSV. Tabelę można zobaczyć na pasku pola po prawej stronie. Zmień nazwę na AzMig_Dependencies
    - Kliknij przycisk Odśwież na pasku narzędzi.

    Na wykresie połączenia sieciowe i nazwa serwera źródłowego, nazwa serwera docelowego, nazwa procesu źródłowego, fragmentatory nazw procesów docelowych powinny być jasne z zaimportowanymi danymi.

7. Wizualizuj mapę połączeń sieciowych filtrowanie według serwerów i procesów. Zapisz plik.


## <a name="next-steps"></a>Następne kroki

[Grupuj maszyny](how-to-create-a-group.md) do oceny.
