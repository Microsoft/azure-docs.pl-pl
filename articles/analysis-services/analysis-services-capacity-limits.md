---
title: Azure Analysis Services limity zasobów i obiektów | Microsoft Docs
description: W tym artykule opisano limity zasobów i obiektów dla serwera Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f309c9863eb2f3065251537380a2977839f990d8
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73573209"
---
# <a name="analysis-services-resource-and-object-limits"></a>Analysis Services limity zasobów i obiektów

W tym artykule opisano limity zasobów i obiektów modelu.

## <a name="tier-limits"></a>Limity warstwy

Aby uzyskać QPU i limity pamięci dla warstw deweloperskich, podstawowa i standardowa, zobacz [stronę z cennikiem Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Limity obiektów

Te limity są teoretyczne. Wydajność zostanie obniżona przy mniejszej liczbie.

|Obiekt|Maksymalne rozmiary/liczby|  
|------------|----------------------------|  
|Bazy danych w wystąpieniu|16 000|  
|Łączna liczba tabel i kolumn w bazie danych|16 000|  
|Wiersze w tabeli|Nieograniczona liczba<br /><br /> **Ostrzeżenie:** Ograniczenie, że żadna Pojedyncza kolumna w tabeli nie może mieć więcej niż 1 999 999 997 odrębnych wartości.|  
|Hierarchie w tabeli|15 999|  
|Poziomy w hierarchii|15 999|  
|Relacje|8000|  
|Kolumny kluczy we wszystkich tabelach|15 999|  
|Miary w tabelach|2 ^ 31-1 = 2 147 483 647|  
|Komórki zwrócone przez zapytanie|2 ^ 31-1 = 2 147 483 647|  
|Rozmiar rekordu zapytania źródłowego|64 K|  
|Długość nazw obiektów|512 znaków|  


