---
title: Skalowanie przetwarzania multimediów przez dodawanie jednostek kodowania — Azure |  Microsoft Docs
description: W tym artykule pokazano, jak dodać jednostki kodowania za pomocą Azure Media Services .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: milangada
ms.custom: devx-track-csharp
ms.openlocfilehash: 2d5e6305de1852dad3a08c110cb2fc5a58a31049
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "96009201"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Skalowanie kodowania za pomocą zestawu .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Omówienie
> [!IMPORTANT]
> Zapoznaj się z [omówieniem](media-services-scale-media-processing-overview.md) , aby uzyskać więcej informacji na temat skalowania przetwarzania multimediów.
> 
> 

Aby zmienić typ jednostki zarezerwowanej i liczbę jednostek zarezerwowanych kodowania przy użyciu zestawu SDK platformy .NET, wykonaj następujące czynności:

```csharp
IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
encodingS1ReservedUnit.Update();
Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

encodingS1ReservedUnit.CurrentReservedUnits = 2;
encodingS1ReservedUnit.Update();

Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);
```

## <a name="opening-a-support-ticket"></a>Otwieranie biletu pomocy technicznej

Domyślnie każde konto Media Services może być skalowane do 10 jednostek zarezerwowanych multimediów S2 lub S3 (MRUs) lub 25 S1 MRUs i 5 jednostek zarezerwowanych przesyłania strumieniowego na żądanie. Aby zażądać wyższego limitu, możesz otworzyć bilet pomocy technicznej.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
