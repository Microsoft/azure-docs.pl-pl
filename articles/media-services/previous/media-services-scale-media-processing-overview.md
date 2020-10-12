---
title: Informacje o jednostkach zarezerwowanych multimediów | Microsoft Docs
description: Ten artykuł zawiera omówienie skalowania przetwarzania multimediów przy użyciu Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: 8867e680be0aba187daf83bc538dd47c582c71fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618509"
---
# <a name="media-reserved-units"></a>Jednostki zarezerwowane multimediów

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services umożliwia skalowanie przetwarzania multimediów przez Zarządzanie jednostkami zarezerwowanych multimediów (MRUs). MRU oferuje dodatkową pojemność obliczeniową wymaganą na potrzeby kodowania multimediów. Liczba MRUs określa szybkość, z jaką zadania multimedialne są przetwarzane i ile zadań multimedialnych może być przetwarzanych współbieżnie na koncie. Na przykład jeśli konto ma pięć MRUs i są zadania do przetworzenia, można uruchomić pięć zadań multimedialnych jednocześnie. Wszystkie pozostałe zadania zostaną dodane do kolejki i mogą być pobierane do przetwarzania sekwencyjnego po zakończeniu uruchomionego zadania. Każdy z ostatnio zainicjowanych zasobów umożliwia rezerwację pojemności, ale nie zapewnia dedykowanego zasobu. W przypadku bardzo dużego popytu wszystkie MRUs mogą nie rozpoczynać przetwarzania natychmiast.

## <a name="choosing-between-different-reserved-unit-types"></a>Wybieranie między różnymi typami jednostek zarezerwowanych

Poniższa tabela ułatwia podejmowanie decyzji podczas wybierania różnych szybkości kodowania.  Pokazuje on czas trwania kodowania dla 7 minut, 1080p wideo w zależności od użytych ostatnio używanych elementów.

|Typ RU|Scenariusz|Przykładowe wyniki dla wideo z 7 min 1080p |
|---|---|---|
| **S1**|Kodowanie pojedynczej szybkości transmisji bitów. <br/>Pliki o wartościach SD lub niższych, a nie czasowe, niskie koszty.|Kodowanie do pliku MP4 o pojedynczej szybkości transmisji bitów przy użyciu polecenia "wielokrotna H264 Single szybkość transmisji bitów 16x9" zajmuje około 7 minut.|
| **S2**|Pojedyncza szybkość transmisji bitów i kodowanie wielu szybkości transmisji bitów.<br/>Normalne użycie zarówno dla kodowania SD, jak i HD.|Kodowanie z ustawieniem "wielokrotna H264 Single szybkość transmisji bitów 720" zajmuje około 6 minut.<br/><br/>Kodowanie za pomocą ustawienia "wielokrotna H264 o wielu szybkościach transmisji bitów" zajmuje około 12 minut.|
| **S3**|Pojedyncza szybkość transmisji bitów i kodowanie wielu szybkości transmisji bitów.<br/>Pełne wideo o rozdzielczości HD i 4K. Czuły czasowo, szybsze kodowanie szybkością oferowaną.|Kodowanie z ustawieniem "wielokrotna H264 Single szybkość transmisji bitów 1080p" zajmuje około 3 minuty.<br/><br/>Kodowanie za pomocą ustawienia "wielokrotna H264 Multiple szybkość transmisji bitów 1080p" zajmuje około 8 minut.|

> [!NOTE]
> Jeśli nie planujesz używania MRU dla Twojego konta, zadania multimedialne zostaną przetworzone z wydajnością, a zadania zostaną pobrane sekwencyjnie. Żadna pojemność przetwarzania nie jest zarezerwowana, więc czas oczekiwania między kolejnymi zadaniami zostanie zależał od dostępności zasobów w systemie.

## <a name="considerations"></a>Zagadnienia do rozważenia

* W przypadku zadań analizy audio i analizy wideo, które są wyzwalane przez Media Services v3 lub Video Indexer, zdecydowanie zaleca się aprowizacji konta z dziesięciu jednostkami S3. Jeśli potrzebujesz więcej niż 10 MRUs S3, Otwórz bilet pomocy technicznej przy użyciu [Azure Portal](https://portal.azure.com/).
* W przypadku zadań kodowania, które nie mają MRUs, nie istnieje górny limit czasu, w którym zadania mogą być spędzane w stanie umieszczonym w kolejce i co najwyżej jedno zadanie będzie uruchamiane w danym momencie.

## <a name="billing"></a>Rozliczenia

Opłata jest naliczana na podstawie liczby minut, w ciągu których zainicjowano alokację jednostek zarezerwowanych multimediów na Twoim koncie. Zdarza się to niezależnie od tego, czy na Twoim koncie są uruchomione jakieś zadania. Szczegółowe wyjaśnienie można znaleźć w sekcji często zadawane pytania na stronie [cennika Media Services](https://azure.microsoft.com/pricing/details/media-services/) .

## <a name="quotas-and-limitations"></a>Limity przydziału i ograniczenia

Aby uzyskać informacje na temat przydziałów i ograniczeń oraz sposobu otwierania biletu pomocy technicznej, zobacz [limity przydziału i ograniczenia](media-services-quotas-and-limitations.md).

## <a name="next-steps"></a>Następne kroki

Wypróbuj Skalowanie przetwarzania multimediów przy użyciu jednej z następujących technologii:

[Platforma .NET](media-services-dotnet-encoding-units.md) 
 [Portal](media-services-portal-scale-media-processing.md) 
 [Rest](/rest/api/media/operations/encodingreservedunittype) 
 [Język Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples) 
 Język [php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)