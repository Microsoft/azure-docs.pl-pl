---
title: Rozwiązywanie problemów z monitorem Windows Virtual Desktop Preview — Azure
description: Jak rozwiązywać problemy z Azure Monitor dla pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91cf6729911cdb674c5451f172e76a2e9d5943e4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467673"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Rozwiązywanie problemów z Azure Monitor dla pulpitu wirtualnego systemu Windows (wersja zapoznawcza)

>[!IMPORTANT]
>Azure Monitor dla pulpitu wirtualnego systemu Windows jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule przedstawiono znane problemy i rozwiązania typowych problemów występujących w Azure Monitor dla pulpitu wirtualnego systemu Windows (wersja zapoznawcza).

## <a name="the-configuration-workbook-isnt-working-properly"></a>Skoroszyt konfiguracji nie działa prawidłowo

Jeśli skoroszyt konfiguracji Azure Monitor nie działa, możesz użyć tych zasobów do ręcznego skonfigurowania jego części:

- Aby ręcznie włączyć diagnostykę lub uzyskać dostęp do obszaru roboczego Log Analytics, zobacz [wysyłanie diagnostyki pulpitu wirtualnego systemu Windows do log Analytics](diagnostics-log-analytics.md).
- Aby ręcznie zainstalować rozszerzenie Log Analytics na hoście, zobacz [log Analytics rozszerzenie maszyny wirtualnej dla systemu Windows](../virtual-machines/extensions/oms-windows.md).
- Aby skonfigurować nowy obszar roboczy Log Analytics, zobacz [tworzenie log Analytics obszaru roboczego w Azure Portal](../azure-monitor/learn/quick-create-workspace.md).
- Aby dodać lub usunąć liczniki wydajności, zobacz [Konfigurowanie liczników wydajności](../azure-monitor/platform/data-sources-performance-counters.md).
- Aby skonfigurować zdarzenia dla obszaru roboczego Log Analytics, zobacz [zbieranie źródeł danych dziennika zdarzeń systemu Windows z agentem log Analytics](../azure-monitor/platform/data-sources-windows-events.md).

Alternatywnie problem może być spowodowany brakiem zasobów lub brakiem wymaganych uprawnień.

Jeśli subskrypcja nie zawiera żadnych zasobów pulpitu wirtualnego systemu Windows, nie będzie wyświetlana w parametrze *subskrypcji* .

Jeśli nie masz dostępu do odczytu do odpowiednich subskrypcji, nie będą one wyświetlane w parametrze *subskrypcji* , a ich dane nie będą widoczne na pulpicie nawigacyjnym. Aby rozwiązać ten problem, skontaktuj się z właścicielem subskrypcji i poproś o dostęp do odczytu.

## <a name="my-data-isnt-displaying-properly"></a>Moje dane nie są prawidłowo wyświetlane

Jeśli dane nie są wyświetlane prawidłowo, może to być spowodowane tym, że wystąpił problem podczas procesu konfiguracji Azure Monitor. Najpierw upewnij się, że wypełniono wszystkie pola w skoroszycie konfiguracji, zgodnie z opisem w temacie [używanie Azure monitor dla pulpitu wirtualnego systemu Windows do monitorowania wdrożenia](azure-monitor.md). Ustawienia dla nowych i istniejących środowisk można zmienić w dowolnym momencie. Jeśli nie ma żadnych liczników lub zdarzeń, dane skojarzone z nimi nie będą wyświetlane w Azure Portal.

Jeśli nie masz żadnych informacji, ale dane nadal nie są wyświetlane prawidłowo, może wystąpić problem z kwerendą lub źródłami danych. 

Jeśli nie widzisz żadnych błędów instalacji i nadal nie widzisz oczekiwanych danych, możesz poczekać 15 minut i odświeżyć kanał informacyjny. Azure Monitor ma okres opóźnienia 15 minut na zapełnienie danych dziennika. Aby dowiedzieć się więcej, zobacz czas pozyskiwania [danych dziennika w Azure monitor](../azure-monitor/platform/data-ingestion-time.md).

Na koniec Jeśli nie masz żadnych informacji, ale dane nadal nie pojawiają się, może wystąpić problem z kwerendą lub źródłami danych. W takim przypadku może być konieczne skontaktowanie się z pomocą techniczną w celu rozwiązania problemu.

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Chcę dostosować Azure Monitor dla pulpitu wirtualnego systemu Windows

Azure Monitor dla pulpitu wirtualnego systemu Windows używa Azure Monitor skoroszytów. Skoroszyty umożliwiają zapisywanie kopii szablonu skoroszytu pulpitu wirtualnego systemu Windows i tworzenie własnych dostosowań.

Niestandardowe szablony nie będą aktualizowane, gdy grupa produktów zaktualizuje oryginalny szablon. Jest to zgodne z projektem w narzędziu skoroszyty, należy zapisać kopię zaktualizowanego szablonu i ponownie skompilować dostosowania w celu zastosowania aktualizacji. Aby uzyskać więcej informacji, zobacz temat [Rozwiązywanie problemów opartych na skoroszycie](../azure-monitor/insights/troubleshoot-workbooks.md) i [Omówienie skoroszytów](../azure-monitor/platform/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Nie można zinterpretować danych

Dowiedz się więcej na temat terminów dotyczących danych na [Azure monitor słowniku pulpitów wirtualnych systemu Windows](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>Potrzebne dane nie są dostępne

Nie można znaleźć punktu danych ułatwiającego zdiagnozowanie problemu? Wyślij nam opinię!

- Aby dowiedzieć się, jak opuścić opinię, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczną dla pulpitu wirtualnego systemu Windows](troubleshoot-set-up-overview.md).
- Możesz również wystawić opinię na temat pulpitu wirtualnego systemu Windows w [centrum opinii na pulpicie wirtualnym systemu Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) lub [naszym forum UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

## <a name="known-issues"></a>Znane problemy

Poniżej znajdują się problemy, które obecnie są świadome i które pracują w celu naprawy:

- Teraz można wybrać tylko jedną subskrypcję, grupę zasobów i pulę hostów do monitorowania w danym momencie. W związku z tym w przypadku korzystania ze strony raporty użytkowników w celu zrozumienia środowiska użytkownika należy sprawdzić, czy masz poprawną pulę hostów, z której korzysta użytkownik, czy też dane nie zapełnią wizualizacji.

- Nie jest obecnie możliwe Zapisywanie ulubionych ustawień w Azure Monitor, chyba że zostanie zapisany szablon niestandardowy skoroszytu. Oznacza to, że administratorzy IT będą musieli wprowadzać nazwy subskrypcji, nazwy grup zasobów i preferencji puli hostów za każdym razem, gdy otwierają Azure Monitor dla pulpitu wirtualnego systemu Windows.

- Obecnie nie istnieje sposób eksportowania danych z Azure Monitor dla pulpitu wirtualnego systemu Windows do programu Excel.

- Wszystkie informacje o ważności 1 Azure Monitor alertów dla wszystkich produktów w ramach wybranej subskrypcji zostaną wyświetlone na stronie Przegląd. Jest to zgodne z projektem, ponieważ alerty z innych produktów w subskrypcji mogą mieć wpływ na pulpit wirtualny systemu Windows. Teraz zapytanie jest ograniczone do alertów o ważności 1, z wyłączeniem alertów o ważności 0 o wysokim priorytecie na stronie Przegląd.

- Niektóre komunikaty o błędach nie są oznaczane w sposób przyjazny dla użytkownika, a nie wszystkie komunikaty o błędach są opisane w dokumentacji.

## <a name="next-steps"></a>Następne kroki

Jeśli nie masz pewności, jak interpretować dane lub chcesz dowiedzieć się więcej na temat typowych warunków, zapoznaj się z tematem [słownik Azure monitor dla systemu Windows Virtual Desktop](azure-monitor-glossary.md). Jeśli chcesz dowiedzieć się, jak skonfigurować i używać Azure Monitor dla pulpitu wirtualnego systemu Windows, zobacz [używanie Azure monitor dla pulpitu wirtualnego systemu Windows do monitorowania wdrożenia](azure-monitor.md).