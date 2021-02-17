---
title: Rozwiązywanie problemów z monitorem Windows Virtual Desktop Preview — Azure
description: Jak rozwiązywać problemy z Azure Monitor dla pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1453d43707a3680380e553fe69d738b27fe5ae3a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571357"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Rozwiązywanie problemów z Azure Monitor dla pulpitu wirtualnego systemu Windows (wersja zapoznawcza)

>[!IMPORTANT]
>Azure Monitor dla pulpitu wirtualnego systemu Windows jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule przedstawiono znane problemy i rozwiązania typowych problemów występujących w Azure Monitor dla pulpitu wirtualnego systemu Windows (wersja zapoznawcza).

## <a name="issues-with-configuration-and-setup"></a>Problemy związane z konfiguracją i instalacją

Jeśli skoroszyt konfiguracji nie działa prawidłowo w celu zautomatyzowania instalacji, możesz użyć tych zasobów, aby ręcznie skonfigurować środowisko:

- Aby ręcznie włączyć diagnostykę lub uzyskać dostęp do obszaru roboczego Log Analytics, zobacz [wysyłanie diagnostyki pulpitu wirtualnego systemu Windows do log Analytics](diagnostics-log-analytics.md).
- Aby ręcznie zainstalować rozszerzenie Log Analytics na hoście, zobacz [log Analytics rozszerzenie maszyny wirtualnej dla systemu Windows](../virtual-machines/extensions/oms-windows.md).
- Aby skonfigurować nowy obszar roboczy Log Analytics, zobacz [tworzenie log Analytics obszaru roboczego w Azure Portal](../azure-monitor/logs/quick-create-workspace.md).
- Aby dodać lub usunąć liczniki wydajności, zobacz [Konfigurowanie liczników wydajności](../azure-monitor/agents/data-sources-performance-counters.md).
- Aby skonfigurować zdarzenia dla obszaru roboczego Log Analytics, zobacz [zbieranie źródeł danych dziennika zdarzeń systemu Windows z agentem log Analytics](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="my-data-isnt-displaying-properly"></a>Moje dane nie są prawidłowo wyświetlane

Jeśli dane nie są wyświetlane prawidłowo, sprawdź konfigurację, uprawnienia i sprawdź, czy wymagane adresy IP zostały odblokowane. 

- Najpierw upewnij się, że wypełniono wszystkie pola w skoroszycie konfiguracji, zgodnie z opisem w temacie [używanie Azure monitor dla pulpitu wirtualnego systemu Windows do monitorowania wdrożenia](azure-monitor.md). Jeśli nie ma żadnych liczników lub zdarzeń, dane skojarzone z nimi nie będą wyświetlane w Azure Portal.

- Sprawdź uprawnienia dostępu & skontaktować się z właścicielami zasobów, aby zażądać brakujących uprawnień; Każda osoba monitorująca pulpit wirtualny systemu Windows wymaga następujących uprawnień:

    - Dostęp do odczytu do subskrypcji platformy Azure, w których przechowywane są zasoby pulpitu wirtualnego systemu Windows
    - Dostęp do odczytu do grup zasobów subskrypcji, w których znajdują się hosty sesji usług pulpitu wirtualnego systemu Windows 
    - Dostęp do odczytu do obszaru roboczego Log Analytics

- Może być konieczne otwarcie portów wychodzących w zaporze serwera, aby umożliwić Azure Monitor wysyłanie danych do portalu, zobacz [porty wychodzące](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses). 

- Nie widzisz danych z ostatniej aktywności? Możesz poczekać 15 minut i odświeżyć źródło danych. Azure Monitor zawiera 15-minutowy okres opóźnienia do wypełniania danych dziennika. Aby dowiedzieć się więcej, zobacz czas pozyskiwania [danych dziennika w Azure monitor](../azure-monitor/logs/data-ingestion-time.md).

Jeśli nie masz żadnych informacji, ale dane nadal nie są wyświetlane prawidłowo, może wystąpić problem z kwerendą lub źródłami danych. Przejrzyj nasze znane problemy i ograniczenia. 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Chcę dostosować Azure Monitor dla pulpitu wirtualnego systemu Windows

Azure Monitor dla pulpitu wirtualnego systemu Windows używa Azure Monitor skoroszytów. Skoroszyty umożliwiają zapisywanie kopii szablonu skoroszytu pulpitu wirtualnego systemu Windows i tworzenie własnych dostosowań.

Zgodnie z projektem niestandardowe szablony skoroszytów nie będą automatycznie przyjmować aktualizacji z grupy produkty. Aby uzyskać więcej informacji, zobacz temat [Rozwiązywanie problemów opartych na skoroszycie](../azure-monitor/insights/troubleshoot-workbooks.md) i [Omówienie skoroszytów](../azure-monitor/visualize/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Nie można zinterpretować danych

Dowiedz się więcej na temat terminów dotyczących danych na [Azure monitor słowniku pulpitów wirtualnych systemu Windows](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>Potrzebne dane nie są dostępne

Jeśli chcesz monitorować więcej liczników wydajności lub zdarzeń, możesz umożliwić im wysyłanie danych do obszaru roboczego Log Analytics i monitorowanie ich w diagnostyce hosta: przeglądarka hosta. 

- Aby dodać liczniki wydajności, zobacz [Konfigurowanie liczników wydajności](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#configuring-performance-counters)
- Aby dodać zdarzenia systemu Windows, zobacz [Konfigurowanie dzienników zdarzeń systemu Windows](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events#configuring-windows-event-logs)

Nie można znaleźć punktu danych ułatwiającego zdiagnozowanie problemu? Wyślij nam opinię!

- Aby dowiedzieć się, jak opuścić opinię, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczną dla pulpitu wirtualnego systemu Windows](troubleshoot-set-up-overview.md).
- Możesz również wystawić opinię na temat pulpitu wirtualnego systemu Windows w [centrum opinii na pulpicie wirtualnym systemu Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) lub [naszym forum UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia

Są to problemy i ograniczenia, które obecnie są świadome i działające w celu naprawy:

- Można monitorować tylko jedną pulę hostów jednocześnie. 

- Aby zapisać ulubione ustawienia, musisz zapisać niestandardowy szablon skoroszytu. Szablony niestandardowe nie będą automatycznie przyjmować aktualizacji z grupy produktów.

- Niektóre komunikaty o błędach nie są oznaczane w sposób przyjazny dla użytkownika, a nie wszystkie komunikaty o błędach są opisane w dokumentacji.

- Licznik wydajności całkowita liczba sesji może nadmiernie przekroczyć liczbę sesji według niewielkiej liczby, a całkowita liczba sesji może pozornie przekroczyć limit maksymalnej liczby sesji.

- Liczba dostępnych sesji nie odzwierciedla zasad skalowania w puli hostów. 
    
- Sporadycznie może istnieć zdarzenie zakończenia połączenia, które może mieć wpływ na niektóre wizualizacje, takie jak połączenia w czasie i stan połączenia użytkownika.  
    
- Skoroszyt konfiguracji obsługuje tylko Konfigurowanie hostów w tym samym regionie, w których znajduje się grupa zasobów. 

- Czas połączenia obejmuje czas, w którym użytkownicy wprowadzają swoje poświadczenia; jest to skorelowane ze środowiska, ale w niektórych przypadkach może pokazać fałszywe szczyty. 
    

## <a name="next-steps"></a>Następne kroki

Jeśli nie masz pewności, jak interpretować dane lub chcesz dowiedzieć się więcej na temat typowych warunków, zapoznaj się z tematem [słownik Azure monitor dla systemu Windows Virtual Desktop](azure-monitor-glossary.md). Jeśli chcesz dowiedzieć się, jak skonfigurować i używać Azure Monitor dla pulpitu wirtualnego systemu Windows, zobacz [używanie Azure monitor dla pulpitu wirtualnego systemu Windows do monitorowania wdrożenia](azure-monitor.md).