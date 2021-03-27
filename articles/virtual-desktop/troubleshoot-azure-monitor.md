---
title: Rozwiązywanie problemów z monitorem Windows Virtual Desktop Preview — Azure
description: Jak rozwiązywać problemy z Azure Monitor dla pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a07d7536d3d71b121c1dde761d8c290b8be01fe7
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628462"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Rozwiązywanie problemów z Azure Monitor dla pulpitu wirtualnego systemu Windows (wersja zapoznawcza)

>[!IMPORTANT]
>Azure Monitor dla pulpitu wirtualnego systemu Windows jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule przedstawiono znane problemy i rozwiązania typowych problemów występujących w Azure Monitor dla pulpitu wirtualnego systemu Windows (wersja zapoznawcza).

## <a name="issues-with-configuration-and-setup"></a>Problemy związane z konfiguracją i instalacją

Jeśli skoroszyt konfiguracji nie działa prawidłowo w celu zautomatyzowania instalacji, możesz użyć tych zasobów, aby ręcznie skonfigurować środowisko:

- Aby ręcznie włączyć diagnostykę lub uzyskać dostęp do obszaru roboczego Log Analytics, zobacz [wysyłanie diagnostyki pulpitu wirtualnego systemu Windows do log Analytics](diagnostics-log-analytics.md).
- Aby ręcznie zainstalować rozszerzenie Log Analytics na hoście sesji, zobacz [log Analytics rozszerzenie maszyny wirtualnej dla systemu Windows](../virtual-machines/extensions/oms-windows.md).
- Aby skonfigurować nowy obszar roboczy Log Analytics, zobacz [tworzenie log Analytics obszaru roboczego w Azure Portal](../azure-monitor/logs/quick-create-workspace.md).
- Aby dodać, usunąć lub edytować liczniki wydajności, zobacz [Konfigurowanie liczników wydajności](../azure-monitor/agents/data-sources-performance-counters.md).
- Aby skonfigurować dzienniki zdarzeń systemu Windows dla obszaru roboczego Log Analytics, zobacz [zbieranie źródeł danych dziennika zdarzeń systemu Windows z agentem log Analytics](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="my-data-isnt-displaying-properly"></a>Moje dane nie są prawidłowo wyświetlane

Jeśli dane nie są wyświetlane prawidłowo, należy zapoznać się z następującymi typowymi rozwiązaniami:

- Najpierw upewnij się, że poprawnie skonfigurowano skoroszyt konfiguracji, zgodnie z opisem w temacie [używanie Azure monitor dla pulpitu wirtualnego systemu Windows do monitorowania wdrożenia](azure-monitor.md). Jeśli nie ma żadnych liczników lub zdarzeń, dane skojarzone z nimi nie będą wyświetlane w Azure Portal.
- Sprawdź uprawnienia dostępu & skontaktować się z właścicielami zasobów, aby zażądać brakujących uprawnień; Każda osoba monitorująca pulpit wirtualny systemu Windows wymaga następujących uprawnień:
    - Dostęp do odczytu do subskrypcji platformy Azure, w których przechowywane są zasoby pulpitu wirtualnego systemu Windows
    - Dostęp do odczytu do grup zasobów subskrypcji, w których znajdują się hosty sesji usług pulpitu wirtualnego systemu Windows 
    - Dostęp do odczytu do doLog Analyticsych obszarów roboczych, których używasz
- Może być konieczne otwarcie portów wychodzących w zaporze serwera, aby umożliwić Azure Monitor i Log Analytics wysyłanie danych do portalu. Aby dowiedzieć się, jak to zrobić, zobacz następujące artykuły:
      - [Azure Monitor portów wychodzących](../azure-monitor/app/ip-addresses.md)
      - [Log Analytics wymagania dotyczące zapory](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements). 
- Nie widzisz danych z ostatniej aktywności? Możesz poczekać 15 minut i odświeżyć źródło danych. Azure Monitor zawiera 15-minutowy okres opóźnienia do wypełniania danych dziennika. Aby dowiedzieć się więcej, zobacz czas pozyskiwania [danych dziennika w Azure monitor](../azure-monitor/logs/data-ingestion-time.md).

Jeśli nie masz żadnych informacji, ale dane nadal nie są wyświetlane prawidłowo, może wystąpić problem z kwerendą lub źródłami danych. Przejrzyj [znane problemy i ograniczenia](#known-issues-and-limitations). 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Chcę dostosować Azure Monitor dla pulpitu wirtualnego systemu Windows

Azure Monitor dla pulpitu wirtualnego systemu Windows używa Azure Monitor skoroszytów. Skoroszyty umożliwiają zapisywanie kopii szablonu skoroszytu pulpitu wirtualnego systemu Windows i tworzenie własnych dostosowań.

Zgodnie z projektem niestandardowe szablony skoroszytów nie będą automatycznie przyjmować aktualizacji z grupy produkty. Aby uzyskać więcej informacji, zobacz temat [Rozwiązywanie problemów opartych na skoroszycie](../azure-monitor/insights/troubleshoot-workbooks.md) i [Omówienie skoroszytów](../azure-monitor/visualize/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Nie można zinterpretować danych

Dowiedz się więcej na temat terminów dotyczących danych na [Azure monitor słowniku pulpitów wirtualnych systemu Windows](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>Potrzebne dane nie są dostępne

Jeśli chcesz monitorować więcej liczników wydajności lub dzienników zdarzeń systemu Windows, możesz umożliwić im wysyłanie informacji diagnostycznych do obszaru roboczego Log Analytics i monitorowanie ich w **diagnostyce hosta: przeglądarka hosta**. 

- Aby dodać liczniki wydajności, zobacz [Konfigurowanie liczników wydajności](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters)
- Aby dodać zdarzenia systemu Windows, zobacz [Konfigurowanie dzienników zdarzeń systemu Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)

Nie można znaleźć punktu danych ułatwiającego zdiagnozowanie problemu? Wyślij nam opinię!

- Aby dowiedzieć się, jak opuścić opinię, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczną dla pulpitu wirtualnego systemu Windows](troubleshoot-set-up-overview.md).
- Możesz również wystawić opinię na temat pulpitu wirtualnego systemu Windows w [centrum opinii pulpitu wirtualnego systemu Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia

Poniżej znajdują się problemy i ograniczenia, które są świadome i w trakcie naprawy:

- Można monitorować tylko jedną pulę hostów jednocześnie. 
- Aby zapisać ulubione ustawienia, musisz zapisać niestandardowy szablon skoroszytu. Szablony niestandardowe nie będą automatycznie przyjmować aktualizacji z grupy produktów.
- W przypadku ładowania wybranych opcji skoroszyt konfiguracji czasami pokaże błędy "niepowodzenie zapytania". Odśwież zapytanie, w razie konieczności ponownie wprowadź swój wybór i błąd powinien zostać rozwiązany. 
- Niektóre komunikaty o błędach nie są wyświetlane w sposób przyjazny dla użytkownika, a nie wszystkie komunikaty o błędach są opisane w dokumentacji.
- Licznik wydajności całkowita liczba sesji może nadmiernie przekroczyć liczbę sesji według niewielkiej liczby, a całkowita liczba sesji może pozornie przekroczyć limit maksymalnej liczby sesji.
- Liczba dostępnych sesji nie odzwierciedla zasad skalowania w puli hostów.   
- Czy są wyświetlane sprzeczne lub nieoczekiwane czasy połączenia? Chociaż rzadko istnieje zdarzenie zakończenia połączenia, które może mieć wpływ na niektóre wizualizacje i metryki.
- Czas połączenia obejmuje czas, w którym użytkownicy wprowadzają swoje poświadczenia; jest to skorelowane ze środowiska, ale w niektórych przypadkach może pokazać fałszywe szczyty. 
    

## <a name="next-steps"></a>Następne kroki

Jeśli nie masz pewności, jak interpretować dane lub chcesz dowiedzieć się więcej na temat typowych warunków, zapoznaj się z tematem [słownik Azure monitor dla systemu Windows Virtual Desktop](azure-monitor-glossary.md). Jeśli chcesz dowiedzieć się, jak skonfigurować i używać Azure Monitor dla pulpitu wirtualnego systemu Windows, zobacz [używanie Azure monitor dla pulpitu wirtualnego systemu Windows do monitorowania wdrożenia](azure-monitor.md).
