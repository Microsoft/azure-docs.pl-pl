---
title: Monitoruj urządzenie Azure Data Box Gateway | Microsoft Docs
description: Opisuje sposób używania Azure Portal i lokalnego interfejsu użytkownika sieci Web do monitorowania Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: ed05f3d60f8ba4fbb06327136c7a117ae1d1d2db
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582483"
---
# <a name="monitor-your-azure-data-box-gateway"></a>Monitoruj Azure Data Box Gateway

W tym artykule opisano sposób monitorowania Azure Data Box Gateway. Aby monitorować urządzenie, możesz użyć Azure Portal lub lokalnego interfejsu użytkownika sieci Web. Użyj Azure Portal, aby wyświetlić zdarzenia dotyczące urządzeń, skonfigurować alerty i zarządzać nimi oraz wyświetlić metryki.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Wyświetlanie zdarzeń urządzeń i odpowiadających im alertów
> * Wyświetlanie metryk pojemności i transakcji dla urządzenia
> * Konfigurowanie alertów i zarządzanie nimi

## <a name="view-device-events"></a>Wyświetl zdarzenia urządzenia

[!INCLUDE [data-box-gateway-view-device-events](../../includes/data-box-gateway-view-device-events.md)]

## <a name="view-metrics"></a>Wyświetlanie metryk

[!INCLUDE [data-box-gateway-view-metrics](../../includes/data-box-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Metryki na urządzeniu

W tej sekcji opisano metryki monitorowania na urządzeniu. Metryki mogą być następujące:

* Metryki pojemności. Metryki pojemności są powiązane z pojemnością urządzenia.

* Metryki transakcji. Metryki transakcji są powiązane z operacjami odczytu i zapisu w usłudze Azure Storage.

W poniższej tabeli przedstawiono pełną listę metryk:

|Metryki pojemności                     |Opis  |
|-------------------------------------|-------------|
|**Dostępna pojemność**               | Odnosi się do rozmiaru danych, które mogą być zapisywane na urządzeniu. Innymi słowy, jest to wydajność, którą można udostępnić na urządzeniu. <br></br>Możesz zwolnić pojemność urządzenia, usuwając lokalną kopię plików, która ma kopię zarówno na urządzeniu, jak i w chmurze.        |
|**Całkowita pojemność**                   | Odnosi się do łącznej liczby bajtów na urządzeniu, do której mają zostać zapisane dane. Jest to również określane jako łączny rozmiar lokalnej pamięci podręcznej. <br></br> Teraz można zwiększyć pojemność istniejącego urządzenia wirtualnego, dodając dysk danych. Dodaj dysk danych za pomocą funkcji zarządzania funkcją hypervisor dla maszyny wirtualnej, a następnie uruchom ponownie maszynę wirtualną. Lokalna Pula magazynów urządzenia bramy zostanie rozszerzona w celu uwzględnienia nowo dodanego dysku z danymi. <br></br>Aby uzyskać więcej informacji, przejdź do obszaru [Dodawanie dysku twardego dla maszyny wirtualnej funkcji Hyper-V](https://www.youtube.com/watch?v=EWdqUw9tTe4). |

|Metryki transakcji              | Opis         |
|-------------------------------------|---------|
|**Przekazane bajty w chmurze (urządzenie)**    | Suma wszystkich bajtów przesłanych przez wszystkie udziały na urządzeniu        |
|**Przekazane bajty w chmurze (udział)**     | Bajty przekazane na udział. Może to być: <br></br> Średnia, czyli (suma wszystkich bajtów przesłanych na udział/liczbę udziałów),  <br></br>Max, która jest maksymalną liczbą bajtów przekazana z udziału <br></br>Minimum, czyli minimalna liczba bajtów przekazana z udziału      |
|**Przepływność pobierania w chmurze (udział)**| Bajty pobrane na udział. Może to być: <br></br> Średnia, czyli (suma wszystkich bajtów odczytywanych lub pobranych do udziału/liczby udziałów) <br></br> Max, czyli maksymalna liczba bajtów pobranych z udziału<br></br> i min, czyli minimalna liczba bajtów pobranych z udziału  |
|**Przepływność odczytu w chmurze**            | Suma wszystkich bajtów odczytanych z chmury między wszystkimi udziałami na urządzeniu     |
|**Przepływność przekazywania w chmurze**          | Suma wszystkich bajtów zapisanych w chmurze dla wszystkich udziałów na urządzeniu     |
|**Przepływność przekazywania w chmurze (udział)**  | Suma wszystkich bajtów zapisywana w chmurze z udziału/liczby udziałów jest średnia, maksymalna i minimalna na udział      |
|**Przepływność odczytu (Sieć)**           | Obejmuje przepływność sieci systemowej dla wszystkich bajtów odczytanych z chmury. Ten widok może zawierać dane, które nie są ograniczone do udziałów. <br></br>Dzielenie spowoduje wyświetlenie ruchu przez wszystkie karty sieciowe na urządzeniu. Obejmuje to karty, które nie są połączone lub są włączone.      |
|**Przepływność zapisu (Sieć)**       | Obejmuje przepływność sieci systemowej dla wszystkich bajtów zapisanych w chmurze. Ten widok może zawierać dane, które nie są ograniczone do udziałów. <br></br>Dzielenie spowoduje wyświetlenie ruchu przez wszystkie karty sieciowe na urządzeniu. Obejmuje to karty, które nie są połączone lub są włączone.          |

## <a name="manage-alerts"></a>Zarządzanie alertami

[!INCLUDE [data-box-gateway-manage-alerts](../../includes/data-box-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zarządzać przepustowością](data-box-gateway-manage-bandwidth-schedules.md).
