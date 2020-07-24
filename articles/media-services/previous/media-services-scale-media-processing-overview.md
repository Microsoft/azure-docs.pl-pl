---
title: Skalowanie przetwarzania multimediów — Omówienie | Microsoft Docs
description: Ten temat zawiera omówienie skalowania przetwarzania multimediów przy użyciu Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: 6d199271d20b9094ffaac0384863a404c7d0ed0b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87000010"
---
# <a name="scaling-media-processing-overview"></a>Skalowanie przetwarzania multimediów — omówienie 
Ta strona zawiera omówienie sposobów i przyczyn skalowania przetwarzania multimediów. 

## <a name="overview"></a>Omówienie
Konto usługi Media Services jest skojarzone z typem jednostki zarezerwowanej określającym szybkość, z jaką są przetwarzane zadania przetwarzania multimediów. Można wybrać następujące typy jednostek zarezerwowanych: **S1**, **S2**lub **S3**. Na przykład to samo zadanie kodowania jest wykonywane szybciej przy użyciu typu jednostki zarezerwowanej **S2** niż w przypadku użycia typu **S1**. Aby uzyskać więcej informacji, zobacz [typy jednostek zarezerwowanych](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Oprócz określania typu jednostki zarezerwowanej możesz określić, aby udostępnić konto za pomocą jednostek zarezerwowanych. Liczba zainicjowanych jednostek zarezerwowanych określa liczbę zadań multimedialnych, które mogą być przetwarzane jednocześnie w ramach danego konta. Na przykład jeśli konto ma pięć jednostek zarezerwowanych, pięć zadań multimedialnych będzie wykonywanych współbieżnie, o ile istnieją zadania do przetworzenia. Pozostałe zadania czekają w kolejce i zostaną pobrane do przetwarzania sekwencyjnego po zakończeniu uruchomionego zadania. Jeśli dla konta nie zainicjowano obsługi żadnych jednostek zarezerwowanych, zadania będą wybierane sekwencyjnie. W takim przypadku czas oczekiwania między kolejnymi zadaniami zostanie zależał od dostępności zasobów w systemie.

## <a name="choosing-between-different-reserved-unit-types"></a>Wybieranie między różnymi typami jednostek zarezerwowanych
Poniższa tabela ułatwia podejmowanie decyzji podczas wybierania różnych szybkości kodowania. Zawiera również kilka testów porównawczych [wideo, które można pobrać,](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) aby wykonać własne testy:

|Typ RU|Scenariusz|Przykładowe wyniki dla [wideo z 7 min 1080p](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Kodowanie pojedynczej szybkości transmisji bitów. <br/>Pliki o wartościach SD lub niższych, a nie czasowe, niskie koszty.|Kodowanie do pliku MP4 o pojedynczej szybkości transmisji bitów przy użyciu polecenia "wielokrotna H264 Single szybkość transmisji bitów 16x9" zajmuje około 7 minut.|
| **S2**|Pojedyncza szybkość transmisji bitów i kodowanie wielu szybkości transmisji bitów.<br/>Normalne użycie zarówno dla kodowania SD, jak i HD.|Kodowanie z ustawieniem "wielokrotna H264 Single szybkość transmisji bitów 720" zajmuje około 6 minut.<br/><br/>Kodowanie za pomocą ustawienia "wielokrotna H264 o wielu szybkościach transmisji bitów" zajmuje około 12 minut.|
| **S3**|Pojedyncza szybkość transmisji bitów i kodowanie wielu szybkości transmisji bitów.<br/>Pełne wideo o rozdzielczości HD i 4K. Czuły czasowo, szybsze kodowanie szybkością oferowaną.|Kodowanie z ustawieniem "wielokrotna H264 Single szybkość transmisji bitów 1080p" zajmuje około 3 minuty.<br/><br/>Kodowanie za pomocą ustawienia "wielokrotna H264 Multiple szybkość transmisji bitów 1080p" zajmuje około 8 minut.|

## <a name="considerations"></a>Zagadnienia do rozważenia
> [!IMPORTANT]
> Zapoznaj się z zagadnieniami opisanymi w tej sekcji.  
> 
> 

* W przypadku zadań analizy audio i analizy wideo, które są wyzwalane przez Media Services v3 lub Video Indexer, jest zdecydowanie zalecane.
* W przypadku używania puli udostępnionej, czyli bez żadnych jednostek zarezerwowanych, zadania kodowania mają taką samą wydajność jak w przypadku S1 jednostek ru. Nie istnieje jednak Górna granica czasu, w którym zadania mogą być spędzane w stanie umieszczonym w kolejce, a w dowolnym momencie tylko jedno zadanie zostanie uruchomione.

## <a name="billing"></a>Rozliczenia

Opłata jest naliczana na podstawie liczby minut, w ciągu których zainicjowano alokację jednostek zarezerwowanych multimediów na Twoim koncie. Zdarza się to niezależnie od tego, czy na Twoim koncie są uruchomione jakieś zadania. Szczegółowe wyjaśnienie można znaleźć w sekcji często zadawane pytania na stronie [cennika Media Services](https://azure.microsoft.com/pricing/details/media-services/) .   

## <a name="quotas-and-limitations"></a>Limity przydziału i ograniczenia
Aby uzyskać informacje na temat przydziałów i ograniczeń oraz sposobu otwierania biletu pomocy technicznej, zobacz [limity przydziału i ograniczenia](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Następny krok
Uzyskaj zadanie skalowania multimediów z jedną z następujących technologii: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Aby uzyskać najnowszą wersję zestawu SDK języka Java i zacząć programować w języku Java, zobacz [Rozpoczynanie korzystania z zestawu SDK klienta Java dla usług Azure Media Services](./media-services-java-how-to-use.md). <br/>
> Aby pobrać najnowszy zestaw SDK języka PHP dla usługi Media Services, poszukaj wersji 0.5.7 pakietu Microsoft/WindowAzure w [repozytorium Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
