---
title: Łączenie danych Sophos w chmurze OPTIX z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak używać łącznika OPTIX Cloud Sophos do ściągania <PRODUCT NAME> dzienników do platformy Azure — wskaźnik. Wyświetlaj <PRODUCT NAME> dane w skoroszytach, twórz Alerty i ulepszaj badanie.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/26/2021
ms.author: yelevin
ms.openlocfilehash: c66205ffd9bd5a742d645cbf2f9251a44329a16e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700933"
---
# <a name="connect-your-sophos-cloud-optix-to-azure-sentinel"></a>Łączenie Sophos Cloud OPTIX z platformą Azure — wskaźnik

> [!IMPORTANT]
> Łącznik usługi Sophos Cloud OPTIX jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

Łącznik Sophos Cloud OPTIX umożliwia łatwe łączenie dzienników rozwiązań zabezpieczeń Sophos Cloud OPTIX z Twoim wskaźnikiem kontrolnym platformy Azure, przeglądanie pulpitów nawigacyjnych, tworzenie niestandardowych alertów i ulepszanie badania.  Dzięki tej funkcji można uzyskać dokładniejsze informacje o zabezpieczeniach i zgodności z chmurą w organizacji oraz zwiększyć możliwości operacji zabezpieczeń w chmurze. Integracja między Sophos Cloud OPTIX i Azure wskaźnikiem jest używana przez interfejs API REST.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-sophos-cloud-optix"></a>Konfigurowanie usługi Sophos Cloud OPTIX i łączenie się z nią

Sophos Cloud OPTIX umożliwia integrację i eksportowanie dzienników bezpośrednio do usługi Azure wskaźnikowej.

1. W portalu usługi Azure wskaźnikowego kliknij pozycję **Łączniki danych** i wybierz pozycję **Sophos Cloud OPTIX (wersja zapoznawcza)**.

1. Wybierz pozycję **Otwórz stronę łącznika**.

1. Skopiuj i Zapisz **Identyfikator obszaru roboczego** i **klucz podstawowy** na stronie łącznika.

1. Postępuj zgodnie z instrukcjami z Sophos, aby [zintegrować z Microsoft Azure wskaźnikiem](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html) CE (rozpoczynając od trzeciego kroku).

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **dzienniki niestandardowe** w tabeli *SophosCloudOptix_CL* .

Aby wykonać zapytanie dotyczące danych OPTIX w chmurze Sophos, wprowadź `SophosCloudOptix_CL` w oknie zapytania. Zobacz kartę **następne kroki** na stronie łącznik i [dokumentację Sophos](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html), aby poznać przydatne przykłady zapytań.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut. 

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia Sophos Cloud OPTIX z platformą Azure wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
