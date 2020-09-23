---
title: Wymagania dotyczące schematu danych
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: a64bb5b28a06d9a013d59e022047f5e2841126ab
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940135"
---
Monitor metryk to usługa służąca do wykrywania anomalii w szeregach czasowych, diagnostyki i analizy. Jako usługa oparta na formacie AI używa danych do uczenia używanego modelu. Usługa akceptuje tabele zagregowanych danych z następującymi kolumnami:

* **Miara** (wymagana): co najmniej jedna kolumna zawierająca wartości liczbowe.
* **Znacznik czasu** (opcjonalnie): zero lub jedna kolumna z typem `DateTime` lub `String` . Gdy ta kolumna nie jest ustawiona, sygnatura czasowa jest ustawiana jako czas rozpoczęcia każdego okresu pozyskiwania. Sformatuj sygnaturę czasową w: `yyyy-MM-ddTHH:mm:ssZ` . 
  * **Sygnatura czasowa powinna być zgodna z szczegółowością metryki. Na przykład Metryka dzienna powinna zapewnić godzinę, minutę i sekundę dla sygnatury czasowej oznaczonej `00:00:00` jako **.
* **Wymiar** (opcjonalnie): kolumny mogą być dowolnego typu danych. Należy zachować ostrożność podczas pracy z dużymi ilościami kolumn i wartości, aby zapobiec przetwarzaniu nadmiernej liczby wymiarów.

> [!Note]
> Dla każdej metryki powinna istnieć tylko jedna sygnatura czasowa na miarę, odpowiadająca jednej kombinacji wymiaru. Agreguj dane przed dołączeniem lub użyj zapytania, aby określić dane do pozyskania.