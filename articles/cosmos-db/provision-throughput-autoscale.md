---
title: Utwórz kontenery i bazy danych platformy Azure Cosmos w przepływności z obsługą skalowania automatycznego.
description: Zapoznaj się z korzyściami, przypadkami użycia i sposobami udostępniania baz danych i kontenerów platformy Azure Cosmos w przepływności z obsługą skalowania automatycznego.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 81a13dcb7955a7d46f485416bf9b7e4e7be4d9ac
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791718"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-provisioned-throughput"></a>Tworzenie kontenerów i baz danych platformy Azure Cosmos z elastyczną alokacją automatycznego skalowania

Azure Cosmos DB umożliwia skonfigurowanie kontenerów przy użyciu standardowej (ręcznej) przepływności lub elastyczną alokację skalowania automatycznego. W tym artykule opisano zalety i przypadki użycia automatycznego skalowania.

> [!NOTE]
> [Skalowanie automatyczne można włączyć tylko dla nowych baz danych i kontenerów](#create-db-container-autoscale) . Nie jest on dostępny dla istniejących kontenerów i baz danych.

Oprócz standardowej obsługi przepływności można teraz konfigurować kontenery usługi Azure Cosmos z elastyczną przepustowością skalowania automatycznego. W przypadku kontenerów i baz danych skonfigurowanych w ramach przeciążania automatycznego skalowania **automatyczne i natychmiastowe skalowanie zainicjowanej przepływności na podstawie potrzeb aplikacji bez wywierania wpływu na dostępność, opóźnienie, przepływność lub wydajność w całym świecie.**

Podczas konfigurowania kontenerów i baz danych w funkcji automatycznego skalowania należy określić maksymalną przepływność `Tmax` , która nie zostanie przekroczona. Kontenery mogą następnie skalować ich przepływność `0.1*Tmax < T < Tmax`w taki sposób, aby. Innymi słowy, kontenery i bazy danych są skalowane szybko w zależności od potrzeb związanych z obciążeniem, od do 10% maksymalnej wartości przepływności skonfigurowanej do skonfigurowanej maksymalnej wartości przepływności. Po skonfigurowaniu automatycznego skalowania można zmienić ustawienie maksymalna przepływność`Tmax`() dla bazy danych lub kontenera w dowolnym momencie. W przypadku opcji automatycznego skalowania 400 RU/s minimalna przepływność na kontener lub baza danych nie jest już stosowana.

W przypadku określonej maksymalnej przepływności dotyczącej kontenera lub bazy danych system umożliwia działanie w ramach obliczonego limitu magazynu. W przypadku przekroczenia limitu magazynowania maksymalna przepływność jest automatycznie dostosowywana do wyższej wartości. W przypadku korzystania z przepływności poziomu bazy danych z funkcją automatycznego skalowania liczba kontenerów dozwolona w ramach bazy `0.001*TMax`danych jest obliczana jako:. Na przykład jeśli zainicjujesz automatyczne skalowanie RU na 20 000, baza danych może mieć 20 kontenerów.

## <a name="benefits-of-autoscale-provisioned-throughput"></a><a id="autoscale-benefits"></a>Zalety przepływności aprowizacji z funkcją automatycznego skalowania

Kontenery usługi Azure Cosmos, które są skonfigurowane z funkcją automatycznego skalowania, mają następujące zalety:

* **Prosty:** Kontenery z funkcją automatycznego skalowania usuwają złożoność ręcznego zarządzania elastyczną przepływność (jednostek ru) i pojemnością dla różnych kontenerów.

* **Skalowalność:** Kontenery z automatycznym skalowaniem umożliwiają bezproblemową skalowalność zainicjowanej przepływności zgodnie z potrzebami. Nie ma przerw w połączeniach klientów, aplikacje i nie wpływają na żadne istniejące umowy SLA.

* **Koszt ekonomiczny:** Gdy korzystasz z kontenerów skonfigurowanych przy użyciu funkcji automatycznego skalowania, płacisz tylko za zasoby, których obciążenia wymagają w ciągu godziny.

* **Wysoka dostępność:** Kontenery z funkcją automatycznego skalowania używają tych samych globalnie dystrybuowanych, odpornych na uszkodzenia, zaplecza o wysokiej dostępności w celu zapewnienia trwałości danych i wysokiej dostępnej.

## <a name="use-cases-of-autoscale-provisioned-throughput"></a><a id="autoscale-usecases"></a>Przypadki użycia przystosowanej przepływności automatycznego skalowania

Przypadki użycia dla kontenerów usługi Azure Cosmos skonfigurowanych przy użyciu funkcji automatycznego skalowania obejmują:

* **Obciążenia zmienne:** Gdy korzystasz z aplikacji ze szczytowym użyciem przez 1 godzinę do kilku godzin lub kilka razy dziennie. Przykłady obejmują aplikacje dla zasobów ludzkich, budżetowania i raportowania operacyjnego. W takich scenariuszach można używać kontenerów skonfigurowanych przy użyciu funkcji automatycznego skalowania i nie trzeba już ręcznie udostępniać ich wartości szczytowej lub średniej.

* **Nieprzewidywalne obciążenia:** W przypadku korzystania z obciążeń, w których w ciągu dnia występuje użycie bazy danych, ale także szczytowe działania, które są trudne do przewidywania. Przykład obejmuje lokację ruchu, która widzi wzrost aktywności w przypadku zmiany prognozy pogody. Kontenery skonfigurowane przy użyciu funkcji automatycznego skalowania dostosowują pojemność w celu spełnienia wymagań szczytowego obciążenia aplikacji i skalowania w dół w przypadku przekroczenia liczby działań.

* **Nowe aplikacje:** Jeśli wdrażasz nową aplikację i nie wiesz, jak dużo zainicjowanej przepływności (tj. ilu jednostek ru) potrzebujesz. Przy użyciu kontenerów skonfigurowanych przy użyciu funkcji automatycznego skalowania można automatycznie skalować do potrzeb związanych z pojemnością i wymagań aplikacji.

* **Rzadko używane aplikacje:** Jeśli masz aplikację, która jest używana tylko przez kilka godzin w ciągu dnia lub tygodnia lub miesiąca, na przykład w przypadku aplikacji/witryny sieci Web/blogu o niskiej wielkości.

* **Tworzenie i testowanie baz danych:** Jeśli masz deweloperów korzystających z kontenerów w godzinach pracy, ale nie są one potrzebne w nocy lub w weekendy. W przypadku kontenerów skonfigurowanych przy użyciu funkcji automatycznego skalowania skalowanie w dół do minimum, gdy nie jest używane.

* **Zaplanowane obciążenia produkcyjne/zapytania:** Jeśli masz serię zaplanowanych żądań/operacji/zapytań w jednym kontenerze i jeśli istnieją bezczynne okresy, w których chcesz uruchamiać bezwzględnie niską przepływność, możesz to zrobić łatwo. Gdy zaplanowana kwerenda/żądanie zostanie przesłane do kontenera skonfigurowanego przy użyciu funkcji automatycznego skalowania, zostanie ono automatycznie skalowane do rozmiaru i uruchomienia operacji.

Rozwiązania dotyczące poprzednich problemów nie tylko wymagają dużego czasu w implementacji, ale również wprowadzają złożoność w konfiguracji lub kodzie i często wymagają ręcznej interwencji do ich rozwiązania. Funkcja automatycznego skalowania włącza powyższe scenariusze, dzięki czemu nie trzeba już martwić się o te problemy.

## <a name="comparison--standard-manual-vs-autoscale-provisioned-throughput"></a>Porównanie — standardowe (ręczne) a automatyczne skalowanie przepływności

|  | Kontenery skonfigurowane przy użyciu standardowej, alokowanej przepływności  | Kontenery skonfigurowane z przepływną przepustowością automatycznego skalowania |
|---------|---------|---------|
| **Aprowizowana przepływność** | Ręcznie zainicjowany. | Automatycznie i chwilowo skalowane na podstawie wzorców użycia obciążeń. |
| **Szybkość ograniczania żądań/operacji (429)**  | Może się tak zdarzyć, jeśli zużycie przekracza przypuszczalną pojemność. | Nie nastąpi, jeśli używana przepływność mieści się w maksymalnej przepływności wybranej przy użyciu funkcji automatycznego skalowania.   |
| **Planowanie pojemności** |  Należy wykonać planowanie początkowej pojemności i zapewnić wymaganą przepływność. |    Nie musisz martwić się o planowanie pojemności. System automatycznie zajmuje się planowaniem pojemności i zarządzaniem pojemnością. |
| **Cennik** | Ręcznie aprowizacji RU/s na godzinę. | W przypadku kont z jednym regionem zapisu płacisz za przepływność używaną w godzinie, przy użyciu skali RU/s na godzinę. <br/><br/>W przypadku kont z wieloma regionami zapisu nie ma dodatkowej opłaty za automatyczne skalowanie. Opłata jest naliczana za przepływność użyta co godzinę przy użyciu tego samego kursu wieloskładnikowego RU/s na godzinę. |
| **Najlepiej dopasowane do typów obciążeń** |  Przewidywalne i stabilne obciążenia|   Nieprzewidywalne obciążenia i zmienne  |

## <a name="create-a-database-or-a-container-with-autoscale"></a><a id="create-db-container-autoscale"></a>Tworzenie bazy danych lub kontenera z funkcją automatycznego skalowania

Skalowanie automatyczne można skonfigurować dla nowych baz danych lub kontenerów podczas ich tworzenia za pomocą Azure Portal. Wykonaj następujące kroki, aby utworzyć nową bazę danych lub kontener, włączyć funkcję automatycznego skalowania i określić maksymalną przepływność (RU/s).

1. Zaloguj się do [Azure Portal](https://portal.azure.com) lub [Eksploratora Azure Cosmos DB.](https://cosmos.azure.com/)

1. Przejdź do konta Azure Cosmos DB i Otwórz kartę **Eksplorator danych** .

1. Wybierz pozycję **nowy kontener.** Wprowadź nazwę bazy danych, kontenera i klucza partycji. W obszarze **przepływność**wybierz opcję **automatycznego skalowania** , a następnie wybierz maksymalną przepływność (ru/s), przez którą baza danych lub kontener nie mogą być przekroczenia przy użyciu opcji skalowania automatycznego.

   ![Tworzenie kontenera i Konfigurowanie przepływności automatycznego skalowania](./media/provision-throughput-autoscale/create-container-autoscale-mode.png)

1. Wybierz przycisk **OK**.

Udostępnioną bazę danych przepływności można utworzyć przy użyciu funkcji automatycznego skalowania, wybierając opcję **Udostępnij przepływność bazy danych** .

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a>Limity przepływności i magazynu dla automatycznego skalowania

W poniższej tabeli przedstawiono maksymalne limity limitów w zakresie i przestrzeni magazynowej dla różnych opcji skalowania automatycznego:

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
