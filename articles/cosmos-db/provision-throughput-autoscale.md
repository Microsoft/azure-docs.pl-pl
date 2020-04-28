---
title: Utwórz kontenery usługi Azure Cosmos i bazy danych w trybie skalowania automatycznego.
description: Poznaj korzyści, przypadki użycia i sposób aprowizacji baz danych i kontenerów usługi Azure Cosmos w trybie skalowania automatycznego.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 49fea2cc9a48d5afc794d6932208b61252bea424
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196493"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autoscale-mode"></a>Tworzenie kontenerów i baz danych usługi Azure Cosmos w trybie skalowania automatycznego

Azure Cosmos DB pozwala na inicjowanie obsługi przepływności w kontenerach w trybie ręcznym lub skalowaniem automatycznym. W tym artykule opisano zalety i przypadki użycia trybu skalowania automatycznego.

> [!NOTE]
> [Skalowanie automatyczne można włączyć tylko dla nowych baz danych i kontenerów](#create-db-container-autoscale) . Nie jest on dostępny dla istniejących kontenerów i baz danych.

Oprócz ręcznego inicjowania obsługi przepływności można teraz skonfigurować kontenery usługi Azure Cosmos w trybie skalowania automatycznego. Kontenery i bazy danych skonfigurowane w trybie automatycznego skalowania będą **automatycznie i natychmiast skalować zainicjowaną przepływność na podstawie potrzeb aplikacji, bez wywierania wpływu na dostępność, opóźnienia, przepływność lub wydajność w całym świecie.**

Podczas konfigurowania kontenerów i baz danych w trybie skalowania automatycznego należy określić maksymalną przepływność `Tmax` , która nie zostanie przekroczona. Kontenery mogą następnie skalować ich przepływność `0.1*Tmax < T < Tmax`w taki sposób, aby. Innymi słowy, kontenery i bazy danych są skalowane szybko w zależności od potrzeb związanych z obciążeniem, od do 10% maksymalnej wartości przepływności skonfigurowanej do skonfigurowanej maksymalnej wartości przepływności. Po skonfigurowaniu automatycznego skalowania można zmienić ustawienie maksymalna przepływność`Tmax`() dla bazy danych lub kontenera w dowolnym momencie. W przypadku opcji automatycznego skalowania 400 RU/s minimalna przepływność na kontener lub baza danych nie jest już stosowana.

W przypadku określonej maksymalnej przepływności dotyczącej kontenera lub bazy danych system umożliwia działanie w ramach obliczonego limitu magazynu. W przypadku przekroczenia limitu magazynowania maksymalna przepływność jest automatycznie dostosowywana do wyższej wartości. W przypadku korzystania z przepływności poziomu bazy danych z trybem skalowania automatycznego liczba kontenerów dozwolona w ramach bazy `0.001*TMax`danych jest obliczana jako:. Na przykład jeśli zainicjujesz automatyczne skalowanie RU na 20 000, baza danych może mieć 20 kontenerów.

## <a name="benefits-of-autoscale-mode"></a><a id="autoscale-benefits"></a>Zalety trybu skalowania automatycznego

Kontenery usługi Azure Cosmos, które są skonfigurowane w trybie skalowania automatycznego, mają następujące zalety:

* **Prosty:** Kontenery w trybie skalowania automatycznego usuwają złożoność ręcznego zarządzania elastyczną przepływność (jednostek ru) i pojemnością dla różnych kontenerów.

* **Skalowalność:** Kontenery w trybie skalowania automatycznego umożliwiają bezproblemowe skalowanie alokowanej przepływności w miarę potrzeb. Nie ma przerw w połączeniach klientów, aplikacje i nie wpływają na żadne istniejące umowy SLA.

* **Koszt ekonomiczny:** W przypadku korzystania z kontenerów skonfigurowanych w trybie skalowania automatycznego opłaty są naliczane tylko za zasoby, których obciążenia są wymagane w ciągu godziny.

* **Wysoka dostępność:** Kontenery w trybie skalowania automatycznego używają tych samych globalnie dystrybuowanych, odpornych na błędy, zaplecza o wysokiej dostępności, aby zapewnić trwałość i wysoką dostępność danych.

## <a name="use-cases-of-autoscale-mode"></a><a id="autoscale-usecases"></a>Przypadki użycia trybu skalowania automatycznego

Przypadki użycia dla kontenerów usługi Azure Cosmos skonfigurowanych w trybie skalowania automatycznego obejmują:

* **Obciążenia zmienne:** Gdy korzystasz z aplikacji ze szczytowym użyciem przez 1 godzinę do kilku godzin lub kilka razy dziennie. Przykłady obejmują aplikacje dla zasobów ludzkich, budżetowania i raportowania operacyjnego. W takich scenariuszach można używać kontenerów skonfigurowanych w trybie skalowania automatycznego i nie trzeba już ręcznie inicjować ich w celu zapewnienia szczytowej lub średniej wydajności.

* **Nieprzewidywalne obciążenia:** W przypadku korzystania z obciążeń, w których w ciągu dnia występuje użycie bazy danych, ale także szczytowe działania, które są trudne do przewidywania. Przykład obejmuje lokację ruchu, która widzi wzrost aktywności w przypadku zmiany prognozy pogody. Kontenery skonfigurowane w trybie skalowania automatycznego dostosowują pojemność w celu spełnienia wymagań szczytowego obciążenia aplikacji i skalowania w dół w przypadku przekroczenia liczby działań.

* **Nowe aplikacje:** Jeśli wdrażasz nową aplikację i nie wiesz, jak dużo zainicjowanej przepływności (tj. ilu jednostek ru) potrzebujesz. W przypadku kontenerów skonfigurowanych w trybie skalowania automatycznego można automatycznie skalować zapotrzebowanie na pojemność i wymagania aplikacji.

* **Rzadko używane aplikacje:** Jeśli masz aplikację, która jest używana tylko przez kilka godzin w ciągu dnia lub tygodnia lub miesiąca, na przykład w przypadku aplikacji/witryny sieci Web/blogu o niskiej wielkości.

* **Tworzenie i testowanie baz danych:** Jeśli masz deweloperów korzystających z kontenerów w godzinach pracy, ale nie są one potrzebne w nocy lub w weekendy. W przypadku kontenerów skonfigurowanych w trybie skalowania automatycznego skalowanie w dół do minimum, gdy nie jest używane.

* **Zaplanowane obciążenia produkcyjne/zapytania:** Jeśli masz serię zaplanowanych żądań/operacji/zapytań w jednym kontenerze i jeśli istnieją bezczynne okresy, w których chcesz uruchamiać bezwzględnie niską przepływność, możesz to zrobić łatwo. Gdy zaplanowane zapytanie/żądanie zostanie przesłane do kontenera skonfigurowanego w trybie skalowania automatycznego, będzie automatycznie skalowane w górę, jak to konieczne, i uruchomić operację.

Rozwiązania dotyczące poprzednich problemów nie tylko wymagają dużego czasu w implementacji, ale również wprowadzają złożoność w konfiguracji lub kodzie i często wymagają ręcznej interwencji do ich rozwiązania. Tryb skalowania automatycznego włącza powyższe scenariusze, dzięki czemu nie trzeba już martwić się o te problemy.

## <a name="comparison--containers-configured-in-manual-mode-vs-autoscale-mode"></a>Porównanie — kontenery skonfigurowane w trybie ręcznym a tryb skalowania automatycznego

|  | Kontenery skonfigurowane w trybie ręcznym  | Kontenery skonfigurowane w trybie skalowania automatycznego |
|---------|---------|---------|
| **Aprowizowana przepływność** | Ręcznie zainicjowany. | Automatycznie i chwilowo skalowane na podstawie wzorców użycia obciążeń. |
| **Szybkość ograniczania żądań/operacji (429)**  | Może się tak zdarzyć, jeśli zużycie przekracza przypuszczalną pojemność. | Nie nastąpi, jeśli używana przepływność mieści się w maksymalnej przepływności wybranej z trybem skalowania automatycznego.   |
| **Planowanie pojemności** |  Należy wykonać planowanie początkowej pojemności i zapewnić wymaganą przepływność. |    Nie musisz martwić się o planowanie pojemności. System automatycznie zajmuje się planowaniem pojemności i zarządzaniem pojemnością. |
| **Cennik** | Ręcznie aprowizacji RU/s na godzinę. | W przypadku kont z jednym regionem zapisu płacisz za przepływność używaną w godzinie, przy użyciu skali RU/s na godzinę. <br/><br/>W przypadku kont z wieloma regionami zapisu nie ma dodatkowej opłaty za automatyczne skalowanie. Opłata jest naliczana za przepływność użyta co godzinę przy użyciu tego samego kursu wieloskładnikowego RU/s na godzinę. |
| **Najlepiej dopasowane do typów obciążeń** |  Przewidywalne i stabilne obciążenia|   Nieprzewidywalne obciążenia i zmienne  |

## <a name="create-a-database-or-a-container-with-autoscale-mode"></a><a id="create-db-container-autoscale"></a>Tworzenie bazy danych lub kontenera z trybem skalowania automatycznego

Skalowanie automatyczne można skonfigurować dla nowych baz danych lub kontenerów podczas ich tworzenia za pomocą Azure Portal. Wykonaj następujące kroki, aby utworzyć nową bazę danych lub kontener, włączyć funkcję automatycznego skalowania i określić maksymalną przepływność (RU/s).

1. Zaloguj się do [Azure Portal](https://portal.azure.com) lub [Eksploratora Azure Cosmos DB.](https://cosmos.azure.com/)

1. Przejdź do konta Azure Cosmos DB i Otwórz kartę **Eksplorator danych** .

1. Wybierz pozycję **nowy kontener.** Wprowadź nazwę bazy danych, kontenera i klucza partycji. W obszarze **przepływność**wybierz opcję **automatycznego skalowania** , a następnie wybierz maksymalną przepływność (ru/s), przez którą baza danych lub kontener nie mogą być przekroczenia przy użyciu opcji skalowania automatycznego.

   ![Tworzenie kontenera i Konfigurowanie przepływności automatycznego skalowania](./media/provision-throughput-autoscale/create-container-autoscale-mode.png)

1. Wybierz przycisk **OK**.

Udostępnioną bazę danych przepływności można utworzyć z trybem skalowania automatycznego, wybierając opcję **Udostępnij przepływność bazy danych** .

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a>Limity przepływności i magazynu dla automatycznego skalowania

W poniższej tabeli przedstawiono maksymalne limity limitów w granicach i magazynu dla różnych opcji w trybie skalowania automatycznego:

|Maksymalny limit przepływności  |Maksymalny limit magazynu  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20 000 RU/s  |  200 GB  |
|100 000 RU/s    |  1 TB   |
|500 000 RU/s    |  5 TB  |

## <a name="next-steps"></a>Następne kroki

* Przejrzyj [często zadawane pytania dotyczące skalowania automatycznego](autoscale-faq.md).
* Dowiedz się więcej na temat [partycji logicznych](partition-data.md).
* Dowiedz się, jak [zainicjować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [udostępnić przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
