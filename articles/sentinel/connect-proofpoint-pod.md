---
title: Łączenie danych zabezpieczeń poczty E-mail na żądanie Proofpoint na platformie Azure — wskaźnik | Microsoft Docs
description: Dowiedz się, jak korzystać z łącznika danych bezpieczeństwa poczty E-mail Proofpoint na żądanie, aby pobierać z dzienników zabezpieczeń poczty E-mail na platformę Azure. Wyświetlaj dane dotyczące zabezpieczeń poczty E-mail w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 73a9d9c7ab321aebd615922e5d4395c0318e809c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100580442"
---
# <a name="connect-your-proofpoint-on-demand-email-security-pod-solution-to-azure-sentinel"></a>Połącz swoje rozwiązanie z zabezpieczeniami poczty E-mail Proofpoint na żądanie (POD) z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik zabezpieczeń poczty E-mail Proofpoint na żądanie jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

W tym artykule wyjaśniono, jak połączyć urządzenie zabezpieczeń poczty E-mail Proofpoint na żądanie z platformą Azure. Łącznik danych POD dane pozwala łatwo połączyć się z dziennikami na platformie Azure, tak aby można było wyświetlać dane w skoroszytach, używać ich do tworzenia niestandardowych alertów i uwzględniania ich w celu usprawnienia badania.  Integracja między usługą Proofpoint na żądanie zabezpieczeń poczty E-mail i wskaźnikiem na platformie Azure oznacza korzystanie z interfejsu API protokołu WebSocket.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

- Użytkownik musi mieć uprawnienia do odczytu kluczy udostępnionych dla obszaru roboczego. [Dowiedz się więcej o kluczach obszarów roboczych](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Aby można było utworzyć aplikacja funkcji, musisz mieć uprawnienia do odczytu i zapisu w Azure Functions. [Dowiedz się więcej o Azure Functions](../azure-functions/index.yml).

- Wymagane są następujące poświadczenia interfejsu API protokołu WebSocket: ProofpointClusterID, ProofpointToken. [Dowiedz się więcej o interfejsie API protokołu WebSocket](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API).

## <a name="configure-and-connect-proofpoint-on-demand-email-security"></a>Konfigurowanie i łączenie Proofpoint na żądanie zabezpieczeń poczty E-mail

Proofpoint na żądanie bezpieczeństwo poczty E-mail umożliwia integrację i eksportowanie dzienników bezpośrednio do usługi Azure wskaźnikowej.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. W galerii **łączników danych** wybierz pozycję **Proofpoint na żądanie zabezpieczenia poczty e-mail (wersja zapoznawcza)** , a następnie **Otwórz stronę łącznik**.

1. Wykonaj kroki opisane w sekcji **Konfiguracja** na stronie łącznik.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w obszarze *dzienniki niestandardowe* w następujących tabelach:
- `ProofpointPOD_message_CL`
- `ProofpointPOD_maillog_CL`

Niektóre przydatne przykładowe zapytania znajdują się na karcie **następne kroki** na stronie łącznik.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 60 minut.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia zabezpieczeń poczty E-mail Proofpoint na żądanie z usługą Azure wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.