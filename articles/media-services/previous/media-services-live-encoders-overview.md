---
title: Konfigurowanie koderów lokalnych podczas tworzenia strumieni o wielu szybkościach transmisji bitów przy użyciu usługi Azure Media Services | Dokumenty firmy Microsoft
description: W tym temacie wymieniono lokalne kodery na żywo, których można użyć do przechwytywania wydarzeń na żywo i wysyłania pojedynczego strumienia na żywo o szybkości transmisji bitów do kanałów usługi AMS (które są włączone kodowania na żywo) w celu dalszego przetwarzania. Temat zawiera łącza do samouczków, które pokazują, jak skonfigurować wymienione kodery.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 82fa5968d004bd4460f2c05ec944f6e568508675
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641135"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Jak skonfigurować kodery lokalne podczas tworzenia strumieni o wielu szybkościach transmisji bitów w usłudze Azure Media Services
W tym temacie wymieniono lokalne kodery na żywo, których można użyć do przechwytywania wydarzeń na żywo i wysyłania pojedynczego strumienia na żywo o szybkości transmisji bitów do kanałów usługi AMS (które są włączone kodowania na żywo) w celu dalszego przetwarzania. Temat zawiera również łącza do samouczków, które pokazują, jak skonfigurować wymienione kodery.

> [!NOTE]
> Podczas przesyłania strumieniowego za pośrednictwem protokołu RTMP sprawdź ustawienia zapory i/lub serwera proxy, aby upewnić się, że porty TCP ruchu wychodzącego 1935 i 1936 są otwarte.

## <a name="haivision-kb-encoder"></a>Koder Haivision KB
Aby uzyskać informacje dotyczące konfigurowania [kodera ujeżdnerowy ekwiwalatora bazy danych Haivision KB](https://www.haivision.com/products/kb-series/) do wysyłania pojedynczego strumienia na żywo o szybkości transmisji bitów do kanału AMS, zobacz [Konfigurowanie kodera KB usługi Haivision](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Telestream Wirecast
Aby uzyskać informacje na temat konfigurowania kodera [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) do wysyłania pojedynczego strumienia transmisji bitów na żywo do kanału AMS, zobacz [Konfigurowanie wirecastu](media-services-configure-wirecast-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
Aby uzyskać więcej informacji, zobacz [Elemental Live](https://www.elementaltechnologies.com/products/elemental-live).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki

[Przesyłanie strumieniowe na żywo za pomocą usługi Azure Media Services do tworzenia strumieni o wielu szybkościach transmisji bitów](media-services-manage-live-encoder-enabled-channels.md).

