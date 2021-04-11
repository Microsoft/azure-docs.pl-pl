---
title: Przegląd uruchamiania/zatrzymywania maszyn wirtualnych v2 (wersja zapoznawcza)
description: W tym artykule opisano dwie wersje funkcji uruchamiania/zatrzymywania maszyn wirtualnych (wersja zapoznawcza), które uruchamiają lub zatrzymują Azure Resource Manager i klasyczne maszyny wirtualne zgodnie z harmonogramem.
ms.topic: conceptual
ms.service: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.openlocfilehash: aac50f085d3ad63fb0183c19cf7d9aaa679e3d04
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111945"
---
# <a name="startstop-vms-v2-preview-overview"></a>Przegląd uruchamiania/zatrzymywania maszyn wirtualnych v2 (wersja zapoznawcza)

Funkcja uruchamiania/zatrzymywania maszyn wirtualnych w wersji 2 (wersja zapoznawcza) uruchamia lub zatrzymuje maszyny wirtualne platformy Azure w wielu subskrypcjach. Uruchamia lub kończy maszyny wirtualne platformy Azure w harmonogramach zdefiniowanych przez użytkownika, udostępnia szczegółowe informacje za pośrednictwem [usługi azure Application Insights](../../azure-monitor/app/app-insights-overview.md)i wysyła opcjonalne powiadomienia przy użyciu [grup akcji](../../azure-monitor/alerts/action-groups.md). Funkcja ta umożliwia zarządzanie maszynami wirtualnymi Azure Resource Manager i klasycznymi maszynami wirtualnymi w większości scenariuszy.

Ta nowa wersja uruchamiania/zatrzymywania maszyn wirtualnych w wersji 2 (wersja zapoznawcza) zawiera zdecentralizowaną opcję automatyzacji o niskich kosztach dla klientów, którzy chcą zoptymalizować koszty maszyn wirtualnych. Oferuje ona wszystkie te same funkcje, co [wersja oryginalna](../../automation/automation-solution-vm-management.md) dostępna w Azure Automation, ale ma na celu skorzystanie z nowszej technologii platformy Azure.

## <a name="overview"></a>Omówienie

Uruchamianie/zatrzymywanie maszyn wirtualnych v2 (wersja zapoznawcza) jest przeprojektowana i nie zależy od dzienników Azure Automation i Azure Monitor, zgodnie z wymaganiami w [poprzedniej wersji](../../automation/automation-solution-vm-management.md). Ta wersja jest oparta na [Azure Functions](../../azure-functions/functions-overview.md) , aby obsłużyć uruchomienie maszyny wirtualnej i zatrzymanie jej wykonywania.

Tożsamość zarządzana jest tworzona w usłudze Azure Active Directory (Azure AD) dla tej aplikacji Azure Functions i umożliwia uruchamianie/zatrzymywanie maszyn wirtualnych w wersji 2 (wersja zapoznawcza) w celu łatwego uzyskiwania dostępu do innych zasobów chronionych przez usługę Azure AD, takich jak aplikacje logiki i maszyny wirtualne platformy Azure. Aby uzyskać więcej informacji na temat tożsamości zarządzanych w usłudze Azure AD, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Funkcja punktu końcowego wyzwalacza HTTP została utworzona w celu obsługi scenariuszy harmonogramu i sekwencji zawartych z funkcją, jak pokazano w poniższej tabeli.

|Nazwa |Wyzwalacz |Opis |
|-----|--------|------------|
|AlertAvailabilityTest |Czasomierz |Ta funkcja wykonuje test dostępności, aby upewnić się, że podstawowa funkcja **AutoStopVM** jest zawsze dostępna.|
|Zatrzymaj autozatrzymanie |HTTP |Ta funkcja obsługuje scenariusz **autostop** , który jest funkcją punktu wejścia wywoływaną z aplikacji logiki.|
|AutoStopAvailabilityTest |Czasomierz |Ta funkcja wykonuje test dostępności, aby upewnić się, że funkcja **zatrzymywania** funkcji podstawowych jest zawsze dostępna.|
|AutoStopVM |HTTP |Ta funkcja jest wyzwalana automatycznie przez alert maszyny wirtualnej, gdy warunek alertu ma wartość true.|
|CreateAutoStopAlertExecutor |Kolejka |Ta funkcja pobiera informacje o ładunku z funkcji **autozatrzymaj** w celu utworzenia alertu na maszynie wirtualnej.|
|Zaplanowana |HTTP |Ta funkcja jest dla scenariusza zaplanowane i sekwencjonowane (zróżnicowane przez schemat ładunku). Jest to funkcja punktu wejścia wywołana z aplikacji logiki i pobiera ładunek do przetwarzania operacji uruchamiania lub zatrzymywania maszyny wirtualnej. |
|ScheduledAvailabilityTest |Czasomierz |Ta funkcja wykonuje test dostępności, aby upewnić się, że funkcja podstawowa **zaplanowana** jest zawsze dostępna.|
|VirtualMachineRequestExecutor |Kolejka |Ta funkcja wykonuje rzeczywistą operację uruchamiania i zatrzymywania na maszynie wirtualnej.|
|VirtualMachineRequestOrchestrator |Kolejka |Ta funkcja pobiera informacje o ładunku z **zaplanowanej** funkcji i organizuje żądania uruchamiania i zatrzymywania maszyny wirtualnej.|

Na przykład **zaplanowana** funkcja wyzwalacza http jest używana do obsługi scenariuszy harmonogramu i sekwencji. Podobnie funkcja **autozatrzymaj** wyzwalacz http obsługuje scenariusz automatycznie Zatrzymaj.

Funkcje wyzwalacza opartego na kolejce są wymagane w ramach obsługi tej funkcji. Wszystkie wyzwalacze oparte na czasomierzu są używane do przeprowadzania testu dostępności i monitorowania kondycji systemu.

 [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) służy do konfigurowania i zarządzania harmonogramami uruchamiania i zatrzymywania dla maszyny wirtualnej. wykonaj akcję, wywołując funkcję przy użyciu ładunku JSON. Domyślnie podczas początkowego wdrażania tworzone jest łącznie pięć Logic Apps w następujących scenariuszach:

- Akcje zaplanowanego uruchomienia i zatrzymania są oparte na określonym harmonogramie dla Azure Resource Manager i klasycznych maszyn wirtualnych. **ststv2_vms_Scheduled_start** i **ststv2_vms_Scheduled_stop** Skonfiguruj zaplanowane uruchamianie i zatrzymywanie.

- Sekwencyjne — akcje uruchamiania i zatrzymywania są oparte na maszynach wirtualnych dotyczących harmonogramu ze wstępnie zdefiniowanymi tagami sekwencjonowania. Obsługiwane są tylko dwa nazwane Tagi — **sequencestart** i **sequencestop**. **ststv2_vms_Sequenced_start** i **ststv2_vms_Sequenced_stop** skonfigurować sekwencję Uruchom i Zatrzymaj.

    > [!NOTE]
    > W tym scenariuszu obsługiwane są tylko Azure Resource Manager maszyny wirtualne.

- Autozatrzymaj — ta funkcja jest używana tylko do wykonywania akcji zatrzymania dla Azure Resource Manager i klasycznych maszyn wirtualnych na podstawie użycia procesora CPU. Może to być również *Akcja podejmowana* zgodnie z harmonogramem, która tworzy alerty na maszynach wirtualnych i w oparciu o warunek, alert jest wyzwalany w celu wykonania akcji zatrzymania. **ststv2_vms_AutoStop** konfiguruje funkcję zatrzymywania.

Każda akcja uruchamiania/zatrzymywania obsługuje przypisanie jednej lub większej liczby subskrypcji, grup zasobów lub listy maszyn wirtualnych.

Konto usługi Azure Storage, które jest wymagane przez funkcje, służy również do uruchamiania/zatrzymywania maszyn wirtualnych w wersji 2 (wersja zapoznawcza) do dwóch celów:

   - Program korzysta z Table Storage platformy Azure do przechowywania metadanych operacji wykonywania (czyli akcji uruchamiania/zatrzymywania maszyny wirtualnej).

   - Za pomocą usługi Azure Queue Storage można obsługiwać wyzwalacze Azure Functions oparte na kolejce.

Wszystkie dane telemetryczne, które są dziennikami śledzenia z wykonywania aplikacji funkcji, są wysyłane do wystąpienia połączonego Application Insights. Dane telemetryczne przechowywane w Application Insights można przeglądać z zestawu wstępnie zdefiniowanych wizualizacji przedstawionych na udostępnionym [pulpicie nawigacyjnym platformy Azure](../../azure-portal/azure-portal-dashboards.md).

:::image type="content" source="media/overview/shared-dashboard-sml.png" alt-text="Pulpit nawigacyjny stanu udostępnionego uruchamiania/zatrzymywania maszyn wirtualnych":::

Powiadomienia e-mail są również wysyłane w wyniku działań wykonywanych na maszynach wirtualnych.

## <a name="new-releases"></a>Nowe wersje

Gdy zostanie wydana nowa wersja narzędzia Start/Stop VM w wersji 2 (wersja zapoznawcza), Twoje wystąpienie zostanie zaktualizowane ponownie bez konieczności ręcznego ponownego wdrażania.

## <a name="supported-scoping-options"></a>Obsługiwane opcje zakresu

### <a name="subscription"></a>Subskrypcja

Można użyć zakresu do subskrypcji, gdy trzeba wykonać akcję Rozpocznij i Zatrzymaj na wszystkich maszynach wirtualnych w całej subskrypcji. w razie potrzeby możesz wybrać wiele subskrypcji.  

Można również określić listę maszyn wirtualnych do wykluczenia i będzie ona ignorować ich działanie. Możesz również użyć symboli wieloznacznych, aby określić wszystkie nazwy, które jednocześnie mogą być ignorowane.

### <a name="resource-group"></a>Grupa zasobów

Określanie zakresu dla grupy zasobów może być używane, gdy trzeba wykonać akcję Rozpocznij i Zatrzymaj na wszystkich maszynach wirtualnych, określając jedną lub więcej nazw grup zasobów i w jednej lub kilku subskrypcjach.

Można również określić listę maszyn wirtualnych do wykluczenia i będzie ona ignorować ich działanie. Możesz również użyć symboli wieloznacznych, aby określić wszystkie nazwy, które jednocześnie mogą być ignorowane.

### <a name="vmlist"></a>VMList

Określanie listy maszyn wirtualnych może być używane, gdy trzeba wykonać akcję uruchamiania i zatrzymywania w określonym zestawie maszyn wirtualnych oraz w wielu subskrypcjach. Ta opcja nie obsługuje określania listy maszyn wirtualnych do wykluczenia.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/).

- Twojemu kontu udzielono uprawnienia [współautor](../../role-based-access-control/built-in-roles.md#contributor) w subskrypcji.

- Uruchamianie/zatrzymywanie maszyn wirtualnych v2 (wersja zapoznawcza) jest dostępna we wszystkich regionach globalnych platformy Azure, które są wymienione na stronie [dostępne w obszarze produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=functions) dla Azure Functions. W przypadku chmury Azure Government jest ona dostępna tylko w regionie Wirginia dla instytucji rządowych Stanów Zjednoczonych.

## <a name="next-steps"></a>Następne kroki

Aby wdrożyć tę funkcję, zobacz [wdrażanie uruchamiania/zatrzymywania maszyn wirtualnych](deploy.md) (wersja zapoznawcza).