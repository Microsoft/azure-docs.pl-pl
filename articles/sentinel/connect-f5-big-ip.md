---
title: Łączenie danych BIG-IP z usługą Azure wskaźnikowego | Microsoft Docs
description: Dowiedz się, jak połączyć dane BIG-IP z platformą Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: ae361c74b261bdd6a5673040f868392282b573ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77588284"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Połączenie z urządzeniem F5 BIG-IP 

> [!IMPORTANT]
> Łącznik danych BIG-IP na platformie Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Łącznik F5 BIG-IP umożliwia łatwe łączenie wszystkich dzienników F5 BIG-IP z platformą Azure, przeglądanie skoroszytów, tworzenie alertów niestandardowych i ulepszanie badania. Zapewnia to dokładniejszy wgląd w sieć organizacji i zwiększa możliwości operacji zabezpieczeń. Integracja między programami F5 BIG-IP i Azure wskaźnikiem jest używana przez interfejs API REST.


> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-f5-big-ip"></a>Konfigurowanie i nawiązywanie połączenia F5 BIG-IP 

F5 BIG-IP umożliwia integrację i eksportowanie dzienników bezpośrednio do usługi Azure wskaźnikowej.

1. W portalu usługi Azure wskaźnikowym kliknij pozycję **Łączniki danych** i wybierz pozycję **F5 Big-IP** , a następnie **Otwórz stronę łącznik**. 
1. Aby połączyć się z F5 BIG-IP, należy opublikować deklarację JSON w punkcie końcowym interfejsu API systemu. Aby uzyskać instrukcje, jak to zrobić, zobacz [integrowanie F5 Big-IP ze wskaźnikiem na platformie Azure](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).
8. Na stronie łącznika BIG-IP Skopiuj identyfikator obszaru roboczego i klucz podstawowy i wklej je zgodnie z instrukcjami w obszarze [przesyłanie strumieniowe danych do usługi Azure log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics).
1. Po wykonaniu instrukcji F5 BIG-IP na stronie łącznika wskaźnikowego platformy Azure są wyświetlane połączone typy danych.
1. Aby użyć odpowiedniego schematu w Log Analytics dla zdarzenia F5 BIG-IP, Wyszukaj **F5Telemetry_LTM_CL**, **F5Telemetry_system_CL**i **F5Telemetry_ASM_CL**.


## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może zająć więcej niż 20 minut. 



## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia z programem F5 BIG-IP do badania wskaźnikowego platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.


