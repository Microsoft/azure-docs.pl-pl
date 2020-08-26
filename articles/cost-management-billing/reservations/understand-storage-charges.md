---
title: Omówienie stosowania rabatu za rezerwację do usługi Azure Storage | Microsoft Docs
description: Dowiedz się, w jaki sposób rabat za pojemność zarezerwowaną usługi Azure Storage jest stosowany do zasobów usługi Azure Data Lake Storage Gen2 i blokowych obiektów blob.
author: tamram
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: tamram
ms.openlocfilehash: fc1110c124f5e4e6d30c27de0911f86c186b67c2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681638"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-storage"></a>Omówienie stosowania rabatu za rezerwację do usługi Azure Storage

Po zakupie pojemności zarezerwowanej usługi Azure Storage rabat za rezerwację jest automatycznie stosowany do zasobów usługi Azure Data Lake Storage Gen2 i blokowych obiektów blob spełniających warunki rezerwacji. Rabat za rezerwację jest stosowany tylko do pojemności magazynu. Opłaty za przepustowość i liczbę żądań są naliczane zgodnie z rzeczywistym użyciem.

Aby uzyskać więcej informacji na temat pojemności zarezerwowanej usługi Azure Storage, zobacz [Optymalizowanie kosztów magazynu obiektów blob przy użyciu pojemności zarezerwowanej](../../storage/blobs/storage-blob-reserved-capacity.md).

Aby uzyskać informacje na temat cen rezerwacji usługi Azure Storage, zobacz [Ceny blokowych obiektów blob](https://azure.microsoft.com/pricing/details/storage/blobs/) i [Cennik usługi Azure Data Lake Storage Gen 2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="how-the-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Rabat za pojemność zarezerwowaną usługi Azure Storage jest stosowany godzinowo do zasobów usługi Azure Data Lake Storage Gen2 i blokowych obiektów blob.

Rabat za pojemność zarezerwowaną usługi Azure Storage jest rabatem typu „wykorzystaj lub strać”. Jeśli nie masz żadnych zasobów usługi Azure Data Lake Storage Gen2 lub blokowych obiektów blob spełniających warunki rezerwacji przez określoną godzinę, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.

Po usunięciu zasobu rabat za rezerwację jest automatycznie stosowany do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną utracone.

## <a name="discount-examples"></a>Przykłady rabatów

W poniższych przykładach pokazano, jak jest stosowany rabat za pojemność zarezerwowaną usługi Azure Storage w zależności od wdrożeń.

Załóżmy, że zakupiono 100 TB pojemności zarezerwowanej w regionie Zachodnie stany USA 2 na okres 1 roku. Rezerwacja dotyczy magazynu lokalnie nadmiarowego (LRS) w warstwie dostępu Gorąca.

Załóżmy, że koszt tej przykładowej rezerwacji to 18 540 USD. Możesz zapłacić całą kwotę z góry lub płacić stałe miesięczne raty w wysokości 1545 USD przez kolejnych 12 miesięcy.

W tych przykładach założono, że wybrano plan płatności miesięcznych za rezerwację. W poniższych scenariuszach opisano, co się stanie w przypadku niepełnego lub nadmiernego wykorzystania pojemności zarezerwowanej.

### <a name="underusing-your-capacity"></a>Niepełne wykorzystanie pojemności

Załóżmy, że w ciągu danej godziny w okresie rezerwacji użyto tylko 80 TB ze 100 TB pojemności zarezerwowanej. Pozostałe 20 TB nie jest stosowane przez tę godzinę i nie jest przenoszone.

### <a name="overusing-your-capacity"></a>Nadmierne wykorzystanie pojemności

Załóżmy, że w ciągu danej godziny w okresie rezerwacji użyto 101 TB pojemności magazynu. Rabat za rezerwację jest stosowany do 100 TB danych, a opłata za pozostały 1 TB jest naliczana przez tę godzinę według stawek płatności zgodnie z rzeczywistym użyciem. Jeśli w ciągu następnej godziny użycie zmieni się do 100 TB, rezerwacja obejmie całe użycie.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Optymalizowanie kosztów magazynu obiektów blob przy użyciu pojemności zarezerwowanej](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
