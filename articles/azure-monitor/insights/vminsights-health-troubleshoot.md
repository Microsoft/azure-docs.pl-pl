---
title: Rozwiązywanie problemów Azure Monitor dla maszyn wirtualnych kondycji gościa (wersja zapoznawcza)
description: Opisuje kroki rozwiązywania problemów, które można podjąć w przypadku problemów z Azure Monitor dla maszyn wirtualnych kondycji.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: 048230bb85d2ac599efc6bd3fb326f98038cce57
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94687020"
---
# <a name="troubleshoot-azure-monitor-for-vms-guest-health-preview"></a>Rozwiązywanie problemów Azure Monitor dla maszyn wirtualnych kondycji gościa (wersja zapoznawcza)
W tym artykule opisano kroki rozwiązywania problemów, które można podjąć w przypadku problemów z Azure Monitor dla maszyn wirtualnych kondycji.

## <a name="error-message-that-no-data-is-available"></a>Komunikat o błędzie, że żadne dane nie są dostępne 

![Brak danych](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Sprawdź, czy maszyna wirtualna spełnia wymagania dotyczące konfiguracji

1. Sprawdź, czy maszyna wirtualna jest maszyną wirtualną platformy Azure. Usługa Azure ARC dla serwerów nie jest obecnie obsługiwana.
2. Sprawdź, czy na maszynie wirtualnej jest uruchomiony [obsługiwany system operacyjny](vminsights-health-enable.md?current-limitations.md).
3. Sprawdź, czy maszyna wirtualna jest zainstalowana w [obsługiwanym regionie](vminsights-health-enable.md?current-limitations.md).
4. Sprawdź, czy obszar roboczy Log Analytics jest zainstalowany w [obsługiwanym regionie](vminsights-health-enable.md?current-limitations.md).

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Sprawdź, czy maszyna wirtualna została prawidłowo dołączona
Sprawdź, czy na maszynie wirtualnej pomyślnie zainicjowano rozszerzenie agenta Azure Monitor i agenta kondycji maszyny wirtualnej gościa. Wybierz **rozszerzenia** z menu maszyny wirtualnej w Azure Portal. Jeśli na liście znajdują się dwa agenci, zobacz []() .

![Rozszerzenia maszyn wirtualnych](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Sprawdź, czy na maszynie wirtualnej jest włączona tożsamość przypisana przez system
Sprawdź, czy na maszynie wirtualnej jest włączona tożsamość przypisana do systemu. Wybierz opcję **tożsamość** z menu maszyny wirtualnej w Azure Portal. Jeśli na liście znajdują się dwa agenci, zobacz []() .

![Tożsamość przypisana przez system](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Weryfikuj regułę zbierania danych
Sprawdź, czy zasada zbierania danych określająca rozszerzenie kondycji jako źródło danych jest skojarzone z maszyną wirtualną.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Komunikat o błędzie dla nieprawidłowego żądania z powodu niewystarczających uprawnień
Ten błąd wskazuje, że dostawca zasobów **Microsoft. Monitor obciążenia został** nie został zarejestrowany w subskrypcji. Aby uzyskać szczegółowe informacje na temat rejestrowania tego dostawcy zasobów, zobacz temat [dostawcy zasobów platformy Azure i typy](/azure/azure-resource-manager/management/resource-providers-and-types#register-resource-provider) . 

![Złe żądanie](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>Następne kroki

- [Zapoznaj się z omówieniem funkcji kondycji gościa Azure Monitor dla maszyn wirtualnych](vminsights-health-overview.md)
