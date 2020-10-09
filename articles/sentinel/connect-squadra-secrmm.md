---
title: Łączenie Squadra technologii secRMM danych z platformą Azure Microsoft Docs
description: Dowiedz się, jak łączyć dane technologii Squadra Technologies secRMM z danymi na platformie Azure.
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
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77588114"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Połącz dane technologii Squadra secRMM z danymi do usługi Azure wskaźnikowego 

> [!IMPORTANT]
> Łącznik danych Squadra Technologies Security Media Manager (secRMM) na platformie Azure — obecnie jest w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Łącznik technologii Squadra Technologies secRMM umożliwia łatwe łączenie dzienników rozwiązań zabezpieczeń secRMM Technologies Squadra z platformą Azure. Umożliwia wyświetlanie pulpitów nawigacyjnych, tworzenie alertów niestandardowych i ulepszanie badania. Ten łącznik zawiera szczegółowe informacje o zdarzeniach magazynu wymiennego USB. Integracja między Squadra technologiami secRMM i Azure wskaźnikiem IT korzysta z interfejsu API REST.


> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Konfigurowanie i łączenie technologii Squadra secRMM 

Technologie Squadra secRMM umożliwiają integrację i eksportowanie dzienników bezpośrednio do usługi Azure wskaźnikowej.
1. W portalu wskaźnikowym platformy Azure kliknij pozycję łączniki danych, a następnie wybierz pozycję Squadra Technologies secRMM, a następnie otwórz stronę łącznik.

2. Postępuj zgodnie z instrukcjami przedstawionymi w [przewodniku dołączania technologii Squadra do usługi Azure wskaźnikowej](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) , aby uzyskać dane Squadra SecRMM na platformie Azure.   


## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane są wyświetlane w Log Analytics w obszarze secRMM_CL CustomLogs.
Aby użyć odpowiedniego schematu w Log Analytics technologii Squadra Technologies secRMM, wyszukaj ciąg secRMM_CL.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności
Rozpoczęcie wyświetlania dzienników w Log Analytics może zająć więcej niż 20 minut. 


## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia Squadra technologii secRMM z platformą Azure wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.

