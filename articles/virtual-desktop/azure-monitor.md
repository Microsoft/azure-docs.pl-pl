---
title: Korzystanie z monitora systemu Windows Virtual Desktop monitor (wersja zapoznawcza) — Azure
description: Jak używać Azure Monitor dla pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e9da1071686dafa003a5a49d0864b77644493344
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594467"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment-preview"></a>Monitorowanie wdrożenia (wersja zapoznawcza) za pomocą Azure Monitor dla pulpitu wirtualnego systemu Windows

>[!IMPORTANT]
>Azure Monitor dla pulpitu wirtualnego systemu Windows jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Monitor dla systemu Windows Virtual Desktop (wersja zapoznawcza) to pulpit nawigacyjny oparty na Azure Monitor skoroszytach, które pomagają specjalistom IT zrozumieć swoje środowiska pulpitu wirtualnego systemu Windows. W tym temacie opisano sposób konfigurowania Azure Monitor dla pulpitu wirtualnego systemu Windows w celu monitorowania środowisk pulpitu wirtualnego systemu Windows.

## <a name="requirements"></a>Wymagania

Przed rozpoczęciem korzystania z Azure Monitor dla pulpitu wirtualnego systemu Windows należy skonfigurować następujące elementy:

- Wszystkie środowiska pulpitu wirtualnego systemu Windows, które są monitorowane, muszą być oparte na najnowszej wersji pulpitu wirtualnego systemu Windows, który jest zgodny z Azure Resource Manager.

- Co najmniej jeden skonfigurowany obszar roboczy Log Analytics.

- Włącz zbieranie danych dla następujących elementów w obszarze roboczym Log Analytics:
    - Wszystkie wymagane liczniki wydajności
    - Wszystkie liczniki wydajności lub zdarzenia używane w Azure Monitor dla pulpitu wirtualnego systemu Windows
    - Dane narzędzia diagnostycznego dla wszystkich obiektów w środowisku, które będą monitorowane.
    - Maszyny wirtualne w środowisku, które będą monitorowane.

Każda osoba monitorująca Azure Monitor dla pulpitu wirtualnego systemu Windows dla danego środowiska również będzie potrzebować następujących uprawnień do odczytu:

- Dostęp do odczytu do grupy zasobów, w której znajdują się zasoby środowiska.

- Dostęp do odczytu do grup zasobów, w których znajdują się hosty sesji środowiska

>[!NOTE]
> Dostęp do odczytu umożliwia administratorom wyświetlanie danych. Będą oni potrzebować innych uprawnień do zarządzania zasobami w portalu pulpitu wirtualnego systemu Windows.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Otwórz Azure Monitor dla pulpitu wirtualnego systemu Windows

Możesz otworzyć Azure Monitor dla pulpitu wirtualnego systemu Windows przy użyciu jednej z następujących metod:

- Przejdź do [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks).

- Wyszukaj i wybierz pozycję **pulpit wirtualny systemu Windows** z Azure Portal, a następnie wybierz pozycję **szczegółowe** dane.

- Wyszukaj i wybierz **Azure monitor** z Azure Portal. Wybierz pozycję usługi **Insights Hub** w obszarze usługi **Insights**, a w obszarze **inne** wybierz pozycję **pulpit wirtualny systemu Windows** , aby otworzyć pulpit nawigacyjny na stronie Azure monitor.

Gdy Azure Monitor dla pulpitu wirtualnego systemu Windows jest otwarty, zaznacz co najmniej jedno pole wyboru z etykietą **subskrypcja**, **Grupa zasobów**, **pulę hostów** i **zakres czasu** , na podstawie którego środowiska chcesz monitorować.

>[!NOTE]
>Pulpit wirtualny systemu Windows obecnie obsługuje tylko monitorowanie tylko jednej subskrypcji, grupy zasobów i puli hostów. Jeśli nie możesz znaleźć środowiska, które chcesz monitorować, zapoznaj się [z dokumentacją rozwiązywania problemów z](troubleshoot-azure-monitor.md) znanymi żądaniami i problemami dotyczącymi funkcji.

## <a name="set-up-with-the-configuration-workbook"></a>Konfigurowanie za pomocą skoroszytu konfiguracji

Jeśli jest to pierwszy raz otwarty Azure Monitor dla pulpitu wirtualnego systemu Windows, musisz skonfigurować Azure Monitor dla zasobów pulpitu wirtualnego systemu Windows. Aby skonfigurować zasoby:

1. Otwórz skoroszyt w Azure Portal.
2. Wybierz pozycję **Otwórz skoroszyt konfiguracji**.

Skoroszyt konfiguracji konfiguruje środowisko monitorowania i pozwala sprawdzić konfigurację po zakończeniu procesu instalacji. Ważne jest, aby sprawdzić konfigurację, jeśli elementy na pulpicie nawigacyjnym nie są wyświetlane prawidłowo lub gdy grupa produktów publikuje aktualizacje, które wymagają dodatkowych punktów danych.

## <a name="host-pool-diagnostic-settings"></a>Ustawienia diagnostyczne puli hostów

Należy włączyć Azure Monitor ustawienia diagnostyczne dla wszystkich obiektów w środowisku pulpitu wirtualnego systemu Windows, które obsługują tę funkcję.

1. Otwórz Azure Monitor dla pulpitu wirtualnego systemu Windows w [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), a następnie wybierz pozycję **skoroszyt konfiguracji**.

2. Wybierz środowisko do monitorowania w ramach **subskrypcji**, **grupy zasobów** i **puli hostów**.

3. W obszarze **Ustawienia diagnostyczne puli hostów** Sprawdź, czy dla puli hostów włączono funkcję diagnostyki pulpitu wirtualnego systemu Windows. Jeśli nie, zostanie wyświetlony komunikat o błędzie informujący o tym, że dla wybranej puli hostów nie znaleziono istniejącej konfiguracji diagnostycznej. 
   
   Należy włączyć następujące tabele:

    - Punkt kontrolny
    - Błąd
    - Zarządzanie
    - Połączenie
    - Rejestracja hosta

    >[!NOTE]
    > Jeśli komunikat o błędzie nie jest wyświetlany, nie musisz wykonywać kroku 4.

4. Wybierz pozycję **Konfiguruj pulę hostów**.

5. Wybierz pozycję **Wdróż**.

6. Odśwież skoroszyt.

Więcej informacji na temat włączania diagnostyki na wszystkich obiektach w środowisku pulpitu wirtualnego systemu Windows lub dostęp do obszaru roboczego Log Analytics przy [wysyłaniu diagnostyki pulpitu wirtualnego systemu Windows do log Analytics](diagnostics-log-analytics.md).

## <a name="configure-log-analytics"></a>Konfigurowanie usługi Log Analytics

Aby rozpocząć korzystanie z Azure Monitor dla pulpitu wirtualnego systemu Windows, musisz także mieć co najmniej jeden Log Analytics obszar roboczy, aby zbierać dane ze środowiska, które planujesz monitorować i dostarczać do skoroszytu. Jeśli masz już skonfigurowaną funkcję, przejdź z wyprzedzeniem, aby [skonfigurować liczniki wydajności](#set-up-performance-counters). Aby skonfigurować nowy obszar roboczy Log Analytics dla subskrypcji platformy Azure zawierającej środowisko pulpitu wirtualnego systemu Windows, zobacz [tworzenie log Analytics obszaru roboczego w Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

>[!NOTE]
>Będą stosowane standardowe opłaty za magazyn danych dla Log Analytics. Aby rozpocząć, zalecamy wybranie modelu płatność zgodnie z rzeczywistym użyciem i dostosowanie go w miarę skalowania wdrożenia i przejęcia większej ilości danych. Aby dowiedzieć się więcej, zobacz [cennik Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="set-up-performance-counters"></a>Konfigurowanie liczników wydajności

Należy włączyć określone liczniki wydajności dla kolekcji w odpowiednim interwale próbkowania w obszarze roboczym Log Analytics. Te liczniki wydajności są jedynymi licznikami, które będą potrzebne do monitorowania pulpitu wirtualnego systemu Windows. Możesz wyłączyć wszystkie inne, aby zaoszczędzić koszty.

Jeśli masz już włączone liczniki wydajności i chcesz je usunąć, postępuj zgodnie z instrukcjami podanymi w temacie [Konfigurowanie liczników wydajności](../azure-monitor/agents/data-sources-performance-counters.md) , aby ponownie skonfigurować liczniki wydajności. W tym artykule opisano sposób dodawania liczników, ale można je również usunąć w tej samej lokalizacji.

Jeśli nie skonfigurowano jeszcze liczników wydajności, poniżej przedstawiono sposób konfigurowania ich pod kątem Azure Monitor dla pulpitu wirtualnego systemu Windows:

1. Przejdź do [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), a następnie wybierz **skoroszyt konfiguracji** dolny okna.

2. W obszarze **konfiguracja log Analytics** wybierz obszar roboczy, który został skonfigurowany w ramach subskrypcji.

3. W **obszarze liczniki wydajności obszaru roboczego** zostanie wyświetlona lista liczników wymaganych do monitorowania. Z prawej strony tej listy Sprawdź elementy na liście **brakujące liczniki** , aby włączyć liczniki, które będą potrzebne do rozpoczęcia monitorowania obszaru roboczego.

4. Wybierz pozycję **Konfiguruj liczniki wydajności**.

5. Wybierz pozycję **Zastosuj konfigurację**.

6. Odśwież skoroszyt konfiguracji i Kontynuuj Konfigurowanie środowiska.

Nowe liczniki wydajności można również dodać po początkowej konfiguracji przy każdej aktualizacji usługi i wymaga nowych narzędzi do monitorowania. Można sprawdzić, czy wszystkie wymagane liczniki są włączone, wybierając je z listy **brakujące liczniki** .

>[!NOTE]
>Liczniki wydajności opóźnienia wejścia są zgodne tylko z systemem Windows 10 RS5 lub nowszym lub Windows Server 2019 lub nowszym.

Aby dowiedzieć się więcej na temat ręcznego dodawania liczników wydajności, które nie są już włączone dla kolekcji, zobacz [Konfigurowanie liczników wydajności](../azure-monitor/agents/data-sources-performance-counters.md).

### <a name="set-up-windows-events"></a>Konfigurowanie zdarzeń systemu Windows

Następnie musisz włączyć określone zdarzenia systemu Windows dla kolekcji w obszarze roboczym Log Analytics. Zdarzenia opisane w tej sekcji są jedynymi Azure Monitor dla potrzeb pulpitu wirtualnego systemu Windows. Możesz wyłączyć wszystkie inne, aby zaoszczędzić koszty.

Aby skonfigurować zdarzenia systemu Windows:

1. Jeśli masz już włączone zdarzenia systemu Windows i chcesz je usunąć, Usuń niepotrzebne zdarzenia przed użyciem skoroszytu konfiguracji w celu włączenia zestawu wymaganego do monitorowania.

2. Przejdź do Azure Monitor dla pulpitu wirtualnego systemu Windows w [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), a następnie wybierz pozycję **skoroszyt konfiguracji** u dołu okna.

3. W obszarze **Konfiguracja zdarzeń systemu Windows** istnieje lista zdarzeń systemu Windows, które są wymagane do monitorowania. Po prawej stronie tej listy znajdują się listy **brakujące zdarzenia** , w których znajdują się wymagane nazwy zdarzeń i typy zdarzeń, które nie są aktualnie włączone dla Twojego obszaru roboczego. Zapisz każdą z tych nazw w przyszłości.

4. Wybierz pozycję **Konfiguracja otwartych obszarów roboczych**.

5. Wybierz pozycję **dane**.

6. Wybierz pozycję **dzienniki zdarzeń systemu Windows**.

7. Dodaj brakujące nazwy zdarzeń z kroku 3 i wymagany typ zdarzenia dla każdego z nich.

8. Odśwież skoroszyt konfiguracji i Kontynuuj Konfigurowanie środowiska.

Nowe zdarzenia systemu Windows można dodać po konfiguracji początkowej, jeśli aktualizacje narzędzia do monitorowania wymagają włączenia nowych zdarzeń. Aby upewnić się, że wszystkie wymagane zdarzenia zostały włączone, Wróć do listy **brakujące zdarzenia** i Włącz wszystkie brakujące zdarzenia.

## <a name="install-the-log-analytics-agent-on-all-hosts"></a>Zainstaluj agenta Log Analytics na wszystkich hostach

Na koniec należy zainstalować agenta Log Analytics na wszystkich hostach w puli hostów w celu wysłania danych z hostów do wybranego obszaru roboczego.

Aby zainstalować agenta Log Analytics:

1. Przejdź do Azure Monitor dla pulpitu wirtualnego systemu Windows w [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), a następnie wybierz pozycję **skoroszyt konfiguracji** u dołu okna.

2. Jeśli Log Analytics nie zostanie skonfigurowana dla wszystkich hostów w puli hostów, zobaczysz błąd w dolnej części sekcji konfiguracji Log Analytics z komunikatem "niektóre hosty w puli hostów nie będą wysyłać danych do wybranego obszaru roboczego Log Analytics". Wybierz pozycję **Dodaj hosty do obszaru roboczego** , aby dodać wybrane hosty. Jeśli komunikat o błędzie nie jest wyświetlany, Zatrzymaj go tutaj.

3. Odśwież skoroszyt konfiguracji.

>[!NOTE]
>Aby można było zainstalować rozszerzenie Log Analytics, należy uruchomić maszynę hosta. Jeśli automatyczne wdrażanie na hoście zakończy się niepowodzeniem, zawsze możesz zainstalować rozszerzenie na hoście ręcznie. Aby dowiedzieć się, jak ręcznie zainstalować rozszerzenie, zobacz [log Analytics rozszerzenie maszyny wirtualnej dla systemu Windows](../virtual-machines/extensions/oms-windows.md).

## <a name="optional-configure-alerts"></a>Opcjonalne: Konfigurowanie alertów

Istnieje możliwość skonfigurowania Azure Monitor dla pulpitu wirtualnego systemu Windows w celu powiadomienia użytkownika o poważnym Azure Monitor alertach w ramach wybranej subskrypcji. Aby to zrobić, postępuj zgodnie z instrukcjami w temacie [reagowanie na zdarzenia przy użyciu alertów Azure monitor](../azure-monitor/alerts/tutorial-response.md).

## <a name="diagnostic-and-usage-data"></a>Dane diagnostyczne i użycia

Firma Microsoft automatycznie zbiera dane dotyczące użycia i wydajności za pomocą usługi Azure Monitor. Firma Microsoft używa tych danych w celu poprawienia jakości, bezpieczeństwa i integralności usługi.

Aby zapewnić dokładne i wydajne możliwości rozwiązywania problemów, zebrane dane obejmują identyfikator sesji portalu, Azure Active Directory identyfikator użytkownika i nazwę karty portalu, w której wystąpiło zdarzenie. Firma Microsoft nie zbiera nazw, adresów ani innych informacji kontaktowych.

Aby uzyskać więcej informacji na temat zbierania i używania danych, zobacz [zasady zachowania poufności informacji w witrynie Microsoft Online Services](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>Aby dowiedzieć się więcej na temat wyświetlania lub usuwania danych osobowych zbieranych przez usługę, zobacz temat [żądania usługi Azure Data subject dotyczące Rodo](/microsoft-365/compliance/gdpr-dsr-azure). Aby uzyskać więcej informacji na temat Rodo, zobacz [sekcję Rodo w portalu zaufania usługi](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu Azure Portal pulpitu wirtualnego systemu Windows, poniżej przedstawiono niektóre zasoby, które mogą pomóc:

- Zapoznaj się z naszym [słownikiem](azure-monitor-glossary.md) , aby dowiedzieć się więcej na temat terminów i koncepcji dotyczących Azure monitor dla pulpitu wirtualnego systemu Windows.
- Jeśli wystąpi problem, zapoznaj się z naszym [przewodnikiem rozwiązywania problemów](troubleshoot-azure-monitor.md) , aby uzyskać pomoc.