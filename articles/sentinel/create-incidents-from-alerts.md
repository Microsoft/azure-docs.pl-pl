---
title: Tworzenie zdarzeń na podstawie alertów na platformie Azure — Wskaźnikowanie | Microsoft Docs
description: Dowiedz się, jak tworzyć zdarzenia z alertów na platformie Azure — wskaźnik.
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
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 5c7c3d69bb26773171e9e0afc9f79ff25909a12a
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807296"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Automatycznie twórz zdarzenia z alertów zabezpieczeń firmy Microsoft

Alerty wyzwalane w rozwiązaniach zabezpieczeń firmy Microsoft, które są połączone z wskaźnikiem na platformie Azure, takie jak Microsoft Cloud App Security i Microsoft Defender for Identity (dawniej Azure ATP), nie umożliwiają automatycznego tworzenia zdarzeń na platformie Azure. Domyślnie po podłączeniu rozwiązania firmy Microsoft do funkcji wskaźnikowej platformy Azure wszystkie alerty wygenerowane w ramach tej usługi będą przechowywane jako dane pierwotne na platformie Azure, w tabeli alertów zabezpieczeń w obszarze roboczym wskaźnikowego platformy Azure. Następnie możesz użyć tych danych, takich jak wszystkie inne nieprzetworzone dane, które nawiązują połączenie z platformą Azure — wskaźnikiem.

Za każdym razem, gdy alert jest wyzwalany w połączonym rozwiązaniu zabezpieczeń firmy Microsoft, można łatwo skonfigurować wskaźnik na platformie Azure do automatycznego tworzenia zdarzeń, postępując zgodnie z instrukcjami w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne
Należy [połączyć rozwiązania zabezpieczeń firmy Microsoft](connect-data-sources.md#data-connection-methods) , aby umożliwić tworzenie zdarzeń z alertów usługi zabezpieczeń.

## <a name="using-microsoft-security-incident-creation-analytics-rules"></a>Korzystanie z reguł analizy tworzenia zdarzeń zabezpieczeń firmy Microsoft

Skorzystaj z wbudowanych reguł dostępnych na platformie Azure — wskaźnik wyboru, aby wybrać połączone rozwiązania zabezpieczeń firmy Microsoft, które mają być automatycznie tworzone w czasie rzeczywistym. Możesz również edytować reguły, aby zdefiniować bardziej szczegółowe opcje filtrowania, które alerty wygenerowane przez rozwiązanie zabezpieczeń firmy Microsoft powinny tworzyć zdarzenia na platformie Azure. Na przykład możesz wybrać opcję automatycznego tworzenia zdarzeń ze wskaźnikiem na platformie Azure przy użyciu alertów usługi Azure Defender o wysokiej ważności (dawniej Azure Security Center).

1. W Azure Portal w obszarze wskaźnik platformy Azure wybierz pozycję **Analiza**.

1. Wybierz kartę **Szablony reguł** , aby wyświetlić wszystkie wbudowane reguły analizy.

    ![Szablony reguł](media/incidents-from-alerts/rule-templates.png)

1. Wybierz szablon reguł usługi **Microsoft Security** Analytics, którego chcesz użyć, a następnie kliknij pozycję **Utwórz regułę**.

    ![Reguła analizy zabezpieczeń](media/incidents-from-alerts/security-analytics-rule.png)

1. Możesz zmodyfikować szczegóły reguły i wybrać filtrowanie alertów, które spowodują utworzenie incydentów według ważności alertu lub tekstu zawartego w nazwie alertu.  
      
    Na przykład w przypadku wybrania **usługi Azure Defender** (może być nadal wywoływana *Azure Security Center*) w polu **Usługa zabezpieczeń firmy Microsoft** i wybrać opcję **wysoki** w polu **Filtruj według ważności** tylko alerty usługi Azure Defender o wysokiej ważności będą automatycznie tworzyć zdarzenia na platformie Azure.  

    ![Kreator tworzenia reguły](media/incidents-from-alerts/create-rule-wizard.png)

1. Możesz również utworzyć nową regułę **zabezpieczeń firmy Microsoft** , która filtruje alerty z różnych usług zabezpieczeń firmy Microsoft, klikając pozycję **+ Utwórz** i wybierając pozycję **reguła tworzenia zdarzeń firmy Microsoft**.

    ![Reguła tworzenia zdarzenia](media/incidents-from-alerts/incident-creation-rule.png)

  Można utworzyć więcej niż jedną regułę analizy **zabezpieczeń firmy Microsoft** na typ **usługi zabezpieczeń firmy Microsoft** . Nie powoduje to utworzenia zduplikowanych zdarzeń, ponieważ Każda reguła jest używana jako filtr. Nawet jeśli alert jest zgodny z więcej niż jedną regułą analizy **zabezpieczeń firmy Microsoft** , tworzy tylko jedno zdarzenie wskaźnikowe platformy Azure.

## <a name="enable-incident-generation-automatically-during-connection"></a>Włącz automatyczne generowanie zdarzeń podczas połączenia
 W przypadku łączenia rozwiązania zabezpieczeń firmy Microsoft możesz wybrać, czy alerty z rozwiązania zabezpieczeń mają automatycznie generować zdarzenia na platformie Azure.

1. Połącz źródło danych rozwiązania zabezpieczeń firmy Microsoft. 

   ![Generuj zdarzenia zabezpieczeń](media/incidents-from-alerts/generate-security-incidents.png)

1. W obszarze **Tworzenie zdarzeń** wybierz pozycję **Włącz** , aby włączyć domyślną regułę analizy, która automatycznie tworzy zdarzenia z alertów generowanych w połączonej usłudze zabezpieczeń. Następnie można edytować tę regułę w obszarze **Analiza** , a następnie **aktywne reguły**.

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z systemem Azure — wskaźnikiem, potrzebna jest subskrypcja do Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Dowiedz się, jak dołączać [dane do usługi Azure wskaźnikowej](quickstart-onboard.md)i [uzyskiwać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
