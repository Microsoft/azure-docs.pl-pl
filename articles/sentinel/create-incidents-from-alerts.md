---
title: Tworzenie zdarzeń na podstawie alertów w usłudze Azure Sentinel | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć zdarzenia z alertów w usłudze Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 1593b96ae8412632120e8977635a4193996ca88d
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025122"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Automatyczne tworzenie zdarzeń z alertów zabezpieczeń firmy Microsoft

Alerty wyzwalane w rozwiązaniach zabezpieczeń firmy Microsoft połączonych z usługą Azure Sentinel, takich jak Microsoft Cloud App Security i Azure Advanced Threat Protection, nie powodują automatycznego tworzenia zdarzeń w usłudze Azure Sentinel. Domyślnie po podłączeniu rozwiązania firmy Microsoft do usługi Azure Sentinel każdy alert wygenerowany w tej usłudze będzie przechowywany jako nieprzetworzone dane w usłudze Azure Sentinel w tabeli Alert zabezpieczeń w obszarze roboczym usługi Azure Sentinel. Następnie można użyć tych danych, jak wszystkie inne surowe dane, które łączysz z sentinelem.

Usługę Azure Sentinel można łatwo skonfigurować do automatycznego tworzenia zdarzeń za każdym razem, gdy alert jest wyzwalany w połączonym rozwiązaniu zabezpieczeń firmy Microsoft, postępując zgodnie z instrukcjami zawartymi w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne
Należy [połączyć rozwiązania zabezpieczeń firmy Microsoft,](connect-data-sources.md#data-connection-methods) aby umożliwić tworzenie zdarzeń z alertów usługi zabezpieczeń.

## <a name="using-microsoft-security-incident-creation-analytic-rules"></a>Używanie reguł analitycznych tworzenia zdarzeń zabezpieczeń firmy Microsoft

Użyj wbudowanych reguł dostępnych w usłudze Azure Sentinel, aby wybrać połączone rozwiązania zabezpieczeń firmy Microsoft, które powinny automatycznie tworzyć zdarzenia usługi Azure Sentinel w czasie rzeczywistym. Można również edytować reguły, aby zdefiniować bardziej szczegółowe opcje filtrowania, które alerty generowane przez rozwiązanie zabezpieczeń firmy Microsoft powinny tworzyć zdarzenia w usłudze Azure Sentinel. Na przykład można utworzyć zdarzenia usługi Azure Sentinel automatycznie tylko z alertów usługi Azure Security Center o wysokiej ważności.

1. W witrynie Azure portal w obszarze Azure Sentinel wybierz pozycję **Analytics**.

1. Wybierz kartę **Szablony reguł,** aby wyświetlić wszystkie wbudowane reguły analityczne.

    ![Szablony reguł](media/incidents-from-alerts/rule-templates.png)

1. Wybierz szablon reguły analizy **zabezpieczeń firmy Microsoft,** którego chcesz użyć, a następnie kliknij pozycję **Utwórz regułę**.

    ![Reguła analizy zabezpieczeń](media/incidents-from-alerts/security-analytics-rule.png)

1. Można zmodyfikować szczegóły reguły i wybrać filtrowanie alertów, które będą tworzyć zdarzenia według ważności alertu lub tekstu zawartego w nazwie alertu.  
      
    Jeśli na przykład wybierzesz **Usługę Azure Security Center** w polu Usługi zabezpieczeń firmy **Microsoft** i wybierzesz **opcję Wysoki** w polu **Filtruj według ważności,** tylko alerty usługi Azure Security Center o wysokiej ważności automatycznie będą tworzyć zdarzenia w usłudze Azure Sentinel.  

    ![Kreator tworzenia reguł](media/incidents-from-alerts/create-rule-wizard.png)

1. Można również utworzyć nową regułę **zabezpieczeń firmy Microsoft,** która filtruje alerty z różnych usług zabezpieczeń firmy Microsoft, klikając **przycisk +Utwórz** i wybierając **regułę tworzenia zdarzeń firmy Microsoft**.

    ![Reguła tworzenia incydentów](media/incidents-from-alerts/incident-creation-rule.png)

  Można utworzyć więcej niż jedną regułę analityczną **zabezpieczeń firmy Microsoft** dla typu usługi zabezpieczeń firmy **Microsoft.** Nie tworzy to zduplikowanych zdarzeń, ponieważ każda reguła jest używana jako filtr. Nawet jeśli alert pasuje do więcej niż jednej reguły analitycznej **zabezpieczeń firmy Microsoft,** tworzy tylko jeden incydent usługi Azure Sentinel.

## <a name="enable-incident-generation-automatically-during-connection"></a>Automatyczne włączanie generowania zdarzeń podczas połączenia
 Po podłączeniu rozwiązania zabezpieczeń firmy Microsoft można wybrać, czy alerty z rozwiązania zabezpieczeń mają automatycznie generować zdarzenia w usłudze Azure Sentinel.

1. Połącz źródło danych rozwiązania zabezpieczeń firmy Microsoft. 

   ![Generowanie incydentów związanych z bezpieczeństwem](media/incidents-from-alerts/generate-security-incidents.png)

1. W obszarze **Tworzenie zdarzeń** wybierz **pozycję Włącz,** aby włączyć domyślną regułę analityczną, która automatycznie tworzy zdarzenia z alertów generowanych w połączonej usłudze zabezpieczeń. Następnie można edytować tę regułę w obszarze **Analytics,** a następnie **Aktywne reguły**.

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć korzystanie z usługi Azure Sentinel, potrzebujesz subskrypcji platformy Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Dowiedz się, jak [dołączać dane do usługi Azure Sentinel](quickstart-onboard.md)i uzyskać wgląd w dane i potencjalne [zagrożenia.](quickstart-get-visibility.md)
