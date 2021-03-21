---
title: Rozwiązywanie problemów z kondycją gościa usługi VM Insights (wersja zapoznawcza)
description: Opisuje kroki rozwiązywania problemów, które można wykonać w przypadku problemów z kondycją usługi VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: d8b37569ebaa8e75be601a1efd65a23a61aeaa75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051943"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>Rozwiązywanie problemów z kondycją gościa usługi VM Insights (wersja zapoznawcza)
W tym artykule opisano kroki rozwiązywania problemów, które można wykonać w przypadku problemów z kondycją usługi VM Insights.

## <a name="error-message-that-no-data-is-available"></a>Komunikat o błędzie, że żadne dane nie są dostępne 

![Brak danych](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Sprawdź, czy maszyna wirtualna spełnia wymagania dotyczące konfiguracji

1. Sprawdź, czy maszyna wirtualna jest maszyną wirtualną platformy Azure. Usługa Azure Arc dla serwerów nie jest obecnie obsługiwana.
2. Sprawdź, czy na maszynie wirtualnej jest uruchomiony [obsługiwany system operacyjny](vminsights-health-enable.md?current-limitations.md).
3. Sprawdź, czy maszyna wirtualna jest zainstalowana w [obsługiwanym regionie](vminsights-health-enable.md?current-limitations.md).
4. Sprawdź, czy obszar roboczy usługi Log Analytics jest zainstalowany w [obsługiwanym regionie](vminsights-health-enable.md?current-limitations.md).

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Sprawdź, czy maszyna wirtualna została prawidłowo dołączona
Sprawdź, czy na maszynie wirtualnej pomyślnie zainicjowano rozszerzenie agenta Azure Monitor i agenta kondycji maszyny wirtualnej gościa. Wybierz **rozszerzenia** z menu maszyny wirtualnej w Azure Portal i upewnij się, że na liście są wymienione dwa agenci.

![Rozszerzenia maszyn wirtualnych](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Sprawdź, czy na maszynie wirtualnej jest włączona tożsamość przypisana przez system
Sprawdź, czy na maszynie wirtualnej jest włączona tożsamość przypisana do systemu. Wybierz opcję **tożsamość** z menu maszyny wirtualnej w Azure Portal. Jeśli tożsamość zarządzana przez użytkownika jest włączona, niezależnie od stanu tożsamości zarządzanej przez system Agent Azure Monitor nie będzie mógł komunikować się z usługą konfiguracji, a rozszerzenie kondycji gościa nie będzie działać.

![Tożsamość przypisana przez system](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Weryfikuj regułę zbierania danych
Sprawdź, czy zasada zbierania danych określająca rozszerzenie kondycji jako źródło danych jest skojarzone z maszyną wirtualną.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Komunikat o błędzie dla nieprawidłowego żądania z powodu niewystarczających uprawnień
Ten błąd wskazuje, że dostawca zasobów **Microsoft. Monitor obciążenia został** nie został zarejestrowany w subskrypcji. Aby uzyskać szczegółowe informacje na temat rejestrowania tego dostawcy zasobów, zobacz temat [dostawcy zasobów platformy Azure i typy](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) . 

![Złe żądanie](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>Następne kroki

- [Zapoznaj się z omówieniem funkcji kondycji gościa usługi VM Insights](vminsights-health-overview.md)