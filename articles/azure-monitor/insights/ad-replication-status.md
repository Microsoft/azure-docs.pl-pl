---
title: Monitorowanie stanu replikacji Active Directory
description: Pakiet rozwiązań Active Directory Replication Status regularnie monitoruje Środowisko Active Directory pod kątem błędów replikacji.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/24/2018
ms.openlocfilehash: 01d641801b1b9a0cfaa4fd7ee11e3c55314dc53c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577524"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Monitoruj Active Directory stan replikacji za pomocą Azure Monitor

![Symbol AD Replication Status](./media/ad-replication-status/ad-replication-status-symbol.png)

Active Directory to kluczowy składnik środowiska IT przedsiębiorstwa. Aby zapewnić wysoką dostępność i wysoką wydajność, każdy kontroler domeny ma własną kopię bazy danych Active Directory. Kontrolery domeny są replikowane ze sobą w celu propagowania zmian w całym przedsiębiorstwie. Awarie w tym procesie replikacji mogą spowodować różne problemy w całym przedsiębiorstwie.

Rozwiązanie AD Replication Status regularnie monitoruje Środowisko Active Directory pod kątem błędów replikacji.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie.

### <a name="prerequisites"></a>Wymagania wstępne

* Rozwiązanie AD Replication Status wymaga zainstalowanej zainstalowanej wersji programu .NET Framework 4.6.2 lub nowszej na każdym komputerze, na którym jest zainstalowany agent programu Log Analytics dla systemu Windows (określany również jako Microsoft Monitoring Agent (MMA)).  Agent jest używany przez program System Center 2016 — Operations Manager, Operations Manager 2012 R2 i Azure Monitor.
* Rozwiązanie obsługuje kontrolery domeny z systemami Windows Server 2008 i 2008 R2, Windows Server 2012 i 2012 R2 oraz Windows Server 2016.
* Log Analytics obszar roboczy, aby dodać rozwiązanie Active Directory Sprawdzanie kondycji z witryny Azure Marketplace w Azure Portal. Nie jest wymagana żadna dodatkowa konfiguracja.


### <a name="install-agents-on-domain-controllers"></a>Zainstaluj agentów na kontrolerach domeny
Należy zainstalować agentów na kontrolerach domeny należących do domeny, które mają zostać ocenione. Lub należy zainstalować agentów na serwerach członkowskich i skonfigurować agentów do wysyłania danych replikacji usługi AD do Azure Monitor. Aby dowiedzieć się, jak podłączyć komputery z systemem Windows do Azure Monitor, zobacz [łączenie komputerów z systemem Windows, aby Azure monitor](../agents/agent-windows.md). Jeśli kontroler domeny jest już częścią istniejącego środowiska System Center Operations Manager, z którym chcesz nawiązać połączenie Azure Monitor, zobacz [connect Operations Manager to Azure monitor](../agents/om-agents.md).

### <a name="enable-non-domain-controller"></a>Włącz kontroler niebędący domeną
Jeśli nie chcesz połączyć żadnego z kontrolerów domeny bezpośrednio z Azure Monitor, możesz użyć dowolnego innego komputera w domenie połączonej z Azure Monitor, aby zebrać dane dla pakietu rozwiązania AD Replication Status i wysłać dane.

1. Sprawdź, czy komputer jest członkiem domeny, która ma być monitorowana przy użyciu rozwiązania AD Replication Status.
2. [Połącz komputer z systemem Windows, aby Azure monitor](../agents/om-agents.md) lub [połączyć go przy użyciu istniejącego środowiska Operations Manager do Azure monitor](../agents/om-agents.md), jeśli nie jest jeszcze podłączony.
3. Na tym komputerze Ustaw następujący klucz rejestru:<br>Klucz: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName> \Solutions\ADReplication**<br>Wartość: **isTarget**<br>Dane wartości: **prawda**

   > [!NOTE]
   > Zmiany te zaczną obowiązywać dopiero po ponownym uruchomieniu usługi Microsoft Monitoring Agent (HealthService.exe).
   > ### <a name="install-solution"></a>Zainstaluj rozwiązanie
   > Postępuj zgodnie z procesem opisanym w artykule [Instalowanie rozwiązania monitorowania](solutions.md#install-a-monitoring-solution) , aby dodać rozwiązanie **Active Directory Replication status** do obszaru roboczego log Analytics. Nie są wymagane żadne dalsze czynności konfiguracyjne.


## <a name="ad-replication-status-data-collection-details"></a>Szczegóły zbierania danych AD Replication Status
W poniższej tabeli przedstawiono metody zbierania danych oraz inne szczegóły dotyczące sposobu zbierania danych dla AD Replication Status.

| platforma | Agent bezpośredni | Agent SCOM | Azure Storage | Czy SCOM jest wymagany? | Dane agenta SCOM wysyłane przez grupę zarządzania | częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |co pięć dni |



## <a name="understanding-replication-errors"></a>Omówienie błędów replikacji

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Kafelek AD Replication Status Wyświetla liczbę aktualnie występujących błędów replikacji. **Błędy replikacji krytycznej** to błędy, które są co najmniej 75% [okresu istnienia reliktu](/previous-versions/windows/it-pro/windows-server-2003/cc784932(v=ws.10)) dla lasu Active Directory.

![Kafelek AD Replication Status](./media/ad-replication-status/oms-ad-replication-tile.png)

Po kliknięciu kafelka można wyświetlić więcej informacji o błędach.
![Pulpit nawigacyjny AD Replication Status](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Stan serwera docelowego i stanu serwera źródłowego
Te kolumny zawierają stan serwerów docelowych i serwerów źródłowych, na których występują błędy replikacji. Liczba po każdej nazwie kontrolera domeny wskazuje liczbę błędów replikacji na tym kontrolerze domeny.

Błędy zarówno dla serwerów docelowych, jak i serwerów źródłowych są wyświetlane, ponieważ niektóre problemy są łatwiejsze w rozwiązywaniu problemów z perspektywy serwera źródłowego i innych z perspektywy serwera docelowego.

W tym przykładzie można zobaczyć, że wiele serwerów docelowych ma w pełni taką samą liczbę błędów, ale istnieje jeden serwer źródłowy (ADDC35), który zawiera wiele błędów niż wszystkie pozostałe. Prawdopodobnie wystąpił problem z ADDC35, który powoduje, że wysyłanie danych do partnerów replikacji nie powiedzie się. Rozwiązanie problemów z ADDC35 może rozwiązać wiele błędów, które pojawiają się w obszarze serwera docelowego.

### <a name="replication-error-types"></a>Typy błędów replikacji
Ten obszar zawiera informacje o typach błędów wykrytych w całym przedsiębiorstwie. Każdy błąd ma unikatowy kod liczbowy i komunikat, który może pomóc w ustaleniu głównej przyczyny błędu.

Pierścień u góry daje pomysł, które błędy pojawiają się w danym środowisku więcej i rzadziej.

Pokazuje, kiedy wiele kontrolerów domeny ma ten sam błąd replikacji. W takim przypadku można odnajdywać lub identyfikować rozwiązanie na jednym kontrolerze domeny, a następnie powtarzać je na innych kontrolerach domeny, których dotyczy ten sam błąd.

### <a name="tombstone-lifetime"></a>Okres istnienia reliktu
Okres istnienia reliktu określa, jak długo usunięty obiekt (nazywany reliktem) jest przechowywany w bazie danych Active Directory. Gdy usunięty obiekt przeszedł okres istnienia reliktu, proces wyrzucania elementów bezużytecznych automatycznie usunie go z bazy danych Active Directory.

Domyślny okres istnienia reliktu to 180 dni dla najnowszych wersji systemu Windows, ale jego 60 dni we wcześniejszych wersjach i mogą zostać zmienione jawnie przez administratora Active Directory.

Ważne jest, aby wiedzieć, czy występują błędy replikacji, które zbliżają się lub przeszły okres istnienia reliktu. Jeśli dwa kontrolery domeny napotykają błąd replikacji, który będzie trwał okres istnienia reliktu, replikacja zostanie wyłączona między tymi dwoma kontrolerami domeny, nawet jeśli wystąpi błąd replikacji podstawowej.

Obszar istnienia reliktu ułatwia zidentyfikowanie miejsc, w których replikacja wyłączona jest niebezpieczeństwo. Każdy błąd w kategorii **ponad 100% TSL** reprezentuje partycję, która nie została zreplikowana między serwerem źródłowym i docelowym przez co najmniej okres istnienia reliktu dla lasu.

W takiej sytuacji po prostu naprawienie błędu replikacji nie będzie wystarczające. Należy ręcznie zbadać, aby identyfikować i czyścić obiekty pokutujące przed ponownym uruchomieniem replikacji. Może nawet być konieczne zlikwidowanie kontrolera domeny.

Oprócz identyfikowania błędów replikacji trwałych przed okresem istnienia reliktu warto również zwrócić uwagę na wszystkie błędy w kategoriach **50-75% TSL** lub **75-100% TSL** .

Są to błędy, które są wyraźnie pokutujące, nie przejściowo, dzięki czemu mogą one potrzebować interwencji do rozwiązania. Dobrymi wiadomościami jest to, że nie osiągnąli jeszcze okresu istnienia reliktu. Jeśli te problemy zostaną rozwiązane natychmiast i *przed* osiągnięciem okresu istnienia reliktu, replikacja może zostać ponownie uruchomiona z minimalnym interwencją ręczną.

Jak wspomniano wcześniej, kafelek pulpitu nawigacyjnego dla AD Replication Status rozwiązania pokazuje liczbę *krytycznych* błędów replikacji w danym środowisku, które są zdefiniowane jako błędy przekraczające 75% okresu istnienia reliktu (w tym błędy przekraczające 100% TSL). Staraj się zachować tę liczbę na 0.

> [!NOTE]
> Wszystkie obliczenia procentowe okresu istnienia reliktu są oparte na rzeczywistym okresie istnienia dla lasu Active Directory, dzięki czemu można ufać, że te wartości procentowe są dokładne, nawet jeśli masz ustawioną wartość okresu istnienia niestandardowego reliktu.
>
>

### <a name="ad-replication-status-details"></a>Szczegóły stanu replikacji usługi AD
Po kliknięciu dowolnego elementu na jednej z list zostaną wyświetlone dodatkowe szczegółowe informacje na jego temat za pomocą zapytania dziennika. Wyniki są filtrowane w celu wyświetlenia tylko błędów związanych z tym elementem. Jeśli na przykład klikniesz pierwszy kontroler domeny wymieniony w obszarze **stan serwera docelowego (ADDC02)**, zobaczysz wyniki zapytania filtrowane w celu wyświetlenia błędów z kontrolerem domeny wymienionym jako serwer docelowy:

![Błędy stanu replikacji usługi AD w wynikach zapytania](./media/ad-replication-status/oms-ad-replication-search-details.png)

W tym miejscu możesz filtrować dalej, modyfikować zapytanie dziennika i tak dalej. Aby uzyskać więcej informacji o używaniu zapytań dzienników w Azure Monitor, zobacz [Analizowanie danych dziennika w Azure monitor](../logs/log-query-overview.md).

Pole **HelpLink** zawiera adres URL strony TechNet z dodatkowymi szczegółami dotyczącymi tego konkretnego błędu. Możesz skopiować i wkleić ten link do okna przeglądarki, aby wyświetlić informacje dotyczące rozwiązywania problemów i naprawiania błędu.

Możesz również kliknąć pozycję **Eksportuj** , aby wyeksportować wyniki do programu Excel. Eksportowanie danych może ułatwić wizualizację danych o błędach replikacji w dowolny sposób.

![wyeksportowano błędy stanu replikacji usługi AD w programie Excel](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>AD Replication Status często zadawane pytania
**P: jak często są aktualizowane dane stanu replikacji usługi AD?**
Odp.: informacje są aktualizowane co pięć dni.

**P: czy istnieje sposób skonfigurowania, jak często te dane są aktualizowane?**
Odp.: w tej chwili nie.

**P: Czy muszę dodać wszystkie kontrolery domeny do mojego obszaru roboczego Log Analytics, aby zobaczyć stan replikacji?**
Odp.: nie, należy dodać tylko jeden kontroler domeny. Jeśli masz wiele kontrolerów domeny w obszarze roboczym Log Analytics, dane ze wszystkich tych elementów są wysyłane do Azure Monitor.

**P: nie chcę dodawać żadnych kontrolerów domeny do obszaru roboczego Log Analytics. Czy nadal mogę używać rozwiązania AD Replication Status?**

Odp. Tak. Możesz ustawić wartość klucza rejestru, aby go włączyć. Zobacz [Włącz kontroler nie należący do domeny](#enable-non-domain-controller).

**P: Jaka jest nazwa procesu, który wykonuje zbieranie danych?**
ODP.: AdvisorAssessment.exe

**P: jak długo trwa zbieranie danych?**
Odp.: czas zbierania danych zależy od rozmiaru środowiska Active Directory, ale zazwyczaj trwa mniej niż 15 minut.

**P: jakiego typu dane są zbierane?**
Odp.: informacje o replikacji są zbierane za pośrednictwem protokołu LDAP.

**P: czy istnieje sposób skonfigurowania zbierania danych?**
Odp.: w tej chwili nie.

**P: Jakie uprawnienia muszę zbierać dane?**
Odp.: normalne uprawnienia użytkownika do Active Directory są wystarczające.

## <a name="troubleshoot-data-collection-problems"></a>Rozwiązywanie problemów z zbieraniem danych
Aby można było zbierać dane, pakiet rozwiązań AD Replication Status wymaga, aby co najmniej jeden kontroler domeny mógł zostać połączony z obszarem roboczym Log Analytics. Dopóki nie zostanie nawiązane połączenie z kontrolerem domeny, zostanie wyświetlony komunikat informujący o tym, że **dane są nadal zbierane**.

Jeśli potrzebujesz pomocy przy łączeniu jednego z kontrolerów domeny, możesz wyświetlić dokumentację w obszarze [łączenie komputerów z systemem Windows, aby Azure monitor](../agents/om-agents.md). Alternatywnie, Jeśli kontroler domeny jest już połączony z istniejącym środowiskiem System Center Operations Manager, można wyświetlić dokumentację w [System Center Operations Manager Connect, aby Azure monitor](../agents/om-agents.md).

Jeśli nie chcesz połączyć żadnego z kontrolerów domeny bezpośrednio do Azure Monitor lub System Center Operations Manager, zobacz [Włącz kontroler nie należący do domeny](#enable-non-domain-controller).

## <a name="next-steps"></a>Następne kroki
* Użyj [zapytań dzienników w Azure monitor](../logs/log-query-overview.md) , aby wyświetlić szczegółowe Active Directory dane stanu replikacji.

