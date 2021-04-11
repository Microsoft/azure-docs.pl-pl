---
title: Rozwiązywanie problemów z kondycją gościa usługi VM Insights (wersja zapoznawcza)
description: Opisuje kroki rozwiązywania problemów, które można wykonać w przypadku problemów z kondycją usługi VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2021
ms.openlocfilehash: 834c70e02ab25fa6dcadb5f6c997be09aaf5e353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932781"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>Rozwiązywanie problemów z kondycją gościa usługi VM Insights (wersja zapoznawcza)
W tym artykule opisano kroki rozwiązywania problemów, które można wykonać w przypadku problemów z kondycją usługi VM Insights.


## <a name="upgrade-available-message-is-still-displayed-after-upgrading-guest-health"></a>Po uaktualnieniu kondycji gościa jest nadal wyświetlany komunikat o dostępności uaktualnienia 

- Sprawdź, czy maszyna wirtualna jest uruchomiona na globalnym platformie Azure. Serwery z włączonymi łukiemmi nie są jeszcze obsługiwane.
- Sprawdź, czy jest obsługiwana wersja regionu i systemu operacyjnego maszyny wirtualnej, zgodnie z opisem w temacie [włączanie Azure monitor dla maszyn wirtualnych kondycji gościa (wersja zapoznawcza)](vminsights-health-enable.md).
- Sprawdź, czy rozszerzenie kondycji gościa zostało pomyślnie zainstalowane z kodem zakończenia 0.
- Sprawdź, czy rozszerzenie agenta Azure Monitor zostało pomyślnie zainstalowane.
- Sprawdź, czy dla maszyny wirtualnej jest włączona tożsamość zarządzana przypisana przez system.
- Sprawdź, czy dla maszyny wirtualnej nie zostały określone tożsamości zarządzane przypisane przez użytkownika.
- Sprawdź, czy maszyny wirtualne z systemem Windows są używane w *języku angielskim (Stany Zjednoczone*). Lokalizacja nie jest obecnie obsługiwana przez agenta Azure Monitor.
- Sprawdź, czy maszyna wirtualna nie używa serwera proxy sieci. Agent Azure Monitor nie obsługuje obecnie serwerów proxy.
- Sprawdź, czy Agent rozszerzenia kondycji został uruchomiony bez błędów. Jeśli nie można uruchomić agenta, stan agenta może być uszkodzony. Usuń zawartość folderu stan agenta i uruchom ponownie agenta.
  - W przypadku systemu Linux: Demon jest *vmGuestHealthAgent*. Folder stanu to */var/opt/vmGuestHealthAgent/**
  - Dla systemu Windows: usługa to *Agent kondycji gościa maszyny wirtualnej*. Folder stanu to _%ProgramData%\Microsoft\VMGuestHealthAgent \\ *_.
- Sprawdź, czy Agent Azure Monitor ma łączność sieciową. 
  - Na maszynie wirtualnej spróbuj wykonać polecenie ping _<region> . Handler.Control.Monitor.Azure.com_. Na przykład w przypadku maszyny wirtualnej w westeurope próbuj wykonać polecenie ping _westeurope.Handler.Control.Monitor.Azure.com:443_.
- Sprawdź, czy maszyna wirtualna ma skojarzenie z regułą zbierania danych w tym samym regionie co obszar roboczy Log Analytics.
  -  Zapoznaj się z tematem **Tworzenie reguły zbierania danych (DCR)** w temacie [Włączanie Azure monitor dla maszyn wirtualnych kondycji gościa (wersja zapoznawcza)](vminsights-health-enable.md) w celu zapewnienia, że struktura DCR jest prawidłowa. Należy zwrócić szczególną uwagę na obecność sekcji źródła danych *liczniki wydajności* , która została skonfigurowana do próbkowania trzech liczników i obecności sekcji *inputDataSources* w konfiguracji rozszerzenia kondycji w celu wysłania liczników do rozszerzenia.
-  Sprawdź, czy maszyna wirtualna nie ma błędów rozszerzenia kondycji gościa.
   -  W przypadku systemu Linux: Sprawdź dzienniki pod adresem _/var/log/Azure/Microsoft.Azure.Monitor.VirtualMachines.GuestHealthLinuxAgent/*. log_.
   -  Dla systemu Windows: Sprawdź dzienniki _w \{ wersji rozszerzenia C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitor.VirtualMachines.GuestHealthWindowsAgent} \* . log_.
-  Sprawdź maszynę wirtualną pod kątem błędów agenta Azure Monitor.
   -  W przypadku systemu Linux: Sprawdź dzienniki w _/var/log/MDSD. *_.
   -  Dla systemu Windows: Sprawdź dzienniki w lokalizacji _C:\WindowsAzure\Resources \* {vmName}. AMADataStore_.
 



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

## <a name="health-shows-as-unknown-after-guest-health-is-enabled"></a>Kondycja jest wyświetlana jako "nieznana" po włączeniu kondycji gościa.

### <a name="verify-that-performance-counters-on-windows-nodes-are-working-correctly"></a>Sprawdź, czy liczniki wydajności w węzłach systemu Windows działają prawidłowo 
Kondycja gościa polega na tym, że Agent może zbierać liczniki wydajności z węzła. podstawowy zestaw bibliotek liczników wydajności może ulec uszkodzeniu i może wymagać odbudowania. Postępuj zgodnie z instrukcjami w obszarze [Ręczne ponowne kompilowanie wartości biblioteki licznika wydajności](/troubleshoot/windows-server/performance/rebuild-performance-counter-library-values) , aby ponownie skompilować liczniki wydajności.





## <a name="next-steps"></a>Następne kroki

- [Zapoznaj się z omówieniem funkcji kondycji gościa usługi VM Insights](vminsights-health-overview.md)