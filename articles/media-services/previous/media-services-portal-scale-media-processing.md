---
title: Skalowanie przetwarzania multimediów przy użyciu Azure Portal | Microsoft Docs
description: Ten samouczek przeprowadzi Cię przez kroki skalowania przetwarzania multimediów przy użyciu Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: b9e8ffb3173ed0d25599be446611ceca72c2ef82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89266871"
---
# <a name="change-the-reserved-unit-type"></a>Zmiana typu jednostki zarezerwowanej

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Omówienie

Konto usługi Media Services jest skojarzone z typem jednostki zarezerwowanej określającym szybkość, z jaką są przetwarzane zadania przetwarzania multimediów. Można wybrać następujące typy jednostek zarezerwowanych: **S1**, **S2**lub **S3**. Na przykład to samo zadanie kodowania jest wykonywane szybciej przy użyciu typu jednostki zarezerwowanej **S2** niż w przypadku użycia typu **S1**.

Oprócz określania typu jednostki zarezerwowanej możesz określić, aby udostępnić konto za pomocą **jednostek zarezerwowanych** (jednostek ru). Liczba zainicjowanych jednostek zarezerwowanych określa liczbę zadań multimedialnych, które mogą być przetwarzane jednocześnie w ramach danego konta.

>[!NOTE]
>Jednostki zarezerwowane przekształcają wszystkie operacje przetwarzania multimediów do postaci równoległej, uwzględniając zadania Indeksowania za pomocą usługi Azure Media Indexer. Jednak w przeciwieństwie do kodowania zadania indeksowania nie są przetwarzane szybciej przy użyciu szybszych jednostek zarezerwowanych.

> [!IMPORTANT]
> Zapoznaj się z tematem [Omówienie](media-services-scale-media-processing-overview.md) , aby uzyskać więcej informacji na temat skalowania przetwarzania multimediów.
> 
> 

## <a name="scale-media-processing"></a>Skalowanie przetwarzania multimediów
Aby zmienić typ jednostki zarezerwowanej i liczbę jednostek zarezerwowanych, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. W oknie **Ustawienia** wybierz pozycję **jednostki zarezerwowane multimediów**.
   
    Aby zmienić liczbę jednostek zarezerwowanych dla wybranego typu jednostki zarezerwowanej, użyj suwaka **jednostki obsługiwane Media** w górnej części ekranu.
   
    Aby zmienić **Typ jednostki zarezerwowanej**, kliknij pozycję **szybkość zarezerwowanych jednostek przetwarzania** . Następnie wybierz wymaganą warstwę cenową: S1, S2 lub S3.
   
3. Kliknij przycisk ZAPISZ, aby zapisać zmiany.
   
    Nowe zarezerwowane jednostki są przypisywane po naciśnięciu przycisku Zapisz.

## <a name="next-steps"></a>Następne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
