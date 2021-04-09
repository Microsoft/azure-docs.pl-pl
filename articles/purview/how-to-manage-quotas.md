---
title: Zarządzanie zasobami i przydziałami
titleSuffix: Azure Purview
description: Dowiedz się więcej o przydziałach i limitach zasobów dla usługi Azure kontrolą oraz o sposobach zwiększania limitu przydziału.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 57cb1c405e00acb346421d64190a71e9211d21ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96938838"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>Zarządzanie przydziałami i zwiększanie przydziałów zasobów za pomocą usługi Azure kontrolą
 
Azure kontrolą to usługa w chmurze do użycia przez użytkowników danych. Za pomocą usługi Azure kontrolą można centralnie zarządzać nadzorem danych w ramach nieruchomości, obejmującą zarówno środowiska w chmurze, jak i w Premium. Usługa umożliwia analitykom biznesowym wyszukiwanie odpowiednich danych przy użyciu zrozumiałych warunków firmy. Aby podnieść limity do wartości maksymalnej dla subskrypcji, skontaktuj się z pomocą techniczną.
 
## <a name="azure-purview-limits"></a>Limity usługi Azure Purview
 
|**Zasób**|  **Limit domyślny**  |**Limit maksymalny**|
|---|---|---|
|Kontrolą kont na region na dzierżawcę (wszystkie subskrypcje są połączone)|3|Kontakt z pomocą techniczną|
|Rdzeni wirtualnych dostępne do skanowania na konto *|160|160|
|Współbieżne skany dla poszczególnych kont w danym punkcie. Limit jest oparty na typie skanowanych źródeł danych *|5 | 10 |
|Maksymalny czas uruchomienia skanowania|7 dni|7 dni|
|Wywołania interfejsu API dla konta|10 mln interfejsy API/miesiąc dla rozmiaru platformy dla 4 jednostek pojemności. <br>40M interfejsy API/miesiąc dla 16 jednostek wydajności rozmiar platformy |10 mln interfejsy API/miesiąc dla rozmiaru platformy dla 4 jednostek pojemności. <br>40M interfejsy API/miesiąc dla 16 jednostek wydajności rozmiar platformy|
|Magazyn, na konto|10 GB dla 4 jednostek pojemności rozmiar platformy. <br>40 GB na 16 jednostek pojemności — rozmiar platformy |10 GB dla 4 jednostek pojemności rozmiar platformy. <br> 40 GB na 16 jednostek pojemności — rozmiar platformy |
|Rozmiar zasobów na konto|100 mln zasoby fizyczne |Kontakt z pomocą techniczną|
|Maksymalny rozmiar zasobu w wykazie|2 MB|2 MB|
|Maksymalna długość nazwy zasobu i nazwy klasyfikacji|4 KB|4 KB|
|Maksymalna długość nazwy i wartości właściwości zasobu|32 KB|32 KB|
|Maksymalna długość nazwy i wartości atrybutu klasyfikacji|32 KB|32 KB|
|Maksymalna liczba terminów słownika, na konto|100 000|100 000|
 
* Własne scenariusze środowiska Integration Runtime wykraczają poza zakres limitów zdefiniowanych w powyższej tabeli. 
 
## <a name="next-steps"></a>Następne kroki
 
> [!div class="nextstepaction"]
>[Samouczek: skanowanie danych za pomocą usługi Azure kontrolą](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[Samouczek: nawigowanie po stronie głównej i wyszukiwanie elementu zawartości](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[Samouczek: przeglądanie zasobów i wyświetlanie ich pochodzenia](tutorial-browse-and-view-lineage.md)
