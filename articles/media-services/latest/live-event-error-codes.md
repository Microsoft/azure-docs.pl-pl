---
title: Azure Media Services kodów błędów zdarzeń na żywo | Microsoft Docs
description: W tym artykule wymieniono kody błędów zdarzeń na żywo.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: f9067562f67190b8bc04392f33078d4d3262f986
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77654583"
---
# <a name="media-services-live-event-error-codes"></a>Kody błędów zdarzeń na żywo Media Services

W tabelach w tej sekcji wymieniono kody błędów [zdarzeń na żywo](live-events-outputs-concept.md) .

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Po zasubskrybowaniu zdarzeń [Event Grid](https://docs.microsoft.com/azure/event-grid/) dla zdarzenia na żywo może zostać wyświetlony jeden z następujących błędów ze zdarzenia [LiveEventConnectionRejected](media-services-event-schemas.md#liveeventconnectionrejected) .

| Kod wyniku | Opis |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Nieprawidłowy adres URL pozyskiwania |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | Nie istnieje adres IP kodera w skonfigurowanej liście dozwolonych adresów IP |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Koder RTMP nie wysłał polecenia setDataFrame. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Określony koder-dekoder nie jest obsługiwany. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |Informacje o opisie nośnika nie zostały odebrane przed dostarczeniem rzeczywistych danych nośnika.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |Liczba jakości dla typu audio lub wideo przekroczyła maksymalny dozwolony limit.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |Łączna liczba przychodzących szybkości transmisji bitów w zdarzeniu lub w usłudze kanału na żywo przekroczyła maksymalny dozwolony limit.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Sygnatura czasowa dla FLVTag wideo lub audio jest nieprawidłowa z kodera RTMP. |
| MPE_INGEST_FRAMERATE_EXCEEDED | Przychodzące strumienie dla koderów przychodzących z szybkością klatek przekraczają maksymalną dozwoloną liczbę 30fps na potrzeby kodowania na żywo zdarzeń/kanałów.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | Przychodzące strumienie przekazywanego kodera przekraczają następujące dozwolone rozwiązania: 1920x1088 do kodowania na żywo zdarzeń/kanałów i 4096 x 2160 dla zdarzeń przesyłanych/kanałów na żywo.|

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Może zostać wyświetlony jeden z następujących błędów ze zdarzenia [LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) .

|Kod wyniku|Opis|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|Przekroczono limit czasu bezczynności sesji RTMP po upływie limitu czasu.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|Sygnatura czasowa dla FLVTag wideo lub audio jest nieprawidłowa z kodera RTMP.|
|MPE_CAPACITY_LIMIT_REACHED|Koder wysyła zbyt szybkie dane.|
|Nieznane kody błędów|Te kody błędów mogą należeć do zakresu od błędu pamięci do zduplikowanych wpisów w mapie skrótów.|


## <a name="see-also"></a>Zobacz także

[Kody błędów punktu końcowego przesyłania strumieniowego (Źródło)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Następne kroki

[Samouczek: przesyłanie strumieniowe na żywo za pomocą Media Services](stream-live-tutorial-with-api.md)
