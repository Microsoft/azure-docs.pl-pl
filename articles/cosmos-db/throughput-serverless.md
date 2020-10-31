---
title: Jak wybrać między zainicjowaną przepływność a bezserwerową Azure Cosmos DB
description: Dowiedz się, jak wybrać między zainicjowaną przepływność a bezserwerowym obciążeniem.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: d16343864d9602d644b31d34a2b66e39211b6ece
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079343"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>Jak wybrać między zainicjowaną przepływność a bezserwerową
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB jest dostępny w dwóch różnych trybach pojemności: [zainicjowana przepływność](set-throughput.md) i [bezserwerowy](serverless.md). Można wykonać dokładnie te same operacje bazy danych w obu trybach, ale sposób rozliczania dla tych operacji jest radykalnie różny. W poniższym filmie wideo objaśniono podstawowe różnice między tymi trybami i jak dopasowują się do różnych typów obciążeń:

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

> [!NOTE]
> Bezserwerowe jest obecnie obsługiwane tylko w interfejsie API Azure Cosmos DB Core (SQL).

## <a name="detailed-comparison"></a>Szczegółowe porównanie

| Kryteria | Aprowizowana przepływność | Praca bezserwerowa |
| --- | --- | --- |
| Stan | Ogólnie dostępne | W wersji zapoznawczej |
| Najlepiej dopasowane do | Obciążenia o krytycznym znaczeniu wymagające przewidywalnej wydajności | Małe i średnie obciążenia niekrytyczne z lekkim ruchem |
| Jak to działa | W przypadku każdego kontenera należy udostępnić pewną ilość przepływności wyrażoną w [jednostkach żądań](request-units.md) na sekundę. Co sekundę ta liczba jednostek żądania jest dostępna dla operacji bazy danych. Tymczasową przepływność można aktualizować ręcznie lub automatycznie korygować przy użyciu funkcji automatycznego [skalowania](provision-throughput-autoscale.md). | Uruchamiasz operacje bazy danych względem kontenerów bez konieczności aprowizacji żadnej pojemności. |
| Ograniczenia na konto | Maksymalna liczba regionów świadczenia usługi Azure: bez ograniczeń | Maksymalna liczba regionów świadczenia usługi Azure: 1 |
| Ograniczenia na kontener | Maksymalna przepływność: bez ograniczeń<br>Maksymalna ilość miejsca do magazynowania: bez ograniczeń | Maksymalna przepływność: 5 000 RU/s<br>Maksymalna ilość miejsca do magazynowania: 50 GB |
| Wydajność | 99,99% do 99,999% dostępności objętej umową SLA<br>< 10 ms opóźnienia do odczytu punktów i zapisów objętych umową SLA<br>99,99% gwarantowaną przepływność objętą umową SLA | 99,9% do 99,99% dostępności objętej umową SLA<br>< 10 ms opóźnienia do odczytu punktów i < 30 ms dla zapisów objętych przez cel SLO<br>95% możliwości przenoszenia objętej umową SLO |
| Model rozliczania | Opłaty są naliczane za godzinę, dla których Zainicjowano obsługę RU, niezależnie od liczby użytych jednostek ru. | Opłaty są naliczane za godzinę dla ilości jednostek ru zużytych przez operacje bazy danych. |

> [!IMPORTANT]
> Niektóre ograniczenia bezserwerowe mogą być rozładowaniu lub usuwane, gdy bezserwerowy staną się ogólnie dostępne i **Twoje opinie** pomogą nam podjąć decyzję! Skontaktuj się z nami i przekaż nam więcej informacji na temat środowiska bezserwerowego: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com) .

## <a name="burstability-and-expected-consumption"></a>Możliwości przenoszenia i oczekiwanie na użycie

W niektórych sytuacjach może zostać wyczyszczone, czy dla danego obciążenia należy wybrać zainicjowaną przepływność lub bezserwerowy. Aby pomóc w tej decyzji, można oszacować:

- **Wymagana pojemność** obciążenia, czyli maksymalna ilość jednostek ru, które mogą być używane w jednej sekundzie
- Twoje całkowite **oczekiwane użycie** , czyli łączna liczba jednostek ru, które można wykorzystać w ciągu miesiąca (można oszacować za pomocą tabeli pokazanej w [tym miejscu](plan-manage-costs.md#estimating-serverless-costs))

Jeśli obciążenie wymaga serii powyżej 5 000 RU na sekundę, należy wybrać zainicjowaną przepływność, ponieważ kontenery bezserwerowe nie mogą przekroczyć tego limitu. W przeciwnym razie można porównać koszt obu trybów na podstawie oczekiwanego użycia.

**Przykład 1** : oczekiwano obciążenia na maksymalnie 10 000 ru/s i zużywamy łącznie 20 000 000 jednostek RU w ciągu miesiąca.

- Tylko tymczasowy Tryb przepływności może zapewnić przepływność wynoszącą 10 000 RU/s

**Przykład 2** : oczekiwano obciążenia na maksymalnie 500 ru/s i zużywamy łącznie 20 000 000 jednostek RU w ciągu miesiąca.

- W trybie elastycznej przepływności należy udostępnić kontener z 500 RU/s dla miesięcznego kosztu: $0,008 * 5 * 730 = **$29,20**
- W trybie bezserwerowym płacisz za zużytą jednostek ru: $0,25 * 20 = **$5,00**

**Przykład 3** : oczekiwano obciążenia na maksymalnie 500 ru/s i zużywamy łącznie 250 000 000 jednostek RU w ciągu miesiąca.

- W trybie elastycznej przepływności należy udostępnić kontener z 500 RU/s dla miesięcznego kosztu: $0,008 * 5 * 730 = **$29,20**
- W trybie bezserwerowym płacisz za zużytą jednostek ru: $0,25 * 250 = **$62,50**

(te przykłady nie dotyczą kosztu magazynu, który jest taki sam jak w obu trybach)

> [!NOTE]
> Koszty pokazane w poprzednim przykładzie służą wyłącznie do celów demonstracyjnych. Aby uzyskać najnowsze informacje o cenach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/) .

## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej [na temat przepływności aprowizacji na Azure Cosmos DB](set-throughput.md)
- Przeczytaj więcej o [Azure Cosmos DB bezserwerowym](serverless.md)
- Zapoznaj się z koncepcją [jednostek żądania](request-units.md)
