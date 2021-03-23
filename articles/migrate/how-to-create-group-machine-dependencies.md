---
title: Konfigurowanie analizy zależności opartej na agentach w Azure Migrate
description: W tym artykule opisano sposób konfigurowania analizy zależności opartej na agentach w Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 84a672f76de4b11558f2b39bf417a3eda2e31a36
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786536"
---
# <a name="set-up-dependency-visualization"></a>Konfigurowanie wizualizacji zależności

W tym artykule opisano sposób konfigurowania analizy zależności opartej na agentach w Azure Migrate: odnajdywanie i ocenianie. [Analiza zależności](concepts-dependency-visualization.md) pomaga identyfikować zależności między serwerami, które mają być oceniane i migrowane na platformę Azure.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Zapoznaj się z wymaganiami dotyczącymi obsługi i wdrażania analiz zależności opartych na agentach:
    - [Serwery w środowisku VMware](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Serwery fizyczne](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Serwery w środowisku funkcji Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- Upewnij się, że:
    - Mieć projekt Azure Migrate. Jeśli tego nie zrobisz, [Utwórz](./create-manage-projects.md) je teraz.
    - Sprawdź, czy [Dodano](how-to-assess.md) do projektu narzędzie do odnajdywania i oceny Azure Migrate:.
    - Skonfiguruj [urządzenie Azure Migrate](migrate-appliance.md) w celu odnajdywania serwerów lokalnych. Urządzenie odnajduje serwery lokalne i wysyła metadane i dane wydajności do Azure Migrate: odnajdywanie i ocenianie. Skonfiguruj urządzenie dla:
        - [Serwery w środowisku VMware](how-to-set-up-appliance-vmware.md)
        - [Serwery w środowisku funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md)
        - [Serwery fizyczne](how-to-set-up-appliance-physical.md)
- Aby użyć wizualizacji zależności, należy skojarzyć [obszar roboczy log Analytics](../azure-monitor/logs/manage-access.md) z projektem Azure Migrate:
    - Obszar roboczy można dołączyć dopiero po skonfigurowaniu urządzenia Azure Migrate i wykryciu serwerów w projekcie Azure Migrate.
    - Upewnij się, że masz obszar roboczy w subskrypcji zawierającej projekt Azure Migrate.
    - Obszar roboczy musi znajdować się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia lub Europa Zachodnia. Obszary robocze w innych regionach nie mogą być skojarzone z projektem.
    - Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
    - Do projektu Azure Migrate można skojarzyć nowy lub istniejący obszar roboczy Log Analytics.
    - Należy dołączyć obszar roboczy podczas pierwszego konfigurowania wizualizacji zależności dla serwera. Obszaru roboczego dla projektu Azure Migrate nie można modyfikować po jego dodaniu.
    - W Log Analytics obszar roboczy skojarzony z Azure Migrate jest otagowany przy użyciu klucza projektu migracji oraz nazwy projektu.

## <a name="associate-a-workspace"></a>Kojarzenie obszaru roboczego

1. Po wykryciu serwerów do oceny w obszarze **serwery**  >  **Azure Migrate: odnajdywanie i ocenianie**, kliknij przycisk **Przegląd**.  
2. W **Azure Migrate: odnajdywanie i ocenianie** kliknij pozycję **podstawowe**.
3. W **obszarze roboczym pakietu OMS** kliknij pozycję **wymaga konfiguracji**.

     ![Konfigurowanie obszaru roboczego usługi Log Analytics](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. W **obszarze Konfiguracja obszaru roboczego pakietu OMS** Określ, czy chcesz utworzyć nowy obszar roboczy, czy użyć istniejącego.
    - Możesz wybrać istniejący obszar roboczy ze wszystkich obszarów roboczych w subskrypcji projektu.
    - Potrzebujesz dostępu czytelnika do obszaru roboczego, aby go skojarzyć.
5. Jeśli tworzysz nowy obszar roboczy, wybierz dla niego lokalizację.

    ![Dodaj nowy obszar roboczy](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>Pobieranie i instalowanie agentów maszyny wirtualnej

Na każdym serwerze, który ma zostać przeanalizowany, zainstaluj agentów.

> [!NOTE]
> W przypadku serwerów monitorowanych przez System Center Operations Manager 2012 R2 lub nowsze nie trzeba instalować agenta MMA. Service Map integruje się z Operations Manager. [Postępuj zgodnie](../azure-monitor/vm/service-map-scom.md#prerequisites) ze wskazówkami dotyczącymi integracji.

1. W **Azure Migrate: odnajdywanie i ocenianie** kliknij pozycję **odnalezione serwery**.
2. Dla każdego serwera, który ma być analizowany za pomocą wizualizacji zależności, w kolumnie **zależności** kliknij pozycję **wymaga instalacji agenta**.
3. Na stronie **zależności** Pobierz agenta MMA i zależności dla systemu Windows lub Linux.
4. W obszarze **Konfigurowanie agenta MMA** Skopiuj identyfikator i klucz obszaru roboczego. Są one potrzebne podczas instalacji agenta MMA.

    ![Zainstaluj agentów](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Instalowanie programu MMA

Zainstaluj MMA na każdym serwerze z systemem Windows lub Linux, który ma zostać przeanalizowany.

### <a name="install-mma-on-a-windows-server"></a>Zainstaluj program MMA w systemie Windows Server

Aby zainstalować agenta na serwerze z systemem Windows:

1. Kliknij dwukrotnie pobranego agenta.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**. Na stronie **postanowienia licencyjne** kliknij przycisk **zgadzam** się, aby zaakceptować licencję.
3. W **folderze docelowym** Zachowaj lub zmodyfikuj domyślny Folder instalacji > **dalej**.
4. W obszarze **Opcje instalacji agenta** wybierz pozycję **Azure log Analytics**  >  **dalej**.
5. Kliknij przycisk **Dodaj** , aby dodać nowy obszar roboczy log Analytics. Wklej w obszarze Identyfikator i klucz obszaru roboczego skopiowane z portalu. Kliknij przycisk **Dalej**.

Agenta można zainstalować z wiersza polecenia lub przy użyciu metody zautomatyzowanej, takiej jak Configuration Manager lub [Intigua](https://www.intigua.com/intigua-for-azure-migration).
- [Dowiedz się więcej](../azure-monitor/agents/log-analytics-agent.md#installation-options) na temat korzystania z tych metod w celu zainstalowania agenta MMA.
- Agenta programu MMA można również zainstalować za pomocą [tego](https://github.com/brianbar-MSFT/Install-MMA) skryptu.
- [Dowiedz się więcej](../azure-monitor/agents/agents-overview.md#supported-operating-systems) o systemach operacyjnych Windows obsługiwanych przez MMA.

### <a name="install-mma-on-a-linux-server"></a>Instalowanie MMA na serwerze z systemem Linux

Aby zainstalować MMA na serwerze z systemem Linux:

1. Przenieś odpowiedni zbiór (x86 lub x64) na komputer z systemem Linux przy użyciu protokołu SCP/SFTP.
2. Zainstaluj pakiet przy użyciu argumentu--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Dowiedz się więcej](../azure-monitor/agents/agents-overview.md#supported-operating-systems) o obsłudze systemu operacyjnego Linux przez program MMA. 

## <a name="install-the-dependency-agent"></a>Instalowanie agenta zależności

1. Aby zainstalować agenta zależności na serwerze z systemem Windows, kliknij dwukrotnie plik Instalatora i postępuj zgodnie z instrukcjami kreatora.
2. Aby zainstalować agenta zależności na serwerze z systemem Linux, należy zainstalować program jako katalog główny przy użyciu następującego polecenia:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Dowiedz się więcej](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) na temat sposobu instalowania agenta zależności za pomocą skryptów.
- [Dowiedz się więcej](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) o systemach operacyjnych obsługiwanych przez agenta zależności.


## <a name="create-a-group-using-dependency-visualization"></a>Tworzenie grupy przy użyciu wizualizacji zależności

Teraz Utwórz grupę do oceny. 


> [!NOTE]
> Grupy, dla których chcesz wizualizować zależności, nie mogą zawierać więcej niż 10 serwerów. Jeśli masz więcej niż 10 serwerów, Podziel je na mniejsze grupy.

1. W **Azure Migrate: odnajdywanie i ocenianie** kliknij pozycję **odnalezione serwery**.
2. W kolumnie **zależności** kliknij pozycję **Wyświetl zależności** dla każdego serwera, który chcesz przejrzeć.
3. Na mapie zależności można zobaczyć następujące elementy:
    - Połączenia TCP przychodzące (klienckie) i wychodzące (serwery), do i z serwera programu.
    - Serwery zależne, na których nie zainstalowano agentów zależności, są pogrupowane według numerów portów.
    - Serwery zależne z zainstalowanymi agentami zależności są wyświetlane jako oddzielne pola.
    - Procesy działające na serwerze. Rozwiń wszystkie pola serwera, aby wyświetlić procesy.
    - Właściwości serwera (w tym nazwa FQDN, system operacyjny, adres MAC). Kliknij każde pole serwer, aby wyświetlić szczegóły.

4. Można przyjrzeć się zależnościom dla różnych czasów trwania, klikając czas trwania w etykiecie zakresu czasu.
    - Domyślnie zakresem jest godzina. 
    - Możesz zmodyfikować zakres czasu lub określić datę początkową i końcową oraz czas trwania.
    - Zakres czasu może być maksymalnie godzinę. Jeśli potrzebujesz dłuższego zakresu, użyj Azure Monitor do wykonywania zapytań dotyczących danych zależnych przez dłuższy czas.

5. Po zidentyfikowaniu zależnych serwerów, które chcesz zgrupować, użyj kombinacji klawiszy CTRL i kliknięcia, aby wybrać wiele serwerów na mapie, a następnie kliknij pozycję **grupy maszyn**.
6. Określ nazwę grupy.
7. Sprawdź, czy serwery zależne są odnajdywane przez Azure Migrate.

    - Jeśli serwer zależny nie zostanie odnaleziony przez Azure Migrate: odnajdywanie i ocenianie, nie można dodać go do grupy.
    - Aby dodać serwer, ponownie uruchom odnajdywanie i sprawdź, czy serwer został odnaleziony.

8. Jeśli chcesz utworzyć ocenę dla tej grupy, zaznacz pole wyboru, aby utworzyć nową ocenę dla grupy.
8. Kliknij przycisk **OK** , aby zapisać grupę.

Po utworzeniu grupy zalecamy zainstalowanie agentów na wszystkich serwerach w grupie, a następnie wizualizowanie zależności dla całej grupy.

## <a name="query-dependency-data-in-azure-monitor"></a>Wykonywanie zapytań względem danych zależności w Azure Monitor

Możesz badać dane zależności przechwycone przez Service Map w obszarze roboczym Log Analytics skojarzonym z projektem Azure Migrate. Log Analytics służy do zapisywania i uruchamiania zapytań dziennika Azure Monitor.

- [Dowiedz się, jak](../azure-monitor/vm/service-map.md#log-analytics-records) wyszukiwać Service map dane w log Analytics.
- [Zapoznaj się z omówieniem](../azure-monitor/logs/get-started-queries.md)  zapisywania zapytań dzienników w [log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).

Uruchom zapytanie dotyczące danych zależności w następujący sposób:

1. Po zainstalowaniu agentów przejdź do portalu, a następnie kliknij pozycję **Przegląd**.
2. W **Azure Migrate: odnajdywanie i ocenianie**, kliknij przycisk **Przegląd**. Kliknij strzałkę w dół, aby rozwinąć **podstawowe** elementy.
3. W **obszarze roboczym pakietu OMS** kliknij nazwę obszaru roboczego.
3. Na stronie obszaru roboczego Log Analytics > **Ogólne** kliknij pozycję **dzienniki**.
4. Napisz zapytanie i kliknij przycisk **Uruchom**.

### <a name="sample-queries"></a>Przykładowe zapytania

Poniżej przedstawiono kilka przykładowych zapytań, których można użyć do wyodrębnienia danych zależności.

- Zapytania można modyfikować w celu wyodrębnienia preferowanych punktów danych.
- [Zapoznaj](../azure-monitor/vm/service-map.md#log-analytics-records) się z pełną listą rekordów danych zależności.
- [Przejrzyj](../azure-monitor/vm/service-map.md#sample-log-searches) dodatkowe przykładowe zapytania.

#### <a name="sample-review-inbound-connections"></a>Przykład: przeglądanie połączeń przychodzących

Przejrzyj połączenia przychodzące dla zestawu serwerów.

- Rekordy w tabeli dla metryk połączeń (VMConnection) nie reprezentują poszczególnych fizycznych połączeń sieciowych.
- Wiele połączeń sieci fizycznych jest zgrupowanych w połączenie logiczne.
- [Dowiedz się więcej](../azure-monitor/vm/service-map.md#connections) o tym, jak dane połączenia sieci fizycznej są agregowane w VMConnection.

```
// the servers of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Przykład: Podsumowanie wysłanych i odebranych danych

Ten przykład zawiera podsumowanie ilości danych wysłanych i odebranych w połączeniach przychodzących między zestawem serwerów.

```
// the servers of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Następne kroki

[Utwórz ocenę](how-to-create-assessment.md) dla grupy.
