---
title: Jak połączyć dwa lub więcej plików wideo z platformą .NET | Microsoft Docs
description: W tym artykule pokazano, jak połączyć dwa lub więcej plików wideo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5eacc366961d3101a7eaf8877a34ef2d462ea76b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111457"
---
# <a name="how-to-stitch-two-or-more-video-files-with-net"></a>Jak połączyć dwa lub więcej plików wideo z platformą .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="stitch-two-or-more-video-files"></a>Łączenie dwóch lub więcej plików wideo

Poniższy przykład ilustruje, jak można wygenerować ustawienia wstępne, aby połączyć dwa lub więcej plików wideo. Typowy scenariusz polega na tym, że chcesz dodać nagłówek lub przyczepę do głównego wideo.

> [!NOTE]
> Pliki wideo edytowane razem powinny udostępniać właściwości (rozdzielczość wideo, szybkość klatek, liczba ścieżek audio itp.). Należy zadbać o to, aby nie mieszać filmów wideo o różnych szybkościach klatek lub o różnej liczbie ścieżek audio.

## <a name="prerequisites"></a>Wymagania wstępne

Klonuj lub Pobierz [przykłady Media Services .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/).  Kod, do którego się odwołuje się, znajduje się w [folderze EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs).

### <a name="net-code"></a>Kod platformy .NET

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)]
