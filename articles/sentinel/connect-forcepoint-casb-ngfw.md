---
title: Łączenie produktów Forcepoint z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć produkty Forcepoint z platformą Azure — wskaźnikiem.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77588233"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Połącz swoje produkty Forcepoint z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik danych produktów Forcepoint na platformie Azure jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


W tym artykule wyjaśniono, jak połączyć produkty Forcepoint z platformą Azure — wskaźnikiem. 

Łączniki danych Forcepoint umożliwiają połączenie brokera zabezpieczeń usługi Forcepoint Cloud Access i Forcepointe dzienników zapory nowej generacji z platformą Azure. W ten sposób można automatycznie eksportować dzienniki zdefiniowane przez użytkownika do usługi Azure wskaźnikowego w czasie rzeczywistym. Łącznik zapewnia ulepszony wgląd w działania użytkownika zarejestrowane przez produkty Forcepoint. Umożliwia również dalsze korelację z danymi z obciążeń platformy Azure i innych kanałów informacyjnych oraz zwiększa możliwości monitorowania ze skoroszytami w ramach platformy Azure.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Przekazywanie dzienników produktów Forcepoint do agenta dziennika systemu 

Skonfiguruj produkt Forcepoint do przesyłania dalej komunikatów dziennika systemowego w formacie CEF do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemu.

1. Skonfiguruj integrację produktu Forcepoint na platformie Azure wskaźnikowego, zgodnie z opisem w następujących przewodnikach instalacji:
 - [Przewodnik po integracji z programem Forcepoint CASB](https://frcpnt.com/casb-sentinel)
 - [Przewodnik po integracji z programem Forcepoint zapory następnej generacji](https://frcpnt.com/ngfw-sentinel)

2. Wyszukaj CommonSecurityLog, aby użyć odpowiedniego schematu w Log Analytics z nazwą DeviceVendor zawiera "Forcepoint". 

3. Przejdź do [kroku 3: weryfikowanie łączności](connect-cef-verify.md).



## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia produktów Forcepoint z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).

- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.