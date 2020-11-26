---
title: Wymagania dotyczące schematu danych
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 4c55c25621df1925b6ed6c374d8af88551eb1e46
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96231433"
---
Doradca metryk to usługa służąca do wykrywania anomalii w szeregach czasowych, diagnostyki i analizy. Jako usługa oparta na formacie AI używa danych do uczenia używanego modelu. Usługa akceptuje tabele zagregowanych danych z następującymi kolumnami:

* **Miara** (wymagana): co najmniej jedna kolumna zawierająca wartości liczbowe.
* **Znacznik czasu** (opcjonalnie): zero lub jedna kolumna z typem `DateTime` lub `String` . Gdy ta kolumna nie jest ustawiona, sygnatura czasowa jest ustawiana jako czas rozpoczęcia każdego okresu pozyskiwania. Sformatuj sygnaturę czasową w: `yyyy-MM-ddTHH:mm:ssZ` . 
  * **Sygnatura czasowa powinna być zgodna z szczegółowością metryki. Na przykład Metryka dzienna powinna zapewnić godzinę, minutę i sekundę dla sygnatury czasowej oznaczonej `00:00:00` jako**.
* **Wymiar** (opcjonalnie): kolumny mogą być dowolnego typu danych. Należy zachować ostrożność podczas pracy z dużymi ilościami kolumn i wartości, aby zapobiec przetwarzaniu nadmiernej liczby wymiarów.

> [!Note]
> Dla każdej metryki powinna istnieć tylko jedna sygnatura czasowa na miarę, odpowiadająca jednej kombinacji wymiaru. Agreguj dane przed dołączeniem lub użyj zapytania, aby określić dane do pozyskania.