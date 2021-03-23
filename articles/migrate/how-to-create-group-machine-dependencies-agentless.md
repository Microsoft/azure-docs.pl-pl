---
title: Konfigurowanie analizy zależności bez agentów w Azure Migrate
description: Skonfiguruj analizę zależności bez agentów w Azure Migrate.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 7966750d7c3e0f12bb9404a4d78bbc27e4075c52
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786587"
---
# <a name="analyze-server-dependencies-agentless"></a>Analizuj zależności serwera (bez agentów)

W tym artykule opisano sposób konfigurowania analizy zależności bez agenta za pomocą Azure Migrate: narzędzia do odnajdywania i oceny. [Analiza zależności](concepts-dependency-visualization.md) pomaga identyfikować i zrozumieć zależności między serwerami w celu oceny i migracji do platformy Azure.

> [!IMPORTANT]
> Analiza zależności bez agenta jest obecnie dostępna w wersji zapoznawczej dla serwerów działających w środowisku programu VMware, które zostały wykryte za pomocą narzędzia do odnajdywania i oceny Azure Migrate:.
> Ta wersja zapoznawcza jest objęta wsparciem klienta i może być używana na potrzeby obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Bieżące ograniczenia

- W widoku Analiza zależności obecnie nie można dodać ani usunąć serwera z grupy.
- Mapa zależności dla grupy serwerów jest obecnie niedostępna.
- W projekcie Azure Migrate zbieranie danych zależności można włączyć współbieżnie dla serwerów 1000. Można analizować większą liczbę serwerów przez sekwencjonowanie w partiach serwerów 1000.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Upewnij się, że [utworzono projekt](./create-manage-projects.md) z Azure Migrate: narzędzia odnajdywania i oceny dodane do niego.
- Przejrzyj [wymagania programu VMware](migrate-support-matrix-vmware.md#vmware-requirements) , aby przeprowadzić analizę zależności.
- Przejrzyj [wymagania dotyczące urządzenia](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) przed skonfigurowaniem urządzenia.
- [Przejrzyj wymagania dotyczące analizy zależności](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) przed włączeniem analizy zależności na serwerach.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Wdróż i Skonfiguruj urządzenie Azure Migrate

1. [Zapoznaj](migrate-appliance.md#appliance---vmware) się z wymaganiami dotyczącymi wdrażania urządzenia Azure Migrate.
2. Przejrzyj adresy URL platformy Azure, do których urządzenie będzie musiało uzyskać [](migrate-appliance.md#public-cloud-urls) dostęp w [chmurach publicznych i rządowych](migrate-appliance.md#government-cloud-urls).
3. [Przejrzyj dane](migrate-appliance.md#collected-data---vmware) zbierane przez urządzenie podczas odnajdywania i oceny.
4. [Zwróć uwagę](migrate-support-matrix-vmware.md#port-access-requirements) na wymagania dotyczące dostępu do portów dla urządzenia.
5. [Wdróż urządzenie Azure Migrate,](how-to-set-up-appliance-vmware.md) aby rozpocząć odnajdywanie. Aby wdrożyć urządzenie, należy pobrać i zaimportować szablon komórki jajowe do programu VMware w celu utworzenia serwera działającego w vCenter Server. Po wdrożeniu urządzenia należy zarejestrować je w projekcie i skonfigurować do inicjowania odnajdywania.
6. Podczas konfigurowania urządzenia należy określić następujące elementy w Menedżerze konfiguracji urządzenia:
    - Szczegóły vCenter Server, z którym chcesz nawiązać połączenie.
    - vCenter Server poświadczenia w zakresie odnajdywania serwerów w środowisku programu VMware.
    - Poświadczenia serwera, które mogą być poświadczeniami domeny/systemu Windows (niebędącymi domeną)/Linux (nienależących do domeny). [Dowiedz się więcej](add-server-credentials.md) na temat podania poświadczeń i sposobu ich obsługi.

## <a name="verify-permissions"></a>Weryfikowanie uprawnień

- Należy [utworzyć vCenter Server konto tylko do odczytu](./tutorial-discover-vmware.md#prepare-vmware) na potrzeby odnajdywania i oceny. Konto tylko do odczytu wymaga uprawnień włączonych do **Virtual Machines**  >  **operacji gościa**, aby umożliwić współdziałanie z serwerami w celu zbierania danych zależności.
- Musisz mieć konto użytkownika, aby Azure Migrate uzyskać dostęp do serwera w celu zbierania danych zależności. [Dowiedz się więcej](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) o wymaganiach dotyczących kont dla serwerów z systemami Windows i Linux.

### <a name="add-credentials-and-initiate-discovery"></a>Dodawanie poświadczeń i Inicjowanie odnajdywania

1. Otwórz Menedżera konfiguracji urządzenia, Ukończ sprawdzanie wymagań wstępnych i rejestrację urządzenia.
2. Przejdź do panelu **Zarządzanie poświadczeniami i źródłami odnajdywania** .
1.  W **kroku 1: podaj poświadczenia vCenter Server**, kliknij pozycję **Dodaj poświadczenia** , aby podać poświadczenia dla konta vCenter Server, które będzie używane przez urządzenie do odnajdywania serwerów uruchomionych na vCenter Server.
1. W **kroku 2: podaj vCenter Server Szczegóły**, kliknij pozycję **Dodaj źródło odnajdywania** , aby wybrać przyjazną nazwę dla poświadczeń z listy rozwijanej, określ **adres IP/nazwę FQDN** panelu wystąpienia vCenter Server :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="3 w Menedżerze konfiguracji urządzenia dla vCenter Server Szczegóły":::
1. W **kroku 3: podawanie poświadczeń serwera do wykonywania spisu oprogramowania, analizy zależności bez agenta oraz odnajdywania wystąpień SQL Server i baz danych**, kliknij przycisk **Dodaj poświadczenia** , aby podać wiele poświadczeń serwera w celu zainicjowania spisu oprogramowania.
1. Kliknij pozycję **Rozpocznij odnajdywanie**, aby uruchomić odnajdywanie vCenter Server.

 Po ukończeniu odnajdywania vCenter Server urządzenie inicjuje odnajdywanie zainstalowanych aplikacji, ról i funkcji (Spis oprogramowania). Podczas tworzenia spisu oprogramowania poświadczenia dodanych serwerów będą powtarzane względem serwerów i sprawdzane pod kątem analizy zależności bez agenta. Można włączyć analizę zależności bez agenta dla serwerów z poziomu portalu. Tylko serwery, na których Walidacja zakończyła się powodzeniem, można wybrać, aby włączyć analizę zależności bez agenta.

## <a name="start-dependency-discovery"></a>Uruchom odnajdywanie zależności

Wybierz serwery, na których chcesz włączyć odnajdowanie zależności.

1. W **Azure Migrate: odnajdywanie i ocenianie** kliknij pozycję **odnalezione serwery**.
2. Wybierz **nazwę urządzenia** , którego odnajdywanie chcesz przejrzeć.
1. Stan sprawdzania poprawności serwerów jest widoczny w obszarze **zależności (bez agentów)** .
1. Kliknij listę rozwijaną **analiza zależności** .
1. Kliknij pozycję **Dodaj serwery**.
1. Na stronie **Dodawanie serwerów** wybierz serwery, na których ma zostać włączona analiza zależności. Mapowanie zależności można włączyć tylko na tych serwerach, na których Walidacja zakończyła się pomyślnie. Następny cykl weryfikacji zostanie uruchomiony 24 godziny od ostatniego znacznika czasu sprawdzania poprawności.
1. Po wybraniu serwerów kliknij przycisk **Dodaj serwery**.

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="Rozpocznij analizę zależności":::

Można wizualizować zależności około sześciu godzin po włączeniu analizy zależności na serwerach. Jeśli chcesz jednocześnie włączyć wiele serwerów na potrzeby analizy zależności, możesz to zrobić za pomocą [programu PowerShell](#start-or-stop-dependency-analysis-using-powershell) .

## <a name="visualize-dependencies"></a>Wizualizacja zależności

1. W **Azure Migrate: odnajdywanie i ocenianie** kliknij pozycję **odnalezione serwery**.
1. Wybierz **nazwę urządzenia** , którego odnajdywanie chcesz przejrzeć.
1. Wyszukaj serwer, którego zależności chcesz przejrzeć.
1. W kolumnie **zależności (bez agentów)** kliknij pozycję **Wyświetl zależności**
1. Zmień okres, dla którego ma zostać wyświetlona mapa, przy użyciu listy rozwijanej czas **trwania** .
1. Rozwiń grupę **klientów** , aby wyświetlić listę serwerów z zależnością na wybranym serwerze.
1. Rozwiń grupę **portów** , aby wyświetlić listę serwerów z zależnością od wybranego serwera.
1. Aby przejść do widoku mapy dowolnego z serwerów zależnych, kliknij nazwę serwera > **Załaduj mapowanie serwera** 
 :::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="Rozwiń grupę portów serwera i Załaduj mapę serwera":::
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="Rozwiń grupę klientów":::

8. Rozwiń wybrany serwer, aby wyświetlić szczegóły poziomu procesu dla każdej zależności.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="Rozwiń serwer, aby wyświetlić procesy":::

> [!NOTE]
> Informacje o procesie dla zależności nie są zawsze dostępne. Jeśli ta funkcja jest niedostępna, zależność jest przedstawiana z procesem oznaczonym jako "nieznany proces".

## <a name="export-dependency-data"></a>Eksportuj dane zależności

1. W **Azure Migrate: odnajdywanie i ocenianie** kliknij pozycję **odnalezione serwery**.
2. Kliknij listę rozwijaną **analiza zależności** .
3. Kliknij pozycję **Eksportuj zależności aplikacji**.
4. Na stronie **Eksportuj zależności aplikacji** wybierz nazwę urządzenia, która umożliwia odnajdywanie żądanych serwerów.
5. Wybierz godzinę rozpoczęcia i godzinę zakończenia. Należy pamiętać, że dane można pobrać tylko w ciągu ostatnich 30 dni.
6. Kliknij pozycję **Eksportuj zależność**.

Dane zależności są eksportowane i pobierane w formacie CSV. Pobrany plik zawiera dane zależności między wszystkimi serwerami z włączoną analizą zależności. 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="Eksportuj zależności":::

### <a name="dependency-information"></a>Informacje o zależnościach

Każdy wiersz w wyeksportowanym woluminie CSV odpowiada zależności zaobserwowanej w określonym gnieździe czasu.

Poniższa tabela zawiera podsumowanie pól w wyeksportowanym pliku CSV. Zwróć uwagę, że pola Nazwa serwera, aplikacja i proces są wypełniane tylko dla serwerów, na których jest włączona analiza zależności bez agenta.

**Nazwa pola** | **Szczegóły**
--- | --- 
Timeslot | Timeslot, w którym zaobserwowano zależność. <br/> Dane zależności są obecnie przechwytywane za pośrednictwem gniazd 6-godzinnych.
Nazwa serwera źródłowego | Nazwa serwera źródłowego 
Aplikacja źródłowa | Nazwa aplikacji na serwerze źródłowym  
Proces źródłowy | Nazwa procesu na serwerze źródłowym  
Nazwa serwera docelowego | Nazwa serwera docelowego
Docelowy adres IP | Adres IP serwera docelowego
Aplikacja docelowa | Nazwa aplikacji na serwerze docelowym
Proces docelowy | Nazwa procesu na serwerze docelowym  
Port docelowy | Numer portu na serwerze docelowym

## <a name="stop-dependency-discovery"></a>Zatrzymaj odnajdywanie zależności

Wybierz serwery, na których chcesz zatrzymać odnajdywanie zależności.

1. W **Azure Migrate: odnajdywanie i ocenianie** kliknij pozycję **odnalezione serwery**.
1. Wybierz **nazwę urządzenia** , którego odnajdywanie chcesz przejrzeć.
1. Kliknij listę rozwijaną **analiza zależności** .
1. Kliknij przycisk **Usuń serwery**.
1. Na stronie **usuwanie serwerów** wybierz serwer, który ma zostać zatrzymany dla analizy zależności.
1. Po wybraniu serwerów kliknij przycisk **Usuń serwery**.

Jeśli chcesz zatrzymać zależność jednocześnie na wielu serwerach, możesz to zrobić za pomocą [programu PowerShell](#start-or-stop-dependency-analysis-using-powershell) .

## <a name="start-or-stop-dependency-analysis-using-powershell"></a>Uruchamianie lub zatrzymywanie analizy zależności przy użyciu programu PowerShell

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

2. Wybierz subskrypcję, w której został utworzony projekt 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importowanie pobranego modułu AzMig_Dependencies PowerShell

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Włączanie lub wyłączanie zbierania danych zależności

1. Pobierz listę odnalezionych serwerów w projekcie przy użyciu następujących poleceń. W poniższym przykładzie nazwa projektu to FabrikamDemoProject, a grupa zasobów, do której należy, to FabrikamDemoRG. Lista serwerów zostanie zapisana w FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    W pliku można zobaczyć nazwę wyświetlaną serwera, bieżący stan kolekcji zależności oraz identyfikator ARM wszystkich odnalezionych serwerów. 

2. Aby włączyć lub wyłączyć zależności, Utwórz wejściowy plik CSV. Plik musi mieć kolumnę z nagłówkiem "ARM ID". Wszystkie dodatkowe nagłówki w pliku CSV zostaną zignorowane. Wolumin CSV można utworzyć przy użyciu pliku wygenerowanego w poprzednim kroku. Utwórz kopię pliku z zachowaniem serwerów, na których chcesz włączyć lub wyłączyć zależności. 

    W poniższym przykładzie analiza zależności jest włączana na liście serwerów w pliku wejściowym FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    W poniższym przykładzie analiza zależności jest wyłączana na liście serwerów w pliku wejściowym FabrikamDemo_VMs_Disable.csv.

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

    - Wybierz subskrypcję, w której został utworzony projekt

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. Importowanie pobranego modułu AzMig_Dependencies PowerShell

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Uruchom następujące polecenie. To polecenie pobiera dane zależności w woluminie CSV i przetwarza je w celu wygenerowania listy unikatowych zależności, które mogą być używane do wizualizacji w Power BI. W poniższym przykładzie nazwa projektu to FabrikamDemoProject, a grupa zasobów, do której należy, to FabrikamDemoRG. Zależności zostaną pobrane dla serwerów odnalezionych przez FabrikamAppliance. Unikatowe zależności zostaną zapisane w FabrikamDemo_Dependencies.csv

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

[Serwery grupy](how-to-create-a-group.md) do oceny.