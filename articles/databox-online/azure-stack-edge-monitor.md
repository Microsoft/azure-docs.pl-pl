---
title: Monitoruj urządzenie Azure Stack EDGE Pro | Microsoft Docs
description: Zawiera opis sposobu używania Azure Portal i lokalnego interfejsu użytkownika sieci Web do monitorowania Azure Stack EDGE Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.openlocfilehash: aae64cad3603725a4062d5afb42df974bbf8ac40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438794"
---
# <a name="monitor-your-azure-stack-edge-pro"></a>Monitoruj Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

W tym artykule opisano sposób monitorowania Azure Stack Edge. Aby monitorować urządzenie, możesz użyć Azure Portal lub lokalnego interfejsu użytkownika sieci Web. Użyj Azure Portal, aby wyświetlić zdarzenia dotyczące urządzeń, skonfigurować alerty i zarządzać nimi oraz wyświetlić metryki. Użyj lokalnego interfejsu użytkownika sieci Web na urządzeniu fizycznym, aby wyświetlić stan sprzętu różnych składników urządzeń.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Wyświetlanie zdarzeń urządzeń i odpowiadających im alertów
> * Wyświetlanie stanu sprzętu dla składników urządzenia
> * Wyświetlanie metryk pojemności i transakcji dla urządzenia

## <a name="view-device-events"></a>Wyświetl zdarzenia urządzenia

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Wyświetl stan sprzętu

Wykonaj następujące kroki w lokalnym internetowym interfejsie użytkownika, aby wyświetlić stan sprzętu składników urządzenia.

1. Nawiąż połączenie z lokalnym internetowym interfejsem użytkownika urządzenia.
2. Przejdź do pozycji **konserwacja > stan sprzętu**. Można wyświetlić kondycję różnych składników urządzeń.

    ![Wyświetl stan sprzętu](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Wyświetlanie metryk

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Metryki na urządzeniu

W tej sekcji opisano metryki monitorowania na urządzeniu. Metryki mogą być następujące:

* Metryki pojemności. Metryki pojemności są powiązane z pojemnością urządzenia.

* Metryki transakcji. Metryki transakcji są powiązane z operacjami odczytu i zapisu w usłudze Azure Storage.

* Metryki obliczeń brzegowych. Metryki obliczeń brzegowych są powiązane z użyciem obliczeń brzegowych na urządzeniu.

W poniższej tabeli przedstawiono pełną listę metryk:

|Metryki pojemności                     |Opis  |
|-------------------------------------|-------------|
|**Dostępna pojemność**               | Odnosi się do rozmiaru danych, które mogą być zapisywane na urządzeniu. Innymi słowy, ta Metryka to wydajność, którą można udostępnić na urządzeniu. <br></br>Możesz zwolnić pojemność urządzenia, usuwając lokalną kopię plików, która ma kopię zarówno na urządzeniu, jak i w chmurze.        |
|**Całkowita pojemność**                   | Odnosi się do łącznej liczby bajtów na urządzeniu, do której mają zostać zapisane dane, co jest również określane jako łączny rozmiar lokalnej pamięci podręcznej. <br></br> Teraz można zwiększyć pojemność istniejącego urządzenia wirtualnego, dodając dysk danych. Dodaj dysk danych za pomocą funkcji zarządzania funkcją hypervisor dla maszyny wirtualnej, a następnie uruchom ponownie maszynę wirtualną. Lokalna Pula magazynów urządzenia bramy zostanie rozszerzona w celu uwzględnienia nowo dodanego dysku z danymi. <br></br>Aby uzyskać więcej informacji, przejdź do obszaru [Dodawanie dysku twardego dla maszyny wirtualnej funkcji Hyper-V](https://www.youtube.com/watch?v=EWdqUw9tTe4). |

|Metryki transakcji              | Opis         |
|-------------------------------------|---------|
|**Przekazane bajty w chmurze (urządzenie)**    | Suma wszystkich bajtów przesłanych przez wszystkie udziały na urządzeniu        |
|**Przekazane bajty w chmurze (udział)**     | Bajty przekazane na udział. Ta Metryka może być: <br></br> Średnia, czyli (suma wszystkich bajtów przesłanych na udział/liczbę udziałów),  <br></br>Max, która jest maksymalną liczbą bajtów przekazana z udziału <br></br>Minimum, czyli minimalna liczba bajtów przekazana z udziału      |
|**Przepływność pobierania w chmurze (udział)**| Bajty pobrane na udział. Ta Metryka może być: <br></br> Średnia, czyli (suma wszystkich bajtów odczytywanych lub pobranych do udziału/liczby udziałów) <br></br> Max, czyli maksymalna liczba bajtów pobranych z udziału<br></br> i min, czyli minimalna liczba bajtów pobranych z udziału  |
|**Przepływność odczytu w chmurze**            | Suma wszystkich bajtów odczytanych z chmury między wszystkimi udziałami na urządzeniu     |
|**Przepływność przekazywania w chmurze**          | Suma wszystkich bajtów zapisanych w chmurze dla wszystkich udziałów na urządzeniu     |
|**Przepływność przekazywania w chmurze (udział)**  | Suma wszystkich bajtów zapisywana w chmurze z udziału/liczby udziałów jest średnia, maksymalna i minimalna na udział      |
|**Przepływność odczytu (Sieć)**           | Obejmuje przepływność sieci systemowej dla wszystkich bajtów odczytanych z chmury. Ten widok może zawierać dane, które nie są ograniczone do udziałów. <br></br>Podział będzie przedstawiał ruch przez wszystkie karty sieciowe na urządzeniu, w tym karty, które nie są połączone lub włączone.      |
|**Przepływność zapisu (Sieć)**       | Obejmuje przepływność sieci systemowej dla wszystkich bajtów zapisanych w chmurze. Ten widok może zawierać dane, które nie są ograniczone do udziałów. <br></br>Podział będzie przedstawiał ruch przez wszystkie karty sieciowe na urządzeniu, w tym karty, które nie są połączone lub włączone.          |

| Metryki obliczeń brzegowych              | Opis         |
|-------------------------------------|---------|
|**Obliczenia brzegowe — użycie pamięci**      |           |
|**Obliczenia brzegowe — procentowy procesor CPU**    |         |

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zarządzać przepustowością](azure-stack-edge-manage-bandwidth-schedules.md).
Dowiedz się, jak [zarządzać powiadomieniami o alertach zdarzeń urządzeń](azure-stack-edge-gpu-manage-device-event-alert-notifications.md).
