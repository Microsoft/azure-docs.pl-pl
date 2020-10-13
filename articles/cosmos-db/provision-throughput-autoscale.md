---
title: Utwórz kontenery usługi Azure Cosmos i bazy danych w trybie skalowania automatycznego.
description: Poznaj korzyści, przypadki użycia i sposób aprowizacji baz danych i kontenerów usługi Azure Cosmos w trybie skalowania automatycznego.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: 87112af870100859ae008f77eefc4b58eac1b0fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570740"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-throughput"></a>Tworzenie kontenerów i baz danych usługi Azure Cosmos przy użyciu przepływności automatycznego skalowania

Usługa Azure Cosmos DB pozwala skonfigurować przepływność standardową (ręczną) lub aprowizowaną przez autoskalowanie w bazach danych i kontenerach. W tym artykule opisano zalety i przypadki użycia przepływności aprowizowanej przez autoskalowanie. 

Alokacja automatycznego skalowania jest odpowiednia dla obciążeń o kluczowym znaczeniu, które mają zmienne lub nieprzewidywalne wzorce ruchu, i wymagają umowy SLA na wysoką wydajność i skalowalność. 

Funkcja automatycznego skalowania Azure Cosmos DB **automatycznie i natychmiast skaluje przepływność (ru/s)** bazy danych lub kontenera na podstawie użycia, bez wywierania wpływu na dostępność, opóźnienie, przepływność lub wydajność obciążenia. 

## <a name="benefits-of-autoscale"></a>Zalety automatycznego skalowania

Bazy danych i kontenery usługi Azure Cosmos, które są skonfigurowane przy użyciu przepływności z obsługą automatycznego skalowania, mają następujące zalety:

* **Prosty:** Funkcja automatycznego skalowania eliminuje złożoność zarządzania RU/s przy użyciu niestandardowego skryptu lub ręcznie skalowania pojemności. 

* **Skalowalność:** Bazy danych i kontenery automatycznie skalują zainicjowaną przepływność zgodnie z potrzebami. Nie ma przerw w połączeniach klientów, aplikacjach ani wpływu na Azure Cosmos DB umowy SLA.

* **Koszt ekonomiczny:** Funkcja automatycznego skalowania pomaga zoptymalizować użycie RU/s i zużycie kosztów poprzez skalowanie w dół, gdy nie jest używane. Płacisz tylko za zasoby, których obciążenia wymagają w ciągu godziny. Przez wszystkie godziny w miesiącu, jeśli ustawisz automatyczne skalowanie RU/s (Tmax) i wykorzystasz pełną kwotę Tmax przez 66% godzin lub mniej, będziesz zapisywać z funkcją automatycznego skalowania. Aby dowiedzieć się więcej, zobacz artykuł [jak wybrać między standardowym (ręcznym) i automatycznym skalowaniem przepływności](how-to-choose-offer.md) .

* **Wysoka dostępność:** Bazy danych i kontenery korzystające z funkcji automatycznego skalowania używają tych samych globalnie rozproszonych, odpornych na uszkodzenia Azure Cosmos DB zaplecza w celu zapewnienia trwałości i wysokiej dostępności danych.

## <a name="use-cases-of-autoscale"></a>Przypadki użycia automatycznego skalowania

Przypadki użycia funkcji automatycznego skalowania obejmują:

* **Zmienne lub nieprzewidywalne obciążenia:** Gdy obciążenia mają zmienne lub nieprzewidywalne wzrosty użycia, funkcja automatycznego skalowania pomaga automatycznie skalować w górę i w dół w zależności od użycia. Przykłady obejmują internetowe witryny sieci Web, które mają różne wzorce ruchu w zależności od sezonowości; Obciążenia IOT, które mają skoki w różnych porach dnia; aplikacje biznesowe, które zobaczą szczytowe użycie kilka razy w ciągu miesiąca lub roku i nie tylko. Dzięki funkcji automatycznego skalowania nie trzeba już ręcznie udostępniać wartości szczytowej lub średniej wydajności. 

* **Nowe aplikacje:** Jeśli tworzysz nową aplikację i nie masz pewności o przepływności (RU/s), automatyczne skalowanie ułatwia rozpoczęcie pracy. Możesz rozpocząć od punktu wejścia skalowania automatycznego o 400 – 4000 RU/s, monitorować użycie i określić odpowiednie wartości RU/s w czasie.

* **Rzadko używane aplikacje:** Jeśli masz aplikację, która jest używana tylko przez kilka godzin kilkakrotnie, co dzień, tydzień lub miesiąc, na przykład w przypadku aplikacji/witryny sieci Web/blogu o niskiej pojemności — funkcja automatycznego skalowania dostosowuje wydajność w celu obsłużenia szczytowego użycia i skalowania w dół w czasie. 

* **Obciążenia deweloperskie i testowe:** Jeśli ty lub Twój zespół korzysta z baz danych i kontenerów platformy Azure Cosmos w godzinach pracy, ale nie są one potrzebne w nocy lub w weekendy, funkcja automatycznego skalowania pomaga zaoszczędzić koszt przez skalowanie w dół do wartości minimalnej, gdy nie jest używana. 

* **Zaplanowane obciążenia produkcyjne/zapytania:** Jeśli masz serię zaplanowanych żądań, operacji lub zapytań, które mają być uruchamiane w okresach bezczynności, można to łatwo zrobić przy użyciu funkcji automatycznego skalowania. Gdy zachodzi potrzeba uruchomienia obciążenia, przepływność będzie automatycznie skalowana do potrzeb i później skalować w dół. 

Tworzenie niestandardowego rozwiązania tych problemów nie tylko wymaga dużego czasu, ale również wprowadza złożoność w konfiguracji lub kodzie aplikacji. Funkcja automatycznego skalowania włącza powyższe scenariusze i eliminuje konieczność stosowania niestandardowego lub ręcznego skalowania pojemności. 

## <a name="how-autoscale-provisioned-throughput-works"></a>Jak działa funkcja automatycznego skalowania przepływności aprowizacji

Podczas konfigurowania kontenerów i baz danych przy użyciu funkcji automatycznego skalowania należy określić maksymalną przepływność `Tmax` . Azure Cosmos DB skaluje przepływność `T` `0.1*Tmax <= T <= Tmax` . Jeśli na przykład ustawisz maksymalną przepływność na 20 000 RU/s, przepływność zostanie przeskalowana między 2000 a 20 000 RU/s. Ze względu na to, że skalowanie jest automatyczne i natychmiastowe, w dowolnym momencie można użyć do zainicjowania obsługi `Tmax` bez opóźnień. 

Każda godzina zostanie naliczona za najwyższą przepływność `T` , którą system przeskaluje do godziny.

Punkt wejścia dla maksymalnej przepływności skalowania automatycznego `Tmax` rozpoczyna się od 4000 ru/s, co skaluje się między 400 4000 ru/s. Można ustawić `Tmax` liczbę przyrostów 1000 ru/s i zmienić wartość w dowolnym momencie.  

## <a name="enable-autoscale-on-existing-resources"></a>Włącz automatyczne skalowanie dla istniejących zasobów

Użyj [Azure Portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container) , aby włączyć automatyczne skalowanie dla istniejącej bazy danych lub kontenera. W dowolnym momencie można przełączać się między funkcją automatycznego skalowania i standardową (ręczną). Aby uzyskać więcej informacji, zobacz tę [dokumentację](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) . Obecnie w przypadku wszystkich interfejsów API można użyć Azure Portal, aby włączyć automatyczne skalowanie dla istniejących zasobów.

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a> Limity przepływności i magazynu dla automatycznego skalowania

Dla każdej wartości `Tmax` , baza danych lub kontener może przechowywać łącznie `0.01 * Tmax GB` . Po osiągnięciu tej ilości miejsca do magazynowania maksymalna wartość RU/s zostanie automatycznie zwiększona na podstawie nowej wartości miejsca do magazynowania, bez wpływu na aplikację. 

Jeśli na przykład zaczniesz od maksymalnej wartości 50 000 RU/s (skalowanie w zakresie 5000 – 50 000 RU/s), możesz przechowywać do 500 GB danych. Jeśli przekroczysz 500 GB (np. miejsce do magazynowania będzie wynosić 600 GB), nową maksymalną wartością RU/s będzie 60 000 RU/s (skalowanie w zakresie 6000 – 60 000 RU/s).

Jeśli używasz przepływności na poziomie bazy danych z autoskalowaniem, pierwsze 25 kontenerów może współdzielić maksymalną wartość autoskalowania równą 4000 RU/s (skalowanie w zakresie 400 – 4000 RU/s), o ile nie przekroczysz 40 GB miejsca do magazynowania. Aby uzyskać więcej informacji, zobacz tę [dokumentację](autoscale-faq.md#can-i-change-the-max-rus-on-the-database-or-container) .

## <a name="comparison--containers-configured-with-manual-vs-autoscale-throughput"></a>Porównanie — kontenery skonfigurowane z ręczną przepływność automatycznego skalowania
Więcej szczegółów można znaleźć w tej [dokumentacji](how-to-choose-offer.md) dotyczącej wyboru między standardowym (ręcznym) i automatycznym skalowaniem przepływności.  

|| Kontenery ze standardową (ręczną) przepływności  | Kontenery o przepływności automatycznego skalowania |
|---------|---------|---------|
| **Elastyczna przepływność (RU/s)** | Ręcznie zainicjowany. | Automatycznie i chwilowo skalowane na podstawie wzorców użycia obciążeń. |
| **Szybkość ograniczania żądań/operacji (429)**  | Może się tak zdarzyć, jeśli zużycie przekracza przypuszczalną pojemność. | Nie nastąpi w przypadku używania RU/s w określonym zakresie przepływności skalowania automatycznego.    |
| **Planowanie pojemności** |  Trzeba przeprowadzić planowanie pojemności i zapewnić dokładną przepływność. |    System automatycznie zajmuje się planowaniem pojemności i zarządzaniem pojemnością. |
| **Cennik** | Płacisz za ręcznie zainicjowaną jednostkę RU/s na godzinę przy użyciu [standardowej (ręcznej) jednostki ru/s na godzinę](https://azure.microsoft.com/pricing/details/cosmos-db/). | Opłata jest naliczana za godzinę dla największej liczby jednostek RU na sekundę, w której system jest skalowany do godziny. <br/><br/> W przypadku kont z jednym regionem zapisu opłata jest naliczana za użycie jednostek RU/s w godzinie, przy użyciu [skali ru/s na godzinę](https://azure.microsoft.com/pricing/details/cosmos-db/). <br/><br/>W przypadku kont z wieloma regionami zapisu nie ma dodatkowej opłaty za automatyczne skalowanie. Opłata jest naliczana za przepływność użyta co godzinę przy użyciu tego samego [wieloregionowego zapisu ru/s na godzinę](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| **Najlepiej dopasowane do typów obciążeń** |  Przewidywalne i stabilne obciążenia|   Nieprzewidywalne obciążenia i zmienne  |

## <a name="next-steps"></a>Następne kroki

* Przejrzyj [często zadawane pytania dotyczące skalowania automatycznego](autoscale-faq.md).
* Dowiedz się [, jak wybierać przepływność ręczną i skalowanie automatyczne](how-to-choose-offer.md).
* Dowiedz się, jak [udostępnić przepływność automatycznego skalowania dla bazy danych lub kontenera usługi Azure Cosmos](how-to-provision-autoscale-throughput.md).
* Więcej informacji na temat [partycjonowania](partition-data.md) znajduje się w Azure Cosmos DB.


