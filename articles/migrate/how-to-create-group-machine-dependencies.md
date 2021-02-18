---
title: Konfigurowanie analizy zależności opartej na agentach w ocenie serwera Azure Migrate
description: W tym artykule opisano sposób konfigurowania analizy zależności opartej na agentach w ramach oceny serwera Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 1a3f2ae4829c7f4ae41d31e2a2fc35d79adf3d4c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596706"
---
# <a name="set-up-dependency-visualization"></a>Konfigurowanie wizualizacji zależności

W tym artykule opisano sposób konfigurowania analizy zależności opartej na agentach w Azure Migrate: Ocena serwera. [Analiza zależności](concepts-dependency-visualization.md) pomaga identyfikować i zrozumieć zależności między maszynami, które mają być oceniane i migrowane do platformy Azure.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Zapoznaj się z wymaganiami dotyczącymi obsługi i wdrażania analiz zależności opartych na agentach:
    - [Maszyny wirtualne VMware](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Serwery fizyczne](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Maszyny wirtualne funkcji Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- Upewnij się, że:
    - Mieć projekt Azure Migrate. Jeśli tego nie zrobisz, [Utwórz](./create-manage-projects.md) je teraz.
    - Sprawdź, czy [dodano](how-to-assess.md) Azure Migrate: Narzędzie do oceny serwera do projektu.
    - Skonfiguruj [urządzenie Azure Migrate](migrate-appliance.md) w celu odnajdywania maszyn lokalnych. Urządzenie odnajduje maszyny lokalne i wysyła metadane i dane wydajności do Azure Migrate: Ocena serwera. Skonfiguruj urządzenie dla:
        - [Oprogramowanie VMware](how-to-set-up-appliance-vmware.md) Maszyn.
        - [Funkcja Hyper-V](how-to-set-up-appliance-hyper-v.md) Maszyn.
        - [Serwery fizyczne](how-to-set-up-appliance-physical.md).
- Aby użyć wizualizacji zależności, należy skojarzyć [obszar roboczy log Analytics](../azure-monitor/logs/manage-access.md) z projektem Azure Migrate:
    - Obszar roboczy można dołączyć dopiero po skonfigurowaniu urządzenia Azure Migrate i odnalezieniu maszyn w projekcie Azure Migrate.
    - Upewnij się, że masz obszar roboczy w subskrypcji zawierającej projekt Azure Migrate.
    - Obszar roboczy musi znajdować się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia lub Europa Zachodnia. Obszary robocze w innych regionach nie mogą być skojarzone z projektem.
    - Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
    - Do projektu Azure Migrate można skojarzyć nowy lub istniejący obszar roboczy Log Analytics.
    - Podczas pierwszego konfigurowania wizualizacji zależności dla komputera należy dołączyć obszar roboczy. Obszaru roboczego dla projektu Azure Migrate nie można modyfikować po jego dodaniu.
    - W Log Analytics obszar roboczy skojarzony z Azure Migrate jest otagowany przy użyciu klucza projektu migracji oraz nazwy projektu.

## <a name="associate-a-workspace"></a>Kojarzenie obszaru roboczego

1. Po wykryciu maszyn do oceny w obszarze **serwery**  >  **Azure Migrate: Ocena serwera**, kliknij przycisk **Przegląd**.  
2. W **Azure Migrate: Ocena serwera**, kliknij przycisk **Essentials**.
3. W **obszarze roboczym pakietu OMS** kliknij pozycję **wymaga konfiguracji**.

     ![Konfigurowanie obszaru roboczego usługi Log Analytics](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. W **obszarze Konfiguracja obszaru roboczego pakietu OMS** Określ, czy chcesz utworzyć nowy obszar roboczy, czy użyć istniejącego.
    - Możesz wybrać istniejący obszar roboczy ze wszystkich obszarów roboczych w subskrypcji Migrowanie projektu.
    - Potrzebujesz dostępu czytelnika do obszaru roboczego, aby go skojarzyć.
5. Jeśli tworzysz nowy obszar roboczy, wybierz dla niego lokalizację.

    ![Dodaj nowy obszar roboczy](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>Pobieranie i instalowanie agentów maszyny wirtualnej

Na każdej maszynie, którą chcesz analizować, zainstaluj agentów.

> [!NOTE]
> W przypadku maszyn monitorowanych przez System Center Operations Manager 2012 R2 lub nowsze nie trzeba instalować agenta MMA. Service Map integruje się z Operations Manager. [Postępuj zgodnie](../azure-monitor/vm/service-map-scom.md#prerequisites) ze wskazówkami dotyczącymi integracji.

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. Dla każdej maszyny, którą chcesz analizować za pomocą wizualizacji zależności, w kolumnie **zależności** kliknij pozycję **wymagana instalacja agenta**.
3. Na stronie **zależności** Pobierz agenta MMA i zależności dla systemu Windows lub Linux.
4. W obszarze **Konfigurowanie agenta MMA** Skopiuj identyfikator i klucz obszaru roboczego. Są one potrzebne podczas instalacji agenta MMA.

    ![Zainstaluj agentów](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Instalowanie programu MMA

Zainstaluj MMA na każdej maszynie z systemem Windows lub Linux, którą chcesz analizować.

### <a name="install-mma-on-a-windows-machine"></a>Instalowanie MMA na komputerze z systemem Windows

Aby zainstalować agenta na komputerze z systemem Windows:

1. Kliknij dwukrotnie pobranego agenta.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**. Na stronie **postanowienia licencyjne** kliknij przycisk **zgadzam** się, aby zaakceptować licencję.
3. W **folderze docelowym** Zachowaj lub zmodyfikuj domyślny Folder instalacji > **dalej**.
4. W obszarze **Opcje instalacji agenta** wybierz pozycję **Azure log Analytics**  >  **dalej**.
5. Kliknij przycisk **Dodaj** , aby dodać nowy obszar roboczy log Analytics. Wklej w obszarze Identyfikator i klucz obszaru roboczego skopiowane z portalu. Kliknij przycisk **Dalej**.

Agenta można zainstalować z wiersza polecenia lub przy użyciu metody zautomatyzowanej, takiej jak Configuration Manager lub [Intigua](https://www.intigua.com/intigua-for-azure-migration).
- [Dowiedz się więcej](../azure-monitor/agents/log-analytics-agent.md#installation-options) na temat korzystania z tych metod w celu zainstalowania agenta MMA.
- Agenta programu MMA można również zainstalować za pomocą [tego](https://github.com/brianbar-MSFT/Install-MMA) skryptu.
- [Dowiedz się więcej](../azure-monitor/agents/agents-overview.md#supported-operating-systems) o systemach operacyjnych Windows obsługiwanych przez MMA.

### <a name="install-mma-on-a-linux-machine"></a>Instalowanie MMA na komputerze z systemem Linux

Aby zainstalować MMA na komputerze z systemem Linux:

1. Przenieś odpowiedni zbiór (x86 lub x64) na komputer z systemem Linux przy użyciu protokołu SCP/SFTP.
2. Zainstaluj pakiet przy użyciu argumentu--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Dowiedz się więcej](../azure-monitor/agents/agents-overview.md#supported-operating-systems) o obsłudze systemu operacyjnego Linux przez program MMA. 

## <a name="install-the-dependency-agent"></a>Instalowanie agenta zależności

1. Aby zainstalować agenta zależności na komputerze z systemem Windows, kliknij dwukrotnie plik Instalatora i postępuj zgodnie z instrukcjami kreatora.
2. Aby zainstalować agenta zależności na komputerze z systemem Linux, należy zainstalować program jako katalog główny przy użyciu następującego polecenia:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Dowiedz się więcej](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) na temat sposobu instalowania agenta zależności za pomocą skryptów.
- [Dowiedz się więcej](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) o systemach operacyjnych obsługiwanych przez agenta zależności.


## <a name="create-a-group-using-dependency-visualization"></a>Tworzenie grupy przy użyciu wizualizacji zależności

Teraz Utwórz grupę do oceny. 


> [!NOTE]
> Grupy, dla których chcesz wizualizować zależności, nie mogą zawierać więcej niż 10 maszyn. Jeśli masz więcej niż 10 maszyn, Podziel je na mniejsze grupy.

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. W kolumnie **zależności** kliknij pozycję **Wyświetl zależności** dla każdej maszyny, którą chcesz przejrzeć.
3. Na mapie zależności można zobaczyć następujące elementy:
    - Połączenia TCP przychodzące (klienckie) i wychodzące (serwery), do i z komputera.
    - Maszyny zależne, na których nie zainstalowano agentów zależności, są pogrupowane według numerów portów.
    - Komputery zależne z zainstalowanymi agentami zależności są wyświetlane jako oddzielne pola.
    - Procesy działające na maszynie. Rozwiń wszystkie pola maszyn, aby wyświetlić procesy.
    - Właściwości maszyny (w tym nazwa FQDN, system operacyjny, adres MAC). Kliknij każde pole komputera, aby wyświetlić szczegóły.

4. Można przyjrzeć się zależnościom dla różnych czasów trwania, klikając czas trwania w etykiecie zakresu czasu.
    - Domyślnie zakresem jest godzina. 
    - Możesz zmodyfikować zakres czasu lub określić datę początkową i końcową oraz czas trwania.
    - Zakres czasu może być maksymalnie godzinę. Jeśli potrzebujesz dłuższego zakresu, użyj Azure Monitor do wykonywania zapytań dotyczących danych zależnych przez dłuższy czas.

5. Po zidentyfikowaniu maszyn zależnych, które mają być pogrupowane, użyj kombinacji klawiszy CTRL i kliknięcia, aby wybrać wiele maszyn na mapie, a następnie kliknij pozycję **Grupuj maszyny**.
6. Określ nazwę grupy.
7. Sprawdź, czy maszyny zależne są odnajdywane przez Azure Migrate.

    - Jeśli maszyna zależna nie zostanie odnaleziona przez Azure Migrate: Ocena serwera, nie można dodać jej do grupy.
    - Aby dodać maszynę, ponownie uruchom odnajdywanie i sprawdź, czy komputer został odnaleziony.

8. Jeśli chcesz utworzyć ocenę dla tej grupy, zaznacz pole wyboru, aby utworzyć nową ocenę dla grupy.
8. Kliknij przycisk **OK** , aby zapisać grupę.

Po utworzeniu grupy zalecamy zainstalowanie agentów na wszystkich komputerach w grupie, a następnie wizualizowanie zależności dla całej grupy.

## <a name="query-dependency-data-in-azure-monitor"></a>Wykonywanie zapytań względem danych zależności w Azure Monitor

Możesz badać dane zależności przechwycone przez Service Map w obszarze roboczym Log Analytics skojarzonym z projektem Azure Migrate. Log Analytics służy do zapisywania i uruchamiania zapytań dziennika Azure Monitor.

- [Dowiedz się, jak](../azure-monitor/vm/service-map.md#log-analytics-records) wyszukiwać Service map dane w log Analytics.
- [Zapoznaj się z omówieniem](../azure-monitor/logs/get-started-queries.md)  zapisywania zapytań dzienników w [log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).

Uruchom zapytanie dotyczące danych zależności w następujący sposób:

1. Po zainstalowaniu agentów przejdź do portalu, a następnie kliknij pozycję **Przegląd**.
2. W **Azure Migrate: Ocena serwera**, kliknij przycisk **Przegląd**. Kliknij strzałkę w dół, aby rozwinąć **podstawowe** elementy.
3. W **obszarze roboczym pakietu OMS** kliknij nazwę obszaru roboczego.
3. Na stronie obszaru roboczego Log Analytics > **Ogólne** kliknij pozycję **dzienniki**.
4. Napisz zapytanie i kliknij przycisk **Uruchom**.

### <a name="sample-queries"></a>Przykładowe zapytania

Poniżej przedstawiono kilka przykładowych zapytań, których można użyć do wyodrębnienia danych zależności.

- Zapytania można modyfikować w celu wyodrębnienia preferowanych punktów danych.
- [Zapoznaj](../azure-monitor/vm/service-map.md#log-analytics-records) się z pełną listą rekordów danych zależności.
- [Przejrzyj](../azure-monitor/vm/service-map.md#sample-log-searches) dodatkowe przykładowe zapytania.

#### <a name="sample-review-inbound-connections"></a>Przykład: przeglądanie połączeń przychodzących

Przejrzyj połączenia przychodzące dla zestawu maszyn wirtualnych.

- Rekordy w tabeli dla metryk połączeń (VMConnection) nie reprezentują poszczególnych fizycznych połączeń sieciowych.
- Wiele połączeń sieci fizycznych jest zgrupowanych w połączenie logiczne.
- [Dowiedz się więcej](../azure-monitor/vm/service-map.md#connections) o tym, jak dane połączenia sieci fizycznej są agregowane w VMConnection.

```
// the machines of interest
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

Ten przykład zawiera podsumowanie ilości danych wysłanych i odebranych w połączeniach przychodzących między zestawem maszyn.

```
// the machines of interest
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