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
ms.openlocfilehash: c754efef02cdad6edbf047c5de9f1af6d758f137
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043214"
---
Monitor metryk to usługa służąca do wykrywania anomalii w szeregach czasowych, diagnostyki i analizy. Jako usługa oparta na formacie AI używa danych do uczenia używanego modelu. Usługa akceptuje tabele zagregowanych danych z następującymi kolumnami:

* **Miara** (wymagana): co najmniej jedna kolumna zawierająca wartości liczbowe.
* **Znacznik czasu** (opcjonalnie): zero lub jedna kolumna z typem `DateTime` lub `String` . Gdy ta kolumna nie jest ustawiona, sygnatura czasowa jest ustawiana jako czas rozpoczęcia każdego okresu pozyskiwania. Sformatuj sygnaturę czasową w: `yyyy-MM-ddTHH:mm:ssZ` . 
  * **Sygnatura czasowa powinna być zgodna z szczegółowością metryki. Na przykład Metryka dzienna powinna zapewnić godzinę, minutę i sekundę dla sygnatury czasowej oznaczonej `00:00:00` jako **.
* **Wymiar** (opcjonalnie): kolumny mogą być dowolnego typu danych. Należy zachować ostrożność podczas pracy z dużymi ilościami kolumn i wartości, aby zapobiec przetwarzaniu nadmiernej liczby wymiarów.

> [!Note]
> Dla każdej metryki powinna istnieć tylko jedna sygnatura czasowa na miarę, odpowiadająca jednej kombinacji wymiaru. Agreguj dane przed dołączeniem lub użyj zapytania, aby określić dane do pozyskania.