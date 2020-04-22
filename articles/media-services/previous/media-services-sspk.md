---
title: Licencjonowanie&reg; zestawu Microsoft Smooth Streaming Client Porting Kit
description: Dowiedz się, jak uzyskać&reg; licencję na zestaw Microsoft Smooth Streaming Client Porting Kit.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2019
ms.author: xpouyat
ms.openlocfilehash: bd77c53a195a9549f6aaad9ee2928206f5324b0c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686913"
---
# <a name="licensing-microsoftreg-smooth-streaming-client-porting-kit"></a>Licencjonowanie&reg; zestawu Microsoft Smooth Streaming Client Porting Kit 
## <a name="overview"></a>Omówienie
Microsoft Smooth Streaming Client Porting Kit (W skrócie**SSPK)** to implementacja klienta Smooth Streaming, która jest zoptymalizowana, aby pomóc wbudowanym producentom urządzeń, operatorom telewizji kablowej i komórkowej, dostawcom usług w zakresie treści, producentom słuchawek, niezależnym dostawcom oprogramowania (ISV) i dostawcom rozwiązań w tworzeniu produktów i usług do przesyłania strumieniowego adaptacyjnej zawartości w formacie Smooth Streaming. SSPK to niezależna od urządzenia i platforma implementacja klienta Smooth Streaming, który może zostać przeniesiony przez licencjobiorcę na dowolne urządzenie i platformę. 

Poniżej znajduje się architektura wysokiego poziomu i IIS Smooth Streaming Porting Box pole jest Smooth Streaming Client implementacji dostarczonej przez firmę Microsoft i zawiera wszystkie podstawowe logiki odtwarzania zawartości Smooth Streaming. Ta zawartość jest następnie przenoszona przez partnerów dla określonego urządzenia lub platformy przez implementowanie odpowiednich interfejsów. 

![ZSK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Opis
SSPK jest licencjonowany na warunkach, które oferują doskonałą wartość biznesową. Licencja SSPK zapewnia branży:

* Smooth Streaming Porting Kit source w języku C++ 
  * implementuje funkcję klienta smooth streaming
  * dodaje analizowanie formatów, heurystykę, logikę buforowania itp.
* Interfejsy API aplikacji odtwarzacza 
  * interfejsy programowania do interakcji z aplikacją odtwarzacza multimedialnego
* Interfejs warstwy abstrakcji platformy (PAL) 
  * interfejsy programowania interakcji z systemem operacyjnym (wątki, gniazda)
* Interfejs warstwy abstrakcji sprzętu (HAL) 
  * interfejsy programowania interakcji ze sprzętowymi dekoderami A/V (dekodowanie, renderowanie)
* Interfejs zarządzania prawami cyfrowymi (DRM) 
  * interfejsy programowania do obsługi drm za pośrednictwem warstwy abstrakcji DRM (DAL)
  * Zestaw Do przenoszenia Programu Microsoft PlayReady jest dostarczany oddzielnie, ale integruje się za pośrednictwem tego interfejsu. Aby uzyskać więcej informacji na temat licencjonowania urządzeń PlayReady firmy Microsoft, kliknij [tutaj](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Przykłady implementacji 
  * przykładowa implementacja PAL dla systemu Linux
  * przykładowa implementacja HAL dla GStreamer

## <a name="licensing-options"></a>Opcje licencjonowania
Zestaw Microsoft Smooth Streaming Client Porting Kit jest udostępniany licencjobiorcom na podstawie dwóch odrębnych umów licencyjnych: jednej dotyczącej opracowywania produktów tymczasowych klienta Smooth Streaming, a drugiej do dystrybucji produktów końcowych klienta Smooth Streaming wśród użytkowników końcowych.

* W przypadku producentów chipsetów, integratorów systemów lub niezależnych dostawców oprogramowania (ISV), którzy potrzebują zestawu do przenoszenia kodu źródłowego do opracowywania produktów tymczasowych, należy wykonać **tymczasową licencję produktu** zestawu Microsoft Smooth Streaming Client Porting Kit.
* W przypadku producentów urządzeń lub dostawców oprogramowania, którzy wymagają praw do dystrybucji produktów końcowych klienta smooth streaming dla użytkowników końcowych, należy wykonać **licencję produktu końcowego zestawu microsoft** smooth streaming client porting kit.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Tymczasowa licencja produktu zestawu Microsoft Smooth Streaming Client Porting Kit
Zgodnie z niniejszą licencją firma Microsoft oferuje zestaw smooth streaming client porting kit oraz niezbędne prawa własności intelektualnej do opracowywania i dystrybucji produktów tymczasowych klienta Smooth Streaming na rzecz innych licencjobiorców urządzenia Smooth Streaming Client Porting Kit, które dystrybuują produkty końcowe klienta Smooth Streaming.

#### <a name="fee-structure"></a>Struktura opłat
Jednorazowa opłata licencyjna w wysokości 50 000 USD zapewnia dostęp do zestawu smooth streaming client porting kit. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Licencja produktu końcowego zestawu do przenoszenia klienta usługi Microsoft Smooth Streaming
Zgodnie z niniejszą licencją firma Microsoft oferuje wszelkie niezbędne prawa własności intelektualnej do otrzymywania tymczasowych produktów klienta Smooth Streaming od innych licencjobiorców Smooth Streaming Client Porting Kit oraz do dystrybucji produktów końcowych klienta Smooth Streaming marki firmy wśród użytkowników końcowych.

#### <a name="fee-structure"></a>Struktura opłat
Produkt końcowy klienta Smooth Streaming jest oferowany w modelu tantiem, jak w:

* $0.10 za wdrożenie urządzenia wysłane
* Tantiemy są ograniczone do 50.000 dolarów rocznie
* Brak tantiem za pierwsze 10 000 implementacji urządzeń rocznie 

## <a name="licensing-procedure-and-sspk-access"></a>Procedura licencjonowania i dostęp do SSPK
Wyślij [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) wiadomość e-mail do wszystkich zapytań licencyjnych.

Portal dystrybucji SSPK jest dostępny dla zarejestrowanych licencjobiorców tymczasowych.

Licencjobiorcy tymczasowi i końcowi [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com)mogą przesyłać pytania techniczne do .

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Licencjobiorcy tymczasowego produktu klienta usługi Microsoft Smooth Streaming

* Adroit Business Solutions, Inc
* Zaawansowana emisja cyfrowa SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Sp. z o.o.
* Alticast Korporacja
* Amazon Digital Services, Inc.
* Arion Technology, Inc.
* AVC Multimedia Software Co., Ltd.
* Cavium, Inc.
* EchoStar Zakup Korporacja
* Enseo, Inc.
* Fluendo S.A.
* Guangzhou Shikun Electronics., Ltd.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir Sp. z o.o.
* Irdeto USA Inc.
* Liberty Global Services BV
* MediaTek Sp. z o.o.
* MStar Co, Ltd
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Saffron Digital Limited
* Sichuan Changhong Electric Co., Ltd
* SoftAtHome (Dom miękki)
* Korporacja Sony
* Tatung Technology Inc.
* Najlepsze Inwestycje Zwycięstwa, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* Korporacja ZTE

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Licencjobiorcy końcowego produktu klienta usługi Microsoft Smooth Streaming
* Zaawansowana emisja cyfrowa SA
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Albis Technologies Sp. z o.o.
* Amazon Digital Services, Inc.
* AmTRAN Technology Co., Ltd.
* Arcadyan Technology Corporation
* Arion Technology, Inc.
* ATMACA ELEKTRONİK SAN. VE TİC. A.Ş
* Brytyjska Telewizja Sky Limited
* CastPal Technology Inc., Shenzhen
* Compal Electronics, Inc.
* Dongguan Digital AV Technology Corp., Ltd.
* EchoStar Zakup Korporacja
* Enseo, Inc.
* FilmFlex Filmy Ograniczona
* Fluendo S.A.
* FUNAI ELECTRIC CO., LTD
* Gibson Innowacje Ograniczona
* Haier Informacje Wnioskodawca S.R.L
* HANDAN BroadInfoCom Co., Ltd.
* Hisense International Co., Ltd. 
* Homecast Co., Ltd
* Hon Hai Precision Industry Co., Ltd.
* Infomir Sp. z o.o.
* Kaonmedia Co., Ltd.
* Korporacja KDDI
* Nintendo Co., Ltd.
* Pomarańczowy SA
* Saffron Digital Limited
* Sagemcom Broadband SAS
* Shenzhen Chuangwei-RGB Electronics Co., Ltd.
* Shenzhen Coship Electronics CO., LTD
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen Skyworth Digital Technology Co., Ltd
* Sichuan Changhong Electric Co., Ltd.
* Skardin Industrial Corp.
* Sky Deutschland Fernsehen GmbH & Co. KG
* SmarDTV S.A.
* SoftAtHome (Dom miękki)
* Korporacja Sony
* Technicolor Delivery Technologies, SAS
* Tongfang Global Sp. z o.o.
* Najlepsze Inwestycje Zwycięstwa, Ltd.
* Toshiba Lifestyle Products & Services Corporation
* Universal Media Corporation /Słowacja/ s.r.o.
* VIZIO, Inc.
* Korporacja Wistron
* Korporacja ZTE

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

