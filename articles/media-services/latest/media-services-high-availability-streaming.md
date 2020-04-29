---
title: Azure Media Services przesyłania strumieniowego o wysokiej dostępności
description: Dowiedz się, jak przełączyć się w tryb failover na pomocnicze konto Media Services, jeśli wystąpi awaria lub niepowodzenie centrum danych.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78899232"
---
# <a name="media-services-high-availability-streaming"></a>Media Services przesyłania strumieniowego o wysokiej dostępności

Azure Media Services obecnie nie zapewnia natychmiastowej pracy awaryjnej usługi, jeśli istnieje regionalna awaria centrum danych lub niepowodzenie podstawowego składnika lub usług zależnych. Ten artykuł zawiera wskazówki dotyczące sposobu tworzenia przesyłania strumieniowego między regionami wideo na żądanie.

## <a name="prerequisites"></a>Wymagania wstępne

Zapoznaj [się z tematem tworzenie wieloregionalnego systemu kodowania](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Jak kompilować strumieniowe przesyłanie wideo na żądanie 

* Przesyłanie strumieniowe między różnymi regionami wideo obejmuje duplikowanie [zasobów](assets-concept.md), [zasad kluczy zawartości](content-key-policy-concept.md) (jeśli są używane), [zasad przesyłania strumieniowego](streaming-policy-concept.md)i [lokalizatorów przesyłania strumieniowego](streaming-locators-concept.md). 
* Należy utworzyć zasady w obu regionach i zachować ich aktualność. 
* Podczas tworzenia lokalizatorów przesyłania strumieniowego należy użyć tej samej wartości identyfikatora lokalizatora, wartości identyfikatora ContentKey i wartości ContentKey.  
* W przypadku kodowania zawartości należy zakodować zawartość w regionie A i opublikować ją, a następnie skopiować zakodowaną zawartość do regionu B i opublikować ją przy użyciu takich samych wartości jak z regionu A.
* Traffic Manager można użyć na nazwach hostów dla pochodzenia i usługi Key Delivery (w Media Services konfiguracji będzie to wyglądać jak niestandardowy adres URL serwera kluczy).

## <a name="next-steps"></a>Następne kroki

Wymelduj się:

* [Samouczek: kodowanie pliku zdalnego na podstawie adresu URL i strumieniowego wideo](stream-files-dotnet-quickstart.md)
* [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
