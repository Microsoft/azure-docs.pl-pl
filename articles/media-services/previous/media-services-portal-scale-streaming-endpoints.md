---
title: Skalowanie punktów końcowych przesyłania strumieniowego przy użyciu Azure Portal | Microsoft Docs
description: Ten samouczek przeprowadzi Cię przez kroki skalowania punktów końcowych przesyłania strumieniowego przy użyciu Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 4db53d64131e6fb43ce425cf579d79e62775754a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009759"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Skalowanie punktów końcowych przesyłania strumieniowego przy użyciu witryny Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Omówienie

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Punkty końcowe przesyłania strumieniowego **Premium** są odpowiednie w przypadku zaawansowanych obciążeń, ponieważ zapewniają dedykowaną i skalowalną pojemność przepustowości. Klienci, którzy mają punkt końcowy przesyłania strumieniowego **Premium**, domyślnie uzyskują jedną jednostkę przesyłania strumieniowego (SU, streaming unit). Punkt końcowy przesyłania strumieniowego można skalować poprzez dodawanie jednostek SU. Każdy jednostka SU zwiększa pojemność przepustowości aplikacji. Aby uzyskać więcej informacji na temat typów punktów końcowych przesyłania strumieniowego i konfiguracji sieci CDN, zobacz temat [Omówienie punktu końcowego przesyłania strumieniowego](media-services-streaming-endpoints-overview.md) .
 
W tym temacie przedstawiono sposób skalowania punktu końcowego przesyłania strumieniowego.

Aby dowiedzieć się więcej o cenach, zobacz artykuł [Szczegółowe informacje o cenach usługi Media Services](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="scale-streaming-endpoints"></a>Skalowanie punktów końcowych przesyłania strumieniowego

Aby zmienić liczbę jednostek przesyłania strumieniowego, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. W oknie **Ustawienia** wybierz pozycję **punkty końcowe przesyłania strumieniowego**.
3. Kliknij punkt końcowy przesyłania strumieniowego, który chcesz skalować. 

    > [!NOTE] 
    > Możesz skalować punkty końcowe przesyłania strumieniowego w **warstwie Premium** .

4. Przesuń suwak, aby określić liczbę jednostek przesyłania strumieniowego.

    ![Punkt końcowy przesyłania strumieniowego](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Następne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

