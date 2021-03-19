---
title: Licencjonowanie &reg; zestawu portów programu Microsoft Smooth Streaming Client
description: Dowiedz się więcej na temat licencjonowania &reg; zestawu portów Microsoft Smooth Streaming clienting Kit.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: xpouyat
ms.openlocfilehash: 36e5b020241f7be33aa4cfa473222ad301d18fbe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581103"
---
# <a name="licensing-microsoftreg-smooth-streaming-client-porting-kit"></a>Licencjonowanie &reg; zestawu portów programu Microsoft Smooth Streaming Client

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
## <a name="overview"></a>Omówienie
Microsoft Smooth Streaming Clienting Kit (**SSPK** for Short) to Smooth Streaming implementacja klienta, która została zoptymalizowana pod kątem pomocy producentom urządzeń osadzonych, operatorom telewizji kablowej i mobilnemu, dostawcom usług zawartości, producentom słuchawek, niezależnym dostawcom oprogramowania i dostawcom rozwiązań tworzenie produktów i usług na potrzeby przesyłania strumieniowego zawartości w formacie Smooth Streaming. SSPK to oparta na urządzeniach i niezależna od platformy implementacja klienta Smooth Streaming, którą można przenieść na dowolne urządzenie i platformę. 

Poniżej znajduje się architektura wysokiego poziomu, a Usługa IIS Smooth Streaming Porting Kit to Smooth Streaming implementacja klienta udostępniona przez firmę Microsoft, która obejmuje całą podstawową logikę odtwarzania Smooth Streaming zawartości. Ta zawartość jest następnie przewidziana przez partnerów dla określonego urządzenia lub platformy przez implementację odpowiednich interfejsów. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Opis
SSPK jest licencjonowane na warunkach, które oferują doskonałą wartość biznesową. Licencja SSPK zapewnia branżę z:

* Smooth Streaming źródło zestawu portów w języku C++ 
  * implementuje funkcję klienta Smooth Streaming
  * dodaje analizę formatu, algorytmy heurystyczne, logikę buforowania itp.
* Interfejsy API aplikacji odtwarzacza 
  * Interfejsy programowania do interakcji z aplikacją odtwarzacza multimedialnego
* Interfejs warstwy abstrakcji platformy (PAL) 
  * Interfejsy programowania do interakcji z systemem operacyjnym (wątki, gniazda)
* Interfejs warstwy abstrakcji sprzętu (HAL) 
  * Interfejsy programowania do interakcji z sprzętowymi dekoderami A/V (dekodowanie, renderowanie)
* Interfejs Digital Rights Management (DRM) 
  * Interfejsy programowania do obsługi technologii DRM za pomocą warstwy abstrakcji DRM (DAL)
  * Zestaw portów Microsoft PlayReady, który jest dostarczany oddzielnie, ale integruje się z tym interfejsem. [Zobacz więcej szczegółów na temat licencjonowania urządzeń firmy Microsoft PlayReady](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Przykłady implementacji 
  * Przykładowa implementacja PAL dla systemu Linux
  * Implementacja przykładowej warstwy HAL dla GStreamer

## <a name="licensing-options"></a>Opcje licencjonowania
Pakiet Microsoft Smooth Streaming Clienting Kit jest dostępny dla licencji w ramach dwóch odrębnych umów licencyjnych: jeden do tworzenia Smooth Streaming produktów tymczasowych klienta i drugi do dystrybucji Smooth Streaming końcowych produktów klienta dla użytkowników końcowych.

* W przypadku producentów chipsetów, integratorów systemów lub niezależnych dostawców oprogramowania (ISV), którzy wymagają zestawu numerowania kodu źródłowego do opracowania produktów przejściowych, należy wykonać **licencję produktu** Microsoft Smooth Streaming clienting Kit.
* W przypadku producentów urządzeń lub niezależnych dostawców oprogramowania, które wymagają praw dystrybucji dla końcowych produktów klienta Smooth Streaming dla użytkowników końcowych, należy wykonać **ostateczną licencję produktu** Microsoft Smooth Streaming clienting Kit.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Smooth Streaming Client Porting Kit — tymczasowy licencji produktu
W ramach tej licencji firma Microsoft oferuje zestaw portów Smooth Streaming klienta i wymagane prawa własności intelektualnej do opracowania i dystrybucji produktów tymczasowych klienta Smooth Streaming do innych Smooth Streamingch licencji na urządzenia z zestawem portów klienta, które dystrybuują Smooth Streaming końcowych produktów klienta.

#### <a name="fee-structure"></a>Struktura opłat
Opłata za jednorazowe licencje w Stanach Zjednoczonych $50 000 zapewnia dostęp do zestawu portów klienta Smooth Streaming. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Licencja na produkt końcowy programu Microsoft Smooth Streaming Clienting Kit
W ramach tej licencji firma Microsoft oferuje wszystkie niezbędne prawa własności intelektualnej do odbierania Smooth Streaming produktów tymczasowych klienta z innych Smooth Streaming licencji usługi Smooth Streaming Client SDK z wersji

#### <a name="fee-structure"></a>Struktura opłat
Końcowy produkt klienta Smooth Streaming jest oferowany w ramach modelu honorarium w ramach:

* $0,10 na zadaną implementację urządzenia
* Honorarium jest ograniczone do $50 000 każdego roku
* Brak honorarium dla pierwszych 10 000 implementacji urządzeń w każdym roku 

## <a name="licensing-procedure-and-sspk-access"></a>Procedura licencjonowania i dostęp SSPK
Wyślij wiadomość e-mail [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) do wszystkich zapytań licencjonowania.

Portal dystrybucji SSPK jest dostępny dla zarejestrowanych licencji tymczasowych.

Licencje na tymczasowe i końcowe SSPK mogą przesyłać pytania techniczne do programu [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com) .

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Licencje na umowę o produkcie pośrednim klienta firmy Microsoft Smooth Streaming

* Adroit Business Solutions, Inc
* Zaawansowane skojarzenia zabezpieczeń Digital Broadcast
* Kablosuz Iletism Sanayive dis Ticaret A.S.
* Technologie Albis Ltd.
* Alticast Corporation
* Amazon Digital Services, Inc.
* Arion Technology, Inc.
* Oprogramowanie multimedialne z AVC co., Ltd.
* Cavium, Inc.
* EchoStar zakup firmy
* Enseo, Inc.
* Fluendo sa
* Guangzhou Shikun Electronics., Ltd.
* HANDAN BroadInfoCom co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* Wolność usługi globalne usług BV
* MediaTek Inc.
* MStar co, Ltd
* Nintendo co., Ltd.
* OpenTV, Inc.
* Saffron cyfrowo
* Sichuan Changhong, co., Ltd
* SoftAtHome
* Firma Sony Corporation
* Tatung Technology Inc.
* Najlepsze inwestycje Victory, Ltd.
* Vestel Elektronik Sanayi ve Ticaret A.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Ostateczne licencje umowy licencyjnej programu Microsoft Smooth Streaming Client
* Zaawansowane skojarzenia zabezpieczeń Digital Broadcast
* Kablosuz Iletism Sanayive dis Ticaret A.S.
* AmTRAN technologii co., Ltd 
* Arcadyan Technology Corporation
* Arcelik A. S
* Branżowe elektronika, Inc.
* TECHNOLOGIA EXPRESS SZCZĘŚCIA LIMITED
* Fluendo sa
* FUNAI ELEKTRYCZNY CO., LTD
* Hisense International co., Ltd.
* Hisense technologię co., Ltd
* HKC Corporation Limited
* Hongkong Konka Ltd
* InnoLux Corporation
* Jinpin Electrical Company Ltd. Zhuhai. S. E. Z
* Technologia K-Tronics (Suzhou) co., Ltd. 
* Kaonmedia co., Ltd.
* KDDI Corporation
* Fame elektroniki z ograniczeniami
* MIRC elektroniki
* MOKA LIMITED
* Nintendo co., Ltd.
* ONEPLUS ELECTRONICS (SHENZHEN) CO., LTD.
* Firma Panasonic
* Qingdao Haier OPTRONICS co., Ltd.
* Shenzhen ATEKO, co., Ltd.
* Shenzhen Chuangwei-RGB Electronics Co., Ltd.
* Shenzhen Jiuzhou, co., Ltd
* Shenzhen KTC Technology Co., Ltd. 
* Shenzhena technologia Maxmadea co., Ltd
* Shenzhen MTC co., Ltd
* Shenzhen Skyworth — Technologia cyfrowa co., Ltd
* Sichuan Changhong, co., Ltd.
* Skardin przemysł Corp.
* 'C CP Ltd
* SMARDTV GLOBALNE SAS
* SoftAtHome
* Firma Sony Corporation
* Technologie dostarczania Technicolor, SAS
* Najlepsze inwestycje Victory, Ltd.
* UMC Polska Sp. z. o.o.
* Vizio, Inc.
* ZTE Corporation

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

