---
title: Konfigurowanie analizy zależności opartej na agentach w programie Azure Migrate
description: W tym artykule opisano sposób skonfigurowania analizy zależności opartej na agentach w Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 6ded5d4ed8c2a55939bba908a05adbd2dea2ccbf
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714780"
---
# <a name="set-up-dependency-visualization"></a>Konfigurowanie wizualizacji zależności

W tym artykule opisano sposób skonfigurowania analizy zależności opartej na agentach w programie Azure Migrate: odnajdywanie i ocena. [Analiza zależności pomaga](concepts-dependency-visualization.md) identyfikować i identyfikować zależności między serwerami, które chcesz ocenić i zmigrować na platformę Azure.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Zapoznaj się z wymaganiami w zakresie obsługi i wdrażania dla analizy zależności opartej na agentach dla:
    - [Serwery w środowisku VMware](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Serwery fizyczne](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Serwery w środowisku funkcji Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- Upewnij się, że:
    - Mieć Azure Migrate projektu. Jeśli nie, utwórz [go](./create-manage-projects.md) teraz.
    - Sprawdź, czy dodano [do](how-to-assess.md) projektu Azure Migrate: odnajdywanie i ocenę.
    - Skonfiguruj urządzenie Azure Migrate [w celu](migrate-appliance.md) odnajdywania serwerów lokalnych. Urządzenie odnajduje serwery lokalne i wysyła metadane i dane dotyczące wydajności do usługi Azure Migrate: odnajdywanie i ocena. Skonfiguruj urządzenie dla:
        - [Serwery w środowisku VMware](how-to-set-up-appliance-vmware.md)
        - [Serwery w środowisku funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md)
        - [Serwery fizyczne](how-to-set-up-appliance-physical.md)
- Aby użyć wizualizacji zależności, należy skojarzyć obszar roboczy usługi [Log Analytics](../azure-monitor/logs/manage-access.md) z Azure Migrate projektu:
    - Obszar roboczy można dołączyć tylko po skonfigurowaniu Azure Migrate i odnajdywaniu serwerów w Azure Migrate projektu.
    - Upewnij się, że masz obszar roboczy w subskrypcji, który zawiera Azure Migrate projektu.
    - Obszar roboczy musi znajdować się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia lub Europa Zachodnia. Obszarów roboczych w innych regionach nie można skojarzyć z projektem.
    - Obszar roboczy musi znajdować się w regionie, w którym Service Map [jest obsługiwana.](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)
    - Nowy lub istniejący obszar roboczy usługi Log Analytics można skojarzyć z Azure Migrate projektu.
    - Obszar roboczy jest dołączany podczas pierwszej konfiguracji wizualizacji zależności dla serwera. Po dodaniu Azure Migrate nie można zmodyfikować obszaru roboczego dla projektu aplikacji.
    - W u usługi Log Analytics obszar roboczy skojarzony z Azure Migrate jest otagowany kluczem projektu migracji i nazwą projektu.

## <a name="associate-a-workspace"></a>Kojarzenie obszaru roboczego

1. Po odnalezieniu serwerów do oceny w tece Serwery  >  **Azure Migrate: odnajdywanie i ocena** kliknij pozycję **Przegląd.**  
2. W **Azure Migrate: Odnajdywanie i ocena** kliknij pozycję **Podstawy.**
3. W **obszarze roboczym OMS** kliknij pozycję **Wymaga konfiguracji.**

     ![Konfigurowanie obszaru roboczego usługi Log Analytics](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. W **obszarze Konfigurowanie obszaru roboczego OMS** określ, czy chcesz utworzyć nowy obszar roboczy, czy użyć istniejącego.
    - Możesz wybrać istniejący obszar roboczy ze wszystkich obszarów roboczych w subskrypcji projektu.
    - Aby go skojarzyć, musisz mieć dostęp czytelnika do obszaru roboczego.
5. Jeśli tworzysz nowy obszar roboczy, wybierz dla niego lokalizację.

    ![Dodawanie nowego obszaru roboczego](./media/how-to-create-group-machine-dependencies/workspace.png)

> [!Note]
> [Dowiedz się,](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security) jak skonfigurować obszar roboczy OMS dla łączności z prywatnym punktem końcowym.  

## <a name="download-and-install-the-vm-agents"></a>Pobieranie i instalowanie agentów maszyny wirtualnej

Na każdym serwerze, który chcesz przeanalizować, zainstaluj agentów.

> [!NOTE]
> W przypadku serwerów System Center Operations Manager 2012 R2 lub nowszych nie trzeba instalować agenta MMA. Service Map integruje się z Operations Manager. [Postępuj zgodnie ze](../azure-monitor/vm/service-map-scom.md#prerequisites) wskazówkami w zakresie integracji.

1. W **Azure Migrate: Odnajdywanie i ocena** kliknij pozycję **Odnalezione serwery.**
2. Dla każdego serwera, który chcesz przeanalizować za pomocą wizualizacji zależności, w kolumnie **Zależności** kliknij pozycję **Wymaga instalacji agenta**.
3. Na **stronie Zależności** pobierz agenta MMA i agenta zależności dla systemu Windows lub Linux.
4. W **obszarze Konfigurowanie agenta MMA** skopiuj identyfikator i klucz obszaru roboczego. Są one potrzebne podczas instalowania agenta MMA.

    ![Instalowanie agentów](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Instalowanie programu MMA

Zainstaluj program MMA na każdym serwerze z systemem Windows lub Linux, który chcesz przeanalizować.

### <a name="install-mma-on-a-windows-server"></a>Instalowanie programu MMA na serwerze z systemem Windows

Aby zainstalować agenta na serwerze z systemem Windows:

1. Kliknij dwukrotnie pobranego agenta.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**. Na stronie **Postanowienia licencyjne** kliknij pozycję **Zgadzam się,** aby zaakceptować licencję.
3. W **folderze docelowym** zachowaj lub zmodyfikuj domyślny folder instalacji > **Dalej.**
4. W **witrynie Opcje instalacji agenta** wybierz pozycję Azure Log **Analytics**  >  **Dalej.**
5. Kliknij **przycisk Dodaj,** aby dodać nowy obszar roboczy usługi Log Analytics. Wklej identyfikator i klucz obszaru roboczego skopiowane z portalu. Kliknij przycisk **Dalej**.

Agenta można zainstalować z wiersza polecenia lub przy użyciu zautomatyzowanej metody, takiej jak Menedżer konfiguracji [lub Intigua.](https://www.intigua.com/intigua-for-azure-migration)
- [Dowiedz się więcej](../azure-monitor/agents/log-analytics-agent.md#installation-options) na temat używania tych metod do instalowania agenta MMA.
- Agenta programu MMA można również zainstalować za pomocą [tego](https://github.com/brianbar-MSFT/Install-MMA) skryptu.
- [Dowiedz się więcej](../azure-monitor/agents/agents-overview.md#supported-operating-systems) o systemach operacyjnych Windows obsługiwanych przez program MMA.

### <a name="install-mma-on-a-linux-server"></a>Instalowanie programu MMA na serwerze z systemem Linux

Aby zainstalować program MMA na serwerze z systemem Linux:

1. Przenieś odpowiedni pakiet (x86 lub x64) na komputer z systemem Linux przy użyciu narzędzia scp/sftp.
2. Zainstaluj pakiet przy użyciu argumentu --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Dowiedz się więcej](../azure-monitor/agents/agents-overview.md#supported-operating-systems) o liście systemów operacyjnych Linux przez mma. 

## <a name="install-the-dependency-agent"></a>Instalowanie agenta zależności

1. Aby zainstalować agenta zależności na serwerze z systemem Windows, kliknij dwukrotnie plik instalacyjny i wykonaj czynności kreatora.
2. Aby zainstalować agenta zależności na serwerze z systemem Linux, zainstaluj go jako główny przy użyciu następującego polecenia:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Dowiedz się więcej](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) na temat sposobu używania skryptów do instalowania agenta zależności.
- [Dowiedz się więcej](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) o systemach operacyjnych obsługiwanych przez agenta zależności.


## <a name="create-a-group-using-dependency-visualization"></a>Tworzenie grupy przy użyciu wizualizacji zależności

Teraz utwórz grupę do oceny. 


> [!NOTE]
> Grupy, dla których chcesz zwizualizować zależności, nie powinny zawierać więcej niż 10 serwerów. Jeśli masz więcej niż 10 serwerów, podziel je na mniejsze grupy.

1. W **Azure Migrate: Odnajdywanie i ocena** kliknij pozycję **Odnalezione serwery.**
2. W kolumnie **Zależności** kliknij pozycję **Wyświetl zależności dla** każdego serwera, który chcesz przejrzeć.
3. Na mapie zależności widać następujące elementy:
    - Przychodzące (klienci) i wychodzące (serwery) połączenia TCP do i z serwera.
    - Serwery zależne, które nie mają zainstalowanych agentów zależności, są pogrupowane według numerów portów.
    - Serwery zależne z zainstalowanymi agentami zależności są wyświetlane jako osobne pola.
    - Procesy działające na serwerze. Rozwiń każde pole serwera, aby wyświetlić procesy.
    - Właściwości serwera (w tym nazwa FQDN, system operacyjny, adres MAC). Kliknij każde pole serwera, aby wyświetlić szczegóły.

4. Zależności można przyjrzeć się różnym czasom trwania, klikając czas trwania w etykiecie zakresu czasu.
    - Domyślnie zakres to godzina. 
    - Można zmodyfikować zakres czasu lub określić daty rozpoczęcia i zakończenia oraz czas trwania.
    - Zakres czasu może być do godziny. Jeśli potrzebujesz dłuższego zakresu, użyj Azure Monitor do wykonywania zapytań o dane zależne przez dłuższy czas.

5. Po zidentyfikowaniu serwerów zależnych, które chcesz zgrupować, naciśnij klawisze Ctrl+kliknięcie, aby wybrać wiele serwerów na mapie, a następnie kliknij pozycję **Grupuj maszyny.**
6. Określ nazwę grupy.
7. Sprawdź, czy serwery zależne zostały odnalezione przez Azure Migrate.

    - Jeśli serwer zależny nie zostanie odnaleziony przez Azure Migrate: odnajdywanie i ocena, nie można dodać go do grupy.
    - Aby dodać serwer, uruchom odnajdywanie ponownie i sprawdź, czy serwer został odnaleziony.

8. Jeśli chcesz utworzyć ocenę dla tej grupy, zaznacz pole wyboru, aby utworzyć nową ocenę dla grupy.
8. Kliknij **przycisk OK,** aby zapisać grupę.

Po utworzeniu grupy zalecamy zainstalowanie agentów na wszystkich serwerach w grupie, a następnie zwizualizowanie zależności dla całej grupy.

## <a name="query-dependency-data-in-azure-monitor"></a>Wykonywanie zapytań dotyczących danych zależności w Azure Monitor

W obszarze roboczym usługi Log Analytics skojarzonym z projektem można Service Map danych zależności przechwyconych przez Azure Migrate zależności. Usługa Log Analytics służy do pisania i uruchamiania Azure Monitor zapytań dziennika.

- [Dowiedz się, jak](../azure-monitor/vm/service-map.md#log-analytics-records) wyszukiwać dane Service Map u usługi Log Analytics.
- [Omówienie pisania zapytań](../azure-monitor/logs/get-started-queries.md) dzienników w u [usługi Log Analytics.](../azure-monitor/logs/log-analytics-tutorial.md)

Uruchom zapytanie dotyczące danych zależności w następujący sposób:

1. Po zainstalowaniu agentów przejdź do portalu i kliknij pozycję **Przegląd.**
2. W **Azure Migrate: Odnajdywanie i ocena** kliknij pozycję **Przegląd.** Kliknij strzałkę w dół, aby **rozwinąć pozycję Essentials (Podstawowe elementy).**
3. W **obszarze roboczym OMS** kliknij nazwę obszaru roboczego.
3. Na stronie Obszar roboczy usługi Log Analytics > **Ogólne** kliknij pozycję **Dzienniki.**
4. Napisz zapytanie, a następnie kliknij pozycję **Uruchom**.

### <a name="sample-queries"></a>Przykładowe zapytania

Oto kilka przykładowych zapytań, których można użyć do wyodrębnienia danych zależności.

- Zapytania można modyfikować w celu wyodrębnienia preferowanych punktów danych.
- [Przejrzyj](../azure-monitor/vm/service-map.md#log-analytics-records) pełną listę rekordów danych zależności.
- [Przejrzyj](../azure-monitor/vm/service-map.md#sample-log-searches) dodatkowe przykładowe zapytania.

#### <a name="sample-review-inbound-connections"></a>Przykład: Przeglądanie połączeń przychodzących

Przejrzyj połączenia przychodzące dla zestawu serwerów.

- Rekordy w tabeli dotyczące metryk połączeń (VMConnection) nie reprezentują poszczególnych fizycznych połączeń sieciowych.
- Wiele połączeń sieci fizycznej jest grupowanych w połączenie logiczne.
- [Dowiedz się więcej](../azure-monitor/vm/service-map.md#connections) na temat sposobu agregowania danych połączenia sieci fizycznej w programie VMConnection.

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

#### <a name="sample-summarize-sent-and-received-data"></a>Przykład: podsumowuje wysłane i odebrane dane

Ten przykład podsumowuje ilość danych wysyłanych i odbieranych dla połączeń przychodzących między zestawem serwerów.

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
