---
title: Diagnozowanie typowych błędów pakietów kodu przy użyciu Service Fabric
description: Dowiedz się, jak rozwiązywać typowe błędy pakietu kodu za pomocą usługi Azure Service Fabric
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 400651b240f0518a85b9deb7a7293a77a88b0861
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591707"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Diagnozowanie typowych błędów pakietów kodu przy użyciu Service Fabric

W tym artykule opisano, co oznacza, że pakiet kodu może zostać nieoczekiwanie zakończony. Zapewnia wgląd w możliwe przyczyny typowych kodów błędów oraz kroki rozwiązywania problemów.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>Kiedy proces lub kontener jest nieoczekiwanie zakończony?

Gdy usługa Azure Service Fabric odbiera żądanie uruchomienia pakietu kodu, rozpocznie Przygotowywanie środowiska w systemie lokalnym zgodnie z opcjami ustawionymi w manifestach aplikacji i usługi. Te przygotowania mogą obejmować rezerwowe punkty końcowe sieci lub zasoby, konfigurowanie reguł zapory lub Konfigurowanie ograniczeń zarządzania zasobami. 

Po poprawnym skonfigurowaniu środowiska Service Fabric próbuje wywołać pakiet kodu. Ten krok jest uznawany za pomyślne, jeśli środowisko uruchomieniowe systemu operacyjnego lub kontenera zgłosiło, że proces lub kontener został aktywowany pomyślnie. Jeśli aktywacja nie powiedzie się, powinien zostać wyświetlony komunikat o kondycji w SFX, podobny do następującego:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

Po pomyślnym aktywowaniu pakietu kodu Service Fabric rozpoczyna monitorowanie jego okresu istnienia. W tym momencie proces lub kontener można przerwać w dowolnym momencie z kilku powodów. Może na przykład zainicjowanie biblioteki DLL nie powiodło się lub w systemie operacyjnym nie ma miejsca na stercie. Jeśli pakiet kodu został zakończony, powinien zostać wyświetlony następujący komunikat o kondycji w programie SFX:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

Kod zakończenia w tym komunikacie o kondycji jest jedynym komunikatem, że proces lub kontener zawiera informacje o tym, dlaczego został przerwany. Może być wygenerowany przez dowolny poziom stosu. Na przykład ten kod zakończenia może być powiązany z błędem systemu operacyjnego lub problemem z platformą .NET lub mógł zostać zgłoszony przez swój kod. Skorzystaj z tego artykułu jako punktu wyjścia do diagnozowania źródła kodów zakończenia zakończenia i możliwych rozwiązań. Należy jednak pamiętać, że są to ogólne rozwiązania typowych scenariuszy i mogą nie dotyczyć tego błędu.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Jak mogę sprawdzić, czy Service Fabric zakończył mój pakiet kodu?

Service Fabric może być odpowiedzialna za zakończenie działania pakietu kodu z różnych powodów. Na przykład może zadecydować, aby umieścić pakiet kodu w innym węźle na potrzeby równoważenia obciążenia. Możesz sprawdzić, czy Service Fabric przerwany pakiet kodu, Jeśli zobaczysz dowolny z kodów zakończenia w poniższej tabeli.

>[!NOTE]
> Jeśli proces lub kontener kończy się kodem zakończenia innym niż kody w poniższej tabeli, Service Fabric nie jest odpowiedzialny za jego zakończenie.

Kod zakończenia | Opis
--------- | -----------
7147 | Wskazuje, że Service Fabric bezpiecznie zamknąć proces lub kontener przez wysłanie do niego sygnału Ctrl + C.
7148 | Wskazuje, że Service Fabric przerwał proces lub kontener. Czasami ten kod błędu wskazuje, że proces lub kontener nie odpowiedział w odpowiednim czasie po wysłaniu sygnału Ctrl + C i musiał zostać zakończony.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Inne typowe kody błędów i ich potencjalne poprawki

Kod zakończenia | Wartość szesnastkowa | Krótki opis | Główna przyczyna | Potencjalna poprawka
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Ten błąd czasami oznacza, że na maszynie zabrakło miejsca na stercie. Ta przyczyna jest szczególnie przydatna, jeśli masz wiele procesów należących do aplikacji uruchomionej w węźle. | Jeśli program nie został skompilowany w celu reagowania na sygnały Ctrl + C, można włączyć ustawienie **EnableActivateNoWindow** w manifeście klastra. Włączenie tego ustawienia oznacza, że pakiet kodu zostanie uruchomiony bez okna graficznego interfejsu użytkownika i nie otrzyma sygnałów Ctrl + C. Ta akcja zmniejsza również ilość miejsca użycia sterty pulpitu przez każdy proces. Jeśli pakiet kodu musi odbierać sygnały Ctrl + C, można zwiększyć rozmiar sterty pulpitu węzła.
3762504530 | 0xe0434352 | Nie dotyczy | Ta wartość reprezentuje kod błędu dla nieobsłużonego wyjątku z kodu zarządzanego (czyli platformy .NET). | Ten kod zakończenia wskazuje, że aplikacja zgłosiła wyjątek, który pozostanie nieobsłużony i który zakończył proces. Pierwszym krokiem w ustaleniu, co wyzwolił ten błąd, debugowanie dzienników aplikacji i plików zrzutów.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [diagnozowaniu innych typowych scenariuszy](service-fabric-diagnostics-common-scenarios.md).
* Zapoznaj się z bardziej szczegółowym omówieniem dzienników Azure Monitor i ich oferty, odczytując [Azure monitor przegląd](../azure-monitor/overview.md).
* Dowiedz się więcej o dziennikach Azure Monitor [alerty](../azure-monitor/alerts/alerts-overview.md) dotyczące pomocy w wykrywaniu i diagnostyce.
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań](../azure-monitor/logs/log-query-overview.md) , które są oferowane w ramach dzienników Azure monitor.
