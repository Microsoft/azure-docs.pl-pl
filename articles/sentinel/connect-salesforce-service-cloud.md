---
title: Łączenie danych w chmurze usługi Salesforce z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak używać łącznika danych w chmurze usługi Salesforce do ściągania dzienników usług Salesforce do platformy Azure. Wyświetlaj dane usługi Salesforce w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.openlocfilehash: 1efd91d92bac1bc1f39d82aaa0cc71daa0275f8e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570544"
---
# <a name="connect-your-salesforce-service-cloud-to-azure-sentinel"></a>Połącz chmurę usługi Salesforce z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik chmury usługi Salesforce jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

W tym artykule wyjaśniono, jak połączyć rozwiązanie w chmurze usługi Salesforce z platformą Azure — wskaźnikiem. Łącznik danych w chmurze usługi Salesforce pozwala łatwo połączyć dane usług Salesforce z platformą kontroli jakości platformy Azure, aby można było wyświetlać ją w skoroszytach, używać jej do tworzenia niestandardowych alertów i uwzględniania ich w celu usprawnienia badania. Integracja między chmurą usług Salesforce i usługą Azure wskaźnikiem IT korzysta z interfejsu API REST.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

- Użytkownik musi mieć uprawnienia do odczytu kluczy udostępnionych dla obszaru roboczego. [Dowiedz się więcej o kluczach obszarów roboczych](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Aby można było utworzyć aplikacja funkcji, musisz mieć uprawnienia do odczytu i zapisu w Azure Functions. [Dowiedz się więcej o Azure Functions](../azure-functions/index.yml).

- Wymagane są następujące poświadczenia interfejsu API REST usługi Salesforce: **Nazwa użytkownika interfejsu** API usługi Salesforce, **hasło interfejsu API** usługi Salesforce, **token zabezpieczający** usługi Salesforce, **klucz klienta** usługi Salesforce, **wpis tajny klienta usługi Salesforce**. [Dowiedz się więcej o interfejsie API REST usługi Salesforce](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm).

## <a name="configure-and-connect-salesforce-service-cloud"></a>Konfigurowanie i łączenie chmury usługi Salesforce

Chmura usług Salesforce umożliwia integrację i eksportowanie dzienników bezpośrednio do usługi Azure wskaźnikowej.

1. W menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Łączniki danych**.

1. W galerii **łączników danych** wybierz pozycję **Usługa Salesforce Cloud (wersja zapoznawcza)** , a następnie **Otwórz stronę łącznik**.

1. Wykonaj kroki opisane w sekcji **Konfiguracja** na stronie łącznik.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **CustomLogs** w `SalesforceServiceCloud_CL` tabeli.

Niektóre przydatne przykładowe zapytania znajdują się na karcie **następne kroki** na stronie łącznik.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z chmurą usług Salesforce z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.