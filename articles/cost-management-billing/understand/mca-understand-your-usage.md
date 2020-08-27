---
title: Terminy w pliku użycia i opłat dla umowy klienta firmy Microsoft
description: Dowiedz się, jak interpretować sekcje pliku CSV dotyczącego użycia i opłat platformy Azure dla Twojego profilu rozliczeniowego.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 826217091b3297da0c068336ff67a0a58841716d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690463"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Terminy w pliku użycia i opłat platformy Azure dla umowy klienta firmy Microsoft

Ten artykuł dotyczy konta rozliczeniowego związanego z umową klienta firmy Microsoft. [Sprawdź, czy masz dostęp do umowy klienta firmy Microsoft](#check-access-to-a-microsoft-customer-agreement).

Plik CSV dotyczący użycia i opłat platformy Azure zawiera dzienne oraz powiązane z miernikiem opłaty za użycie dla bieżącego okresu rozliczeniowego.

Aby uzyskać plik użycia i opłat platformy Azure, zobacz [Wyświetlanie i pobieranie użycia i opłat platformy Azure dla umowy klienta firmy Microsoft](download-azure-daily-usage.md). Jest on dostępny w formacie pliku z wartościami rozdzielanymi przecinkami (CSV), który można otworzyć w aplikacji obsługującej arkusze kalkulacyjne.

Opłaty za użycie to całkowite opłaty **miesięczne** w ramach subskrypcji. Opłaty za użycie nie uwzględniają żadnych środków na korzystanie ani rabatów.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Zmiany w stosunku do użycia i opłat dla umowy EA platformy Azure

Jeśli jesteś klientem z umową EA, możesz zauważyć, że terminy w pliku CSV użycia profilu rozliczeniowego platformy Azure różnią się od terminów w pliku CSV użycia dla umowy EA platformy Azure. Oto mapowanie terminów dotyczących użycia dla umowy EA na terminy dotyczące użycia w profilu rozliczeniowym:

| Plik CSV użycia dla umowy EA platformy Azure | Plik CSV użycia i opłat dla umowy klienta firmy Microsoft |
| --- | --- |
| Data | date |
| Month| date |
| Day | date |
| Year | date |
| Product (Produkt) | product |
| Identyfikator miernika | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | quantity |
| ResourceRate | effectivePrice |
| ExtendedCost | cost |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| Tagi | tags |
| StoreServiceIdentifier | Nie dotyczy |
| DepartmentName | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>Szczegółowe terminy i opisy

Poniższe terminy są wyświetlane w pliku użycia i opłat platformy Azure.

Okres | Opis
--- | ---
invoiceId | Unikatowy identyfikator dokumentu w pliku PDF faktury
previousInvoiceId | Odwołanie do oryginalnej faktury, jeśli ten element wiersza to zwrot kosztów
billingAccountName | Nazwa konta rozliczeniowego
billingAccountId | Unikatowy identyfikator głównego konta rozliczeniowego
billingProfileId | Nazwa profilu rozliczeniowego, który nalicza fakturowane opłaty
billingProfileName | Unikatowy identyfikator profilu rozliczeniowego, który nalicza fakturowane opłaty
invoiceSectionId | Unikatowy identyfikator sekcji faktury
invoiceSectionName | Nazwa sekcji faktury
costCenter | Centrum kosztów zdefiniowane w ramach subskrypcji na potrzeby śledzenia kosztów (dostępne tylko w otwartych okresach rozliczeniowych)
billingPeriodStartDate | Data rozpoczęcia okresu rozliczeniowego, dla którego generowana jest faktura
billingPeriodEndDate | Data zakończenia okresu rozliczeniowego, dla którego generowana jest faktura
servicePeriodStartDate | Data rozpoczęcia okresu oceny, który ma zdefiniowany i zablokowany cennik dla użytej lub zakupionej usługi
servicePeriodEndDate | Data zakończenia okresu oceny, który ma zdefiniowany i zablokowany cennik dla użytej lub zakupionej usługi
date | W przypadku opłat za użycie platformy Azure i portalu Marketplace jest to data oceny. W przypadku zakupów jednorazowych (usługa Reservations, portal Marketplace) lub stałych opłat cyklicznych (oferty pomocy technicznej) jest to data zakupu.
serviceFamily | Rodzina usług, do której należy usługa
productOrderId | Unikatowy identyfikator zamówienia produktu
productOrderName | Unikatowa nazwa zamówienia produktu
consumedService | Nazwa użytej usługi
meterId | Unikatowy identyfikator miernika
meterName | Nazwa miernika
meterCategory | Nazwa kategorii klasyfikacji dla miernika. Na przykład *Usługi w chmurze*, *Sieć* itp.
meterSubCategory | Nazwa kategorii klasyfikacji podrzędnej miernika
meterRegion | Nazwa regionu, w którym jest dostępny miernik usługi. Określa lokalizację centrum danych pewnych usług, które są wyceniane na podstawie lokalizacji centrum danych.
offer | Nazwa zakupionej oferty
PayGPrice | Cena detaliczna zasobu.
PricingModel | Identyfikator wskazujący, jak jest naliczana opłata za licznik (wartości: Na żądanie, Rezerwacja, Spot)
productId | Unikatowy identyfikator produktu, za który naliczane są opłaty
product | Nazwa produktu, za który naliczane są opłaty
subscription ID | Unikatowy identyfikator subskrypcji, za którą naliczane są opłaty
subscriptionName | Nazwa subskrypcji, za którą naliczane są opłaty
reservationId | Unikatowy identyfikator zakupionego wystąpienia rezerwacji
reservationName | Nazwa zakupionego wystąpienia rezerwacji
publisherType | Typ wydawcy (wartości: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Wydawca dla usług Marketplace
resourceGroupId | Unikatowy identyfikator grupy zasobów skojarzonej z zasobem
resourceGroupName | Nazwa grupy zasobów skojarzonej z zasobem
resourceId | Unikatowy identyfikator wystąpienia zasobu
resourceType | Typ wystąpienia zasobu
resourceLocation | Określa lokalizację centrum danych, w którym jest uruchomiany zasób.
location | Znormalizowana lokalizacja zasobu, jeśli dla tych samych regionów skonfigurowano różne lokalizacje zasobów
quantity | Liczba jednostek zakupionych lub użytych
unitOfMeasure | Jednostka miary dla rozliczeń usługi. Na przykład opłaty za usługi obliczeniowe są naliczane godzinowo.
chargeType | Typ opłaty. Wartości: <ul><li>AsCharged-Usage: opłaty naliczane na podstawie użycia usługi platformy Azure. Obejmuje to użycie w odniesieniu do maszyn wirtualnych, za które nie są naliczane opłaty z powodu wystąpień zarezerwowanych.</li><li>AsCharged-PurchaseMarketplace: Jednorazowe lub stałe opłaty cykliczne za zakupy w portalu Azure Marketplace</li><li>AsCharged-UsageMarketplace: Opłaty za usługi w portalu Azure Marketplace, które są rozliczane na podstawie jednostek użycia</li></ul>
isAzureCreditEligible | Flaga wskazująca, czy opłata za usługę kwalifikuje się do zapłaty przy użyciu środków na korzystanie z platformy Azure (wartości: True, False)
serviceInfo1 | Metadane dotyczące konkretnej usługi
serviceInfo2 | Starsze pole, które zawiera opcjonalne metadane właściwe dla usługi
additionalInfo | Dodatkowe metadane dotyczące konkretnej usługi.
tags | Tagi, które przypisujesz do zasobu

### <a name="make-sure-that-charges-are-correct"></a>Upewnij się, że opłaty są poprawne

Jeśli chcesz się upewnić, że opłaty w szczegółowym pliku użycia są poprawne, możesz je sprawdzić. Zobacz [Informacje o opłatach na fakturze w profilu rozliczeniowym](review-customer-agreement-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Sprawdzanie dostępu do umowy klienta firmy Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie i pobieranie faktury platformy Microsoft Azure](download-azure-invoice.md)
- [Wyświetlanie i pobieranie danych na temat użycia i opłat na platformie Microsoft Azure](download-azure-daily-usage.md)
