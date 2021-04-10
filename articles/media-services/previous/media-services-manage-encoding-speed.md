---
title: Zarządzaj szybkością i współbieżnością kodowania przy użyciu Azure Media Services | Microsoft Docs
description: Ten artykuł zawiera krótkie omówienie sposobu zarządzania szybkością i współbieżnością zadań kodowania/zadań przy użyciu Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: f01235e5b02bf2a61a359fa5571c6e6d575c1232
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014812"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Zarządzanie szybkością i współbieżnością kodowania

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

Ten artykuł zawiera krótkie omówienie sposobu zarządzania szybkością i współbieżnością zadań kodowania/zadań.

## <a name="overview"></a>Omówienie

W Media Services **Typ jednostki zarezerwowanej** określa szybkość przetwarzania zadań przetwarzania multimediów. Można wybrać następujące typy jednostek zarezerwowanych: **S1**, **S2** lub **S3**. Na przykład to samo zadanie kodowania jest wykonywane szybciej przy użyciu typu jednostki zarezerwowanej **S2** niż w przypadku użycia typu **S1**. W temacie [skalowanie jednostek kodowania](media-services-scale-media-processing-overview.md) przedstawiono tabelę, która ułatwia podejmowanie decyzji podczas wybierania różnych szybkości kodowania.

Oprócz określania typu jednostki zarezerwowanej możesz określić, aby udostępnić konto za pomocą **jednostek zarezerwowanych**. Liczba zainicjowanych jednostek zarezerwowanych określa liczbę zadań multimedialnych, które mogą być przetwarzane jednocześnie w ramach danego konta. Na przykład jeśli konto ma pięć jednostek zarezerwowanych, pięć zadań multimedialnych będzie wykonywanych współbieżnie, o ile istnieją zadania do przetworzenia. Pozostałe zadania czekają w kolejce i zostaną pobrane do przetwarzania sekwencyjnego po zakończeniu uruchomionego zadania. Jeśli dla konta nie zainicjowano obsługi żadnych jednostek zarezerwowanych, zadania będą wybierane sekwencyjnie. W takim przypadku czas oczekiwania między kolejnymi zadaniami zostanie zależał od dostępności zasobów w systemie.

Aby uzyskać szczegółowe informacje i przykłady pokazujące sposób skalowania jednostek kodowania, zobacz [ten](media-services-scale-media-processing-overview.md) temat.

## <a name="next-step"></a>Następny krok

[Skalowanie jednostek kodowania](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

