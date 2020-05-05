---
title: Informacje o rabatach na rezerwacje dla usługi Azure Disk Storage
description: Dowiedz się, jak rabat na zarezerwowane dyski platformy Azure jest stosowany do dysków zarządzanych SSD w warstwie Azure Premium.
author: roygara
ms.author: rogarana
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 3541e3f12a6d0b1beecdd67821d31f71b47f6c68
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509469"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Omówienie stosowania rabatu za rezerwację do usługi Azure Disk Storage

Po zakupie pojemności zarezerwowanej dysku na platformie usługi Azure rabat za rezerwację jest stosowany automatycznie do zasobów dyskowych spełniających warunki rezerwacji. Rabat za rezerwację dotyczy tylko do jednostek SKU dysków. Opłaty za migawki dysku są naliczane według stawek płatności zgodnie z rzeczywistym użyciem.

Aby uzyskać więcej informacji na temat rezerwacji dysków platformy Azure, zobacz [Oszczędzanie kosztów dzięki rezerwacji dysków platformy Azure](../../virtual-machines/linux/disks-reserved-capacity.md). Aby uzyskać informacje na temat cennika rezerwacji dysków platformy Azure, zobacz [Cennik dysków zarządzanych platformy Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="how-the-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Rabat za rezerwację dysków platformy Azure jest dostępny na zasadzie „wykorzystaj lub strać”. Jest on stosowany do zasobów dysków zarządzanych co godzinę. Jeśli przez daną godzinę nie masz żadnych zasobów dysków zarządzanych, które spełniają warunki rezerwacji, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin nie można przenieść na później.

Po usunięciu zasobu rabat za rezerwację jest automatycznie stosowany do innego pasującego zasobu w określonym zakresie. Jeśli nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną utracone.

## <a name="discount-examples"></a>Przykłady rabatów

W poniższych przykładach pokazano, jak jest stosowany rabat za rezerwację dysków platformy Azure w zależności od wdrożenia.

Załóżmy, że zakupiono i zarezerwowano 100 dysków P30 w regionie Zachodnie stany USA 2 na okres jednego roku. Każdy dysk ma około 1 TiB magazynu. Załóżmy, że koszt tej przykładowej rezerwacji to 140 100 USD. Możesz zapłacić całą kwotę z góry lub płacić stałe miesięczne raty w wysokości 11 675 USD przez kolejnych 12 miesięcy.

W poniższych scenariuszach opisano, co się stanie w przypadku niepełnego lub nadmiernego wykorzystania pojemności zarezerwowanej bądź podzielenia jej na warstwy. W tych przykładach założono, że wybrano plan płatności miesięcznych za rezerwację.

### <a name="underusing-your-capacity"></a>Niepełne wykorzystanie pojemności

Załóżmy, że przez godzinę w okresie rezerwacji wdrażasz tylko 99 ze 100 zarezerwowanych dysków SSD P30 platformy Azure w warstwie Premium. Pozostały dysk P30 nie jest stosowany w ciągu tej godziny. Niewykorzystana ilość rezerwacji nie jest przenoszona.

### <a name="overusing-your-capacity"></a>Nadmierne wykorzystanie pojemności

Załóżmy, że przez godzinę w okresie rezerwacji jest używanych 101 dysków SSD P30 w warstwie Premium. Rabat za rezerwację dotyczy tylko do 100 dysków P30. Opłaty za pozostały dysk P30 są naliczane według stawek płatności zgodnie z rzeczywistym użyciem za tę godzinę. Jeśli w ciągu następnej godziny użycie zmaleje do 100 dysków P30, rezerwacja obejmie całe użycie.

### <a name="tiering-your-capacity"></a>Dzielenie pojemności na warstwy

Załóżmy, że o danej godzinie w okresie rezerwacji chcesz używać łącznie 200 dysków SSD P30 w warstwie Premium. Załóżmy również, że przez pierwsze 30 minut używasz tylko 100 dysków. W tym okresie użycie jest w pełni pokryte, ponieważ zgłoszono rezerwację dla 100 dysków P30. Jeśli potem przestaniesz używać pierwszych 100 (czyli nie będziesz używać żadnych), a następnie zaczniesz używać innych 100 przez pozostałe 30 minut, to użycie jest również pokryte rezerwacją.

![Przykłady niepełnego i nadmiernego wykorzystania pojemności oraz dzielenia jej na warstwy](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Obniżenie kosztów dzięki rezerwacji dysków platformy Azure (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Obniżenie kosztów dzięki rezerwacji dysków platformy Azure (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
