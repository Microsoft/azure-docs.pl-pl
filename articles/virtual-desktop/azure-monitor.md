---
title: Korzystanie z monitora systemu Windows Virtual Desktop monitor — Azure
description: Jak używać Azure Monitor dla pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1b93e0c0d61eaa390eda66da2a583a4f429ddd79
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709552"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment"></a>Monitorowanie wdrożenia przy użyciu Azure Monitor dla pulpitu wirtualnego systemu Windows

Azure Monitor dla pulpitu wirtualnego systemu Windows to pulpit nawigacyjny oparty na Azure Monitor skoroszytach, które pomagają specjalistom IT zrozumieć swoje środowiska pulpitu wirtualnego systemu Windows. W tym temacie opisano sposób konfigurowania Azure Monitor dla pulpitu wirtualnego systemu Windows w celu monitorowania środowisk pulpitu wirtualnego systemu Windows.

## <a name="requirements"></a>Wymagania

Przed rozpoczęciem korzystania z Azure Monitor dla pulpitu wirtualnego systemu Windows należy skonfigurować następujące elementy:

- Wszystkie środowiska pulpitu wirtualnego systemu Windows, które są monitorowane, muszą być oparte na najnowszej wersji pulpitu wirtualnego systemu Windows, który jest zgodny z Azure Resource Manager.
- Co najmniej jeden skonfigurowany obszar roboczy Log Analytics. Użyj wydanego obszaru roboczego Log Analytics dla hostów sesji pulpitu wirtualnego systemu Windows, aby upewnić się, że liczniki wydajności i zdarzenia są zbierane tylko z hostów sesji w ramach wdrożenia pulpitu wirtualnego systemu Windows.
- Włącz zbieranie danych dla następujących elementów w obszarze roboczym Log Analytics:
    - Diagnostyka ze środowiska pulpitu wirtualnego systemu Windows
    - Zalecane liczniki wydajności z hostów sesji pulpitu wirtualnego systemu Windows
    - Zalecane dzienniki zdarzeń systemu Windows z hostów sesji pulpitu wirtualnego systemu Windows

 Proces konfiguracji danych opisany w tym artykule jest jedynym, który będzie potrzebny do monitorowania pulpitu wirtualnego systemu Windows. Możesz wyłączyć wszystkie inne elementy wysyłające dane do obszaru roboczego Log Analytics, aby zaoszczędzić koszty.

Każda osoba monitorująca Azure Monitor dla pulpitu wirtualnego systemu Windows dla danego środowiska również będzie potrzebować następujących uprawnień do odczytu:

- Dostęp do odczytu do subskrypcji platformy Azure, w których przechowywane są zasoby pulpitu wirtualnego systemu Windows
- Dostęp do odczytu do grup zasobów subskrypcji, w których znajdują się hosty sesji usług pulpitu wirtualnego systemu Windows
- Dostęp do odczytu do obszaru roboczego Log Analytics lub obszarów roboczych

>[!NOTE]
> Dostęp do odczytu umożliwia administratorom wyświetlanie danych. Będą oni potrzebować innych uprawnień do zarządzania zasobami w portalu pulpitu wirtualnego systemu Windows.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Otwórz Azure Monitor dla pulpitu wirtualnego systemu Windows

Możesz otworzyć Azure Monitor dla pulpitu wirtualnego systemu Windows przy użyciu jednej z następujących metod:

- Przejdź do [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks).
- Wyszukaj i wybierz pozycję **pulpit wirtualny systemu Windows** z Azure Portal, a następnie wybierz pozycję **szczegółowe** dane.
- Wyszukaj i wybierz **Azure monitor** z Azure Portal. Wybierz pozycję Usługa **Insights Hub** w obszarze **Insights**, a następnie wybierz pozycję **pulpit wirtualny systemu Windows**.
Po otwarciu strony wprowadź **subskrypcję**, **grupę zasobów**, **pulę hostów** i **zakres czasu** środowiska, które chcesz monitorować.

>[!NOTE]
>Pulpit wirtualny systemu Windows obecnie obsługuje tylko monitorowanie tylko jednej subskrypcji, grupy zasobów i puli hostów. Jeśli nie możesz znaleźć środowiska, które chcesz monitorować, zapoznaj się [z dokumentacją rozwiązywania problemów z](troubleshoot-azure-monitor.md) znanymi żądaniami i problemami dotyczącymi funkcji.

## <a name="log-analytics-settings"></a>Ustawienia Log Analytics

Aby rozpocząć korzystanie z Azure Monitor dla pulpitu wirtualnego systemu Windows, musisz mieć co najmniej jeden obszar roboczy Log Analytics. Aby sprawdzić, czy liczniki wydajności i zdarzenia będą zbierane w ramach wdrożenia pulpitu wirtualnego systemu Windows, należy użyć wydanego obszaru roboczego Log Analytics. Jeśli masz już skonfigurowany obszar roboczy, przejdź z wyprzedzeniem, aby [skonfigurować go za pomocą skoroszytu konfiguracji](#set-up-using-the-configuration-workbook). Aby je skonfigurować, zobacz [Tworzenie obszaru roboczego log Analytics w Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

>[!NOTE]
>Będą stosowane standardowe opłaty za magazyn danych dla Log Analytics. Aby rozpocząć, zalecamy wybranie modelu płatność zgodnie z rzeczywistym użyciem i dostosowanie go w miarę skalowania wdrożenia i przejęcia większej ilości danych. Aby dowiedzieć się więcej, zobacz [cennik Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="set-up-using-the-configuration-workbook"></a>Konfigurowanie za pomocą skoroszytu konfiguracji

Jeśli po raz pierwszy otworzysz Azure Monitor dla pulpitu wirtualnego systemu Windows, musisz skonfigurować Azure Monitor dla środowiska pulpitu wirtualnego systemu Windows. Aby skonfigurować zasoby:

1. Otwórz Azure Monitor dla pulpitu wirtualnego systemu Windows w Azure Portal w [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), a następnie wybierz pozycję **skoroszyt konfiguracji**.
2. Wybierz środowisko do skonfigurowania w ramach **subskrypcji**, **grupy zasobów** i **puli hostów**.

Skoroszyt konfiguracji konfiguruje środowisko monitorowania i pozwala sprawdzić konfigurację po zakończeniu procesu instalacji. Ważne jest, aby sprawdzić konfigurację, jeśli elementy na pulpicie nawigacyjnym nie są wyświetlane prawidłowo lub gdy grupa produktów publikuje aktualizacje, które wymagają nowych ustawień.

### <a name="resource-diagnostic-settings"></a>Ustawienia diagnostyki zasobów

Aby zbierać informacje o infrastrukturze pulpitów wirtualnych systemu Windows, należy włączyć kilka ustawień diagnostycznych pul i obszarów roboczych programu pulpitów wirtualnych systemu Windows (jest to obszar roboczy pulpitu wirtualnego systemu Windows, a nie obszar roboczy Log Analytics). Aby dowiedzieć się więcej na temat pul hostów, obszarów roboczych i innych obiektów zasobów pulpitu wirtualnego systemu Windows, zobacz nasze [Przewodnik po środowisku](environment-setup.md).

Więcej informacji na temat diagnostyki pulpitu wirtualnego systemu Windows i obsługiwanych tabel diagnostycznych można znaleźć w witrynie [wysyłanie diagnostyki pulpitu wirtualnego systemu Windows do log Analytics](diagnostics-log-analytics.md).

Aby ustawić ustawienia diagnostyki zasobów w skoroszycie konfiguracji: 

1. Wybierz kartę **Ustawienia diagnostyki zasobów** w skoroszycie konfiguracja. 
2. Wybierz **log Analytics obszar roboczy** , aby wysłać diagnostykę pulpitu wirtualnego systemu Windows. 

#### <a name="host-pool-diagnostic-settings"></a>Ustawienia diagnostyczne puli hostów

Aby skonfigurować diagnostykę puli hostów za pomocą sekcji ustawień diagnostycznych zasobu w skoroszycie konfiguracji:

1. W obszarze **Pula hostów** Sprawdź, czy jest włączona funkcja diagnostyki pulpitu wirtualnego systemu Windows. Jeśli nie, zostanie wyświetlony komunikat o błędzie informujący o tym, że dla wybranej puli hostów nie znaleziono istniejącej konfiguracji diagnostycznej. Należy włączyć następujące obsługiwane tabele diagnostyczne:

    - Punkt kontrolny
    - Błąd
    - Zarządzanie
    - Połączenie
    - Rejestracja hosta
    - AgentHealthStatus
    
    >[!NOTE]
    > Jeśli komunikat o błędzie nie jest wyświetlany, nie trzeba wykonywać kroków od 2 do 4.

2. Wybierz pozycję **Konfiguruj pulę hostów**.
3. Wybierz pozycję **Wdróż**.
4. Odśwież skoroszyt konfiguracji.

#### <a name="workspace-diagnostic-settings"></a>Ustawienia diagnostyczne obszaru roboczego 

Aby skonfigurować diagnostykę obszaru roboczego za pomocą sekcji ustawień diagnostycznych zasobu w skoroszycie konfiguracji:

 1. W obszarze **obszar roboczy** Sprawdź, czy włączono diagnostykę pulpitu wirtualnego systemu Windows dla obszaru roboczego pulpit wirtualny systemu Windows. Jeśli nie, zostanie wyświetlony komunikat o błędzie informujący o tym, że nie znaleziono istniejącej konfiguracji diagnostycznej dla wybranego obszaru roboczego. Należy włączyć następujące obsługiwane tabele diagnostyczne:
 
    - Punkt kontrolny
    - Błąd
    - Zarządzanie
    - Źródło danych
    
    >[!NOTE]
    > Jeśli komunikat o błędzie nie jest wyświetlany, nie trzeba wykonywać kroków 2-4.

2. Wybierz pozycję **Konfiguruj obszar roboczy**.
3. Wybierz pozycję **Wdróż**.
4. Odśwież skoroszyt konfiguracji.

### <a name="session-host-data-settings"></a>Ustawienia danych hosta sesji

Aby zbierać informacje o hostach sesji usług pulpitu wirtualnego systemu Windows, należy zainstalować agenta Log Analytics na wszystkich hostach sesji w puli hostów, upewnić się, że hosty sesji są wysyłane do Log Analytics obszaru roboczego, i skonfigurować ustawienia agenta Log Analytics w celu zbierania danych wydajności i dzienników zdarzeń systemu Windows.

W obszarze roboczym Log Analytics dane hosta sesji wysyłane do programu nie muszą być tymi samymi, do których są wysyłane dane diagnostyczne. Jeśli masz hosty sesji platformy Azure poza środowiskiem pulpitu wirtualnego systemu Windows, zalecamy wyznaczenie Log Analytics obszaru roboczego dla hostów sesji pulpitu wirtualnego systemu Windows. 

Aby ustawić obszar roboczy Log Analytics, w którym mają być zbierane dane hosta sesji: 

1. Wybierz kartę **Ustawienia danych hosta sesji** w skoroszycie konfiguracja. 
2. Wybierz **obszar roboczy log Analytics** , do którego chcesz wysłać dane hosta sesji. 

#### <a name="session-hosts"></a>Hosty sesji

Należy zainstalować agenta Log Analytics na wszystkich hostach sesji w puli hostów i wysłać dane z tych hostów do wybranego obszaru roboczego Log Analytics. Jeśli Log Analytics nie jest skonfigurowana dla wszystkich hostów sesji w puli hostów, w górnej części **ustawień danych hosta sesji** zostanie wyświetlona sekcja **hosty sesji** z komunikatem "niektóre hosty w puli hostów nie będą wysyłać danych do wybranego log Analytics obszaru roboczego".

>[!NOTE]
> Jeśli nie widzisz sekcji **hosty sesji** lub komunikat o błędzie, wszystkie hosty sesji są poprawnie skonfigurowane. Przejdź z wyprzedzeniem, aby skonfigurować instrukcje dla [liczników wydajności obszaru roboczego](#workspace-performance-counters).

Aby skonfigurować pozostałe hosty sesji przy użyciu skoroszytu konfiguracji:

1. Wybierz pozycję **Dodaj hosty do obszaru roboczego**. 
2. Odśwież skoroszyt konfiguracji.

>[!NOTE]
>Aby można było zainstalować rozszerzenie Log Analytics, należy uruchomić maszynę hosta. Jeśli automatyczne wdrażanie nie działa, można ręcznie zainstalować rozszerzenie na hoście. Aby dowiedzieć się, jak ręcznie zainstalować rozszerzenie, zobacz [log Analytics rozszerzenie maszyny wirtualnej dla systemu Windows](../virtual-machines/extensions/oms-windows.md).

#### <a name="workspace-performance-counters"></a>Liczniki wydajności obszaru roboczego

Aby zbierać informacje o wydajności z hostów sesji i wysyłać je do obszaru roboczego Log Analytics, należy włączyć określone liczniki wydajności.

Jeśli masz już włączone liczniki wydajności i chcesz je usunąć, postępuj zgodnie z instrukcjami podanymi w temacie [Konfigurowanie liczników wydajności](../azure-monitor/agents/data-sources-performance-counters.md). Liczniki wydajności można dodawać i usuwać w tej samej lokalizacji.

Aby skonfigurować liczniki wydajności za pomocą skoroszytu konfiguracji: 

1. W obszarze **obszary robocze liczniki wydajności** w skoroszycie konfiguracji Sprawdź **skonfigurowane liczniki** , aby wyświetlić liczniki, które zostały już włączone do wysłania do obszaru roboczego log Analytics. Sprawdź **brakujące liczniki** , aby upewnić się, że wszystkie wymagane liczniki zostały włączone.
2. Jeśli brakuje liczników, wybierz pozycję **Konfiguruj liczniki wydajności**.
3. Wybierz pozycję **Zastosuj konfigurację**.
4. Odśwież skoroszyt konfiguracji.
5. Upewnij się, że wszystkie wymagane liczniki są włączone, sprawdzając listę **brakujące liczniki** . 

#### <a name="configure-windows-event-logs"></a>Konfigurowanie dzienników zdarzeń systemu Windows

Należy również włączyć określone dzienniki zdarzeń systemu Windows, aby zbierać błędy, ostrzeżenia i informacje z hostów sesji i wysyłać je do obszaru roboczego Log Analytics.

Jeśli włączono już dzienniki zdarzeń systemu Windows i chcesz je usunąć, postępuj zgodnie z instrukcjami zawartymi w temacie [Konfigurowanie dzienników zdarzeń systemu Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).  Dzienniki zdarzeń systemu Windows można dodawać i usuwać w tej samej lokalizacji.

Aby skonfigurować dzienniki zdarzeń systemu Windows przy użyciu skoroszytu konfiguracji:

1. W obszarze **Konfiguracja dzienników zdarzeń systemu Windows** Sprawdź **skonfigurowane dzienniki zdarzeń** , aby wyświetlić dzienniki zdarzeń, które zostały już włączone do wysłania do obszaru roboczego log Analytics. Sprawdź **brakujące dzienniki zdarzeń** , aby upewnić się, że włączono wszystkie dzienniki zdarzeń systemu Windows.
2. Jeśli brakuje dzienników zdarzeń systemu Windows, wybierz pozycję **Konfiguruj zdarzenia**.
3. Wybierz pozycję **Wdróż**.
4. Odśwież skoroszyt konfiguracji.
5. Upewnij się, że wszystkie wymagane dzienniki zdarzeń systemu Windows są włączone, sprawdzając listę **brakujące dzienniki zdarzeń** . 

>[!NOTE]
>Jeśli automatyczne wdrażanie zdarzeń zakończy się niepowodzeniem, wybierz pozycję **Otwórz konfigurację agenta** w skoroszycie konfiguracji, aby ręcznie dodać brakujące dzienniki zdarzeń systemu Windows. 

## <a name="optional-configure-alerts"></a>Opcjonalne: Konfigurowanie alertów

Azure Monitor dla pulpitu wirtualnego systemu Windows umożliwia monitorowanie Azure Monitor alertów w ramach wybranej subskrypcji w kontekście danych pulpitu wirtualnego systemu Windows. Alerty Azure Monitor są opcjonalną funkcją subskrypcji platformy Azure i należy je skonfigurować niezależnie od Azure Monitor dla pulpitu wirtualnego systemu Windows. Za pomocą struktury alertów Azure Monitor można ustawiać niestandardowe alerty dotyczące zdarzeń, diagnostyki i zasobów pulpitu wirtualnego systemu Windows. Aby dowiedzieć się więcej o alertach Azure Monitor, zobacz temat [reagowanie na zdarzenia przy użyciu alertów Azure monitor](../azure-monitor/alerts/tutorial-response.md).

## <a name="diagnostic-and-usage-data"></a>Dane diagnostyczne i użycia

Firma Microsoft automatycznie zbiera dane dotyczące użycia i wydajności za pomocą usługi Azure Monitor. Firma Microsoft używa tych danych w celu poprawienia jakości, bezpieczeństwa i integralności usługi.

Aby zapewnić dokładne i wydajne możliwości rozwiązywania problemów, zebrane dane obejmują identyfikator sesji portalu, Azure Active Directory identyfikator użytkownika i nazwę karty portalu, w której wystąpiło zdarzenie. Firma Microsoft nie zbiera nazw, adresów ani innych informacji kontaktowych.

Aby uzyskać więcej informacji na temat zbierania i używania danych, zobacz [zasady zachowania poufności informacji w witrynie Microsoft Online Services](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>Aby dowiedzieć się więcej na temat wyświetlania lub usuwania danych osobowych zbieranych przez usługę, zobacz temat [żądania usługi Azure Data subject dotyczące Rodo](/microsoft-365/compliance/gdpr-dsr-azure). Aby uzyskać więcej informacji na temat Rodo, zobacz [sekcję Rodo w portalu zaufania usługi](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu Azure Monitor środowiska pulpitu wirtualnego systemu Windows, poniżej przedstawiono niektóre zasoby, które mogą pomóc w rozpoczęciu monitorowania środowiska:

- Zapoznaj się z naszym [słownikiem](azure-monitor-glossary.md) , aby dowiedzieć się więcej na temat terminów i koncepcji dotyczących Azure monitor dla pulpitu wirtualnego systemu Windows.
- W przypadku wystąpienia problemu zapoznaj się z naszym [przewodnikiem rozwiązywania problemów](troubleshoot-azure-monitor.md) , aby uzyskać pomoc i znane problemy.
