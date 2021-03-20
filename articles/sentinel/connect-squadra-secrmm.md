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
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: a00b4b1e81c0d644cf1475aa46dda3848fda1365
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632905"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Połącz dane technologii Squadra secRMM z danymi do usługi Azure wskaźnikowego 

Łącznik technologii Squadra Technologies secRMM umożliwia łatwe łączenie dzienników rozwiązań zabezpieczeń secRMM Technologies Squadra z platformą Azure. Umożliwia wyświetlanie pulpitów nawigacyjnych, tworzenie alertów niestandardowych i ulepszanie badania. Ten łącznik zawiera szczegółowe informacje o zdarzeniach magazynu wymiennego USB. Integracja między Squadra technologiami secRMM i Azure wskaźnikiem IT korzysta z interfejsu API REST.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Konfigurowanie i łączenie technologii Squadra secRMM 

Technologie Squadra secRMM umożliwiają integrację i eksportowanie dzienników bezpośrednio do usługi Azure wskaźnikowej.
1. W portalu wskaźnikowym platformy Azure kliknij pozycję łączniki danych, a następnie wybierz pozycję Squadra Technologies secRMM, a następnie otwórz stronę łącznik.

2. Postępuj zgodnie z instrukcjami przedstawionymi w [przewodniku dołączania technologii Squadra do usługi Azure wskaźnikowej](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) , aby uzyskać dane Squadra SecRMM na platformie Azure.   

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **CustomLogs** w `secRMM_CL` tabeli.

Aby wykonać zapytanie dotyczące dzienników secRMM technologii Squadra, wprowadź nazwę tabeli w górnej części okna zapytania.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut. 

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia Squadra technologii secRMM z platformą Azure wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
