---
title: Ładowanie aplikacji serwera proxy aplikacji trwa zbyt długo | Microsoft Docs
description: Rozwiązywanie problemów z wydajnością ładowania stron serwer proxy aplikacji usługi Azure AD platformy Azure
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac1182d719d7c90129115e1fadf94f4f86a28e8
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "65782647"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Ładowanie aplikacji serwera proxy aplikacji trwa zbyt długo

Ten artykuł pomaga zrozumieć, dlaczego ładowanie aplikacji serwer proxy aplikacji usługi Azure AD platformy Azure może trwać długo. Wyjaśniono również, co można zrobić, aby rozwiązać ten problem.

## <a name="overview"></a>Omówienie
Mimo że aplikacje działają, mogą wystąpić duże opóźnienia. Może istnieć większa topologia sieci, która pozwala zwiększyć szybkość działania. Aby uzyskać informacje na temat oceny różnych topologii, zapoznaj się z [dokumentem zagadnienia dotyczące sieci](application-proxy-network-topology.md).

Oprócz topologii sieci nie są obecnie dostępne dalsze zalecenia dotyczące dostrajania wydajności. Gdy usługa serwera proxy aplikacji zostanie rozwinięta, może to być centrum danych, które jest fizycznie bliższe. Bliższe zbliżenie może pomóc w opóźnieniu. Listę centrów danych platformy Azure można znaleźć na [stronie test opóźnienia](http://www.azurespeed.com/Azure/Latency). 

Centra danych z usługą serwera proxy aplikacji można znaleźć za pomocą [Narzędzia Test portów łączników](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Opinie dotyczące lokalizacji centrów danych serwera proxy aplikacji 
Mogą istnieć centra danych platformy Azure, które jeszcze nie obejmują serwera proxy aplikacji, ale doprowadziłoby do poprawy opóźnienia. Wyślij lokalizację centrum danych do aadapfeedback@microsoft.comprogramu. Firma Microsoft korzysta z opinii na temat planów rozszerzania.

Firma Microsoft pracuje nad dodatkowymi możliwościami w celu poprawy opóźnień. Po udostępnieniu tych ulepszeń dokumentacja zostanie zaktualizowana.

## <a name="next-steps"></a>Następne kroki
[Praca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md)
