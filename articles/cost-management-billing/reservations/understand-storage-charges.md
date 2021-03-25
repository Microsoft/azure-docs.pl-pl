---
title: Informacje na temat sposobu stosowania rabatów rezerwacji do usług Azure Storage | Microsoft Docs
description: Dowiedz się, w jaki sposób zarezerwowane rabaty pojemności są stosowane do zasobów usługi Azure Blob Storage, Azure Files i Azure Data Lake Storage Gen2.
author: tamram
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: tamram
ms.openlocfilehash: f18097f0da658b3a485b79f5279e9ecc54a41ea0
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024034"
---
# <a name="understand-how-reservation-discounts-are-applied-to-azure-storage-services"></a>Informacje na temat sposobu stosowania rabatów rezerwacji do usług Azure Storage 
Usługi Azure Storage pozwalają zaoszczędzić oszczędności na kosztach magazynowania dzięki zachowaniu pojemności. Usługi Azure Blob Storage, Azure Files i Azure Data Lake Storage Gen 2 obsługują wystąpienia rezerwowe. Po zakupie zarezerwowanej pojemności rabat rezerwacji jest automatycznie stosowany do zasobów magazynu, które pasują do warunków rezerwacji. Rabat za rezerwację jest stosowany tylko do pojemności magazynu. Opłaty za przepustowość i liczbę żądań są naliczane zgodnie z rzeczywistym użyciem.

Aby uzyskać więcej informacji na temat magazynu obiektów blob platformy Azure i Azure Data Lake magazynu generacji 2 zarezerwowanej pojemności, zobacz [Optymalizacja kosztów magazynu obiektów BLOB przy użyciu zarezerwowanej pojemności](../../storage/blobs/storage-blob-reserved-capacity.md). Aby uzyskać więcej informacji o Azure Files zarezerwowanej pojemności, zobacz [Optymalizacja kosztów dla Azure Files z zarezerwowaną pojemnością](../../storage/files/files-reserve-capacity.md).

Aby uzyskać informacje na temat cennika usługi Azure Blob Storage, zobacz temat [Zablokuj ceny obiektów BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/) i [Azure Data Lake Storage ceny generacji 2](https://azure.microsoft.com/pricing/details/storage/data-lake/). Aby uzyskać informacje o cenach rezerwacji magazynu Azure Files, zobacz [Azure Files cennika](https://azure.microsoft.com/pricing/details/storage/files).

## <a name="how-the-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację
Rabat zarezerwowanej pojemności jest stosowany do obsługiwanych zasobów magazynu co godzinę.

Rabat zarezerwowanej pojemności jest rabatem "Użyj go lub-tracą". Jeśli nie masz żadnych blokowych obiektów blob, udziałów plików platformy Azure ani zasobów Azure Data Lake Storage Gen2, które spełniają warunki rezerwacji przez daną godzinę, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.

Po usunięciu zasobu rabat za rezerwację jest automatycznie stosowany do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną utracone.

## <a name="discount-examples"></a>Przykłady rabatów
W poniższych przykładach pokazano, jak ma zastosowanie rabat zarezerwowany dla pojemności, w zależności od wdrożeń.

Załóżmy, że zakupiono 100 TiB zdolności zastrzeżonej w regionie zachodnie stany USA 2 przez okres 1 roku. Rezerwacja dotyczy magazynu obiektów BLOB lokalnie nadmiarowego (LRS) w warstwie dostępu gorąca.

Załóżmy, że koszt tej przykładowej rezerwacji to 18 540 USD. Możesz zapłacić całą kwotę z góry lub płacić stałe miesięczne raty w wysokości 1545 USD przez kolejnych 12 miesięcy.

W tych przykładach założono, że wybrano plan płatności miesięcznych za rezerwację. W poniższych scenariuszach opisano, co się stanie w przypadku niepełnego lub nadmiernego wykorzystania pojemności zarezerwowanej.

### <a name="underusing-your-capacity"></a>Niepełne wykorzystanie pojemności
Załóżmy, że w ciągu danej godziny w okresie rezerwacji użyto tylko 80 TiB z zastrzeżoną pojemności 100 TiB. Pozostałe 20 TiB nie jest stosowane dla tej godziny i nie jest przenoszone.

### <a name="overusing-your-capacity"></a>Nadmierne wykorzystanie pojemności
Załóżmy, że w ciągu danej godziny w okresie rezerwacji użyto 101 TiB pojemności magazynu. Rabat związany z rezerwacją dotyczy 100 TiB danych, a pozostałe 1 TiB jest naliczana według stawek płatność zgodnie z rzeczywistym użyciem dla tej godziny. Jeśli w następnej godzinie użycie zmieni się na 100 TiB, wszystkie użycie będzie objęte rezerwacją.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami
Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- [Optymalizowanie kosztów magazynu obiektów blob przy użyciu pojemności zarezerwowanej](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Optymalizacja kosztów Azure Files z zarezerwowaną pojemnością](../../storage/files/files-reserve-capacity.md)
- [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
