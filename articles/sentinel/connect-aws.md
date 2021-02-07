---
title: Łączenie AWS CloudTrail z platformą Azure — wskaźnikiem Microsoft Docs
description: Za pomocą łącznika AWS Przekaż dostęp do dzienników zasobów platformy Azure, tworząc relacje zaufania między CloudTrail AWS i platformą Azure.
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
ms.date: 05/27/2020
ms.author: yelevin
ms.openlocfilehash: 3d24fa1ea046a860feb40d09a6d0a57c79371450
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807601"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Połącz wskaźnik platformy Azure z usługą AWS CloudTrail

Za pomocą łącznika AWS można przesyłać strumieniowo zdarzenia zarządzania AWS CloudTrail do platformy Azure. Ten proces połączenia deleguje dostęp do dzienników zasobów usługi Azure AWS, tworząc relacje zaufania między AWS CloudTrail i platformą Azure. Jest to realizowane w witrynie AWS, tworząc rolę, która przyznaje uprawnienia do uzyskiwania dostępu do dzienników AWS na platformie Azure.

> [!NOTE]
> AWS CloudTrail ma [wbudowane ograniczenia](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html) w interfejsie API LookupEvents. Umożliwia ona nie więcej niż dwie transakcje na sekundę (TPS) na konto, a każde zapytanie może zwrócić maksymalnie 50 rekordów. Dlatego jeśli jedna dzierżawa regularnie generuje ponad 100 rekordów na sekundę w jednym regionie, powstaną opóźnienia w pozyskiwaniu danych.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć uprawnienie do zapisu w obszarze roboczym wskaźnik platformy Azure.

> [!NOTE]
> Azure Wskaźnikowanie zbiera zdarzenia zarządzania CloudTrail ze wszystkich regionów. Zaleca się, aby nie przesyłać strumieniowo zdarzeń z jednego regionu do innego.

## <a name="connect-aws"></a>Łączenie usługi AWS 


1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie wybierz wiersz **Amazon Web Services** w tabeli i w okienku AWS z prawej strony kliknij pozycję **Otwórz stronę łącznika**.

1. Postępuj zgodnie z instrukcjami w sekcji **Konfiguracja** , wykonując następujące kroki.
 
1.  W konsoli Amazon Web Services w obszarze **zabezpieczenia, tożsamość & zgodność**, wybierz pozycję **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Wybierz **role** i wybierz pozycję **Utwórz rolę**.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Wybierz **inne konto AWS.** W polu **Identyfikator konta** wprowadź **Identyfikator konta Microsoft** (**123412341234**), który można znaleźć na stronie łącznika AWS w portalu Azure.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Upewnij się, że wybrano opcję **Wymagaj identyfikatora zewnętrznego** , a następnie wprowadź identyfikator zewnętrzny (identyfikator obszaru roboczego), który można znaleźć na stronie łącznika AWS w portalu Azure.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  W obszarze **Dołącz uprawnienia zasady** wybierz pozycję **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Wprowadź tag (opcjonalnie).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Następnie wprowadź **nazwę roli** i wybierz przycisk **Utwórz rolę** .

    ![AWS7](./media/connect-aws/aws-7.png)

1.  Na liście role Wybierz utworzoną rolę.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Skopiuj **rolę ARN**. W portalu badania na platformie Azure na ekranie Amazon Web Services łącznika wklej go do roli, **Aby dodać** pole, a następnie kliknij przycisk **Dodaj**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń AWS, Wyszukaj **AWSCloudTrail**.

    > [!IMPORTANT]
    > Od 1 grudnia 2020 pole **AwsRequestId** zostało zastąpione polem **AwsRequestId_** (Zwróć uwagę na dodanie znaku podkreślenia). Dane w starym polu **AwsRequestId** zostaną zachowane po upływie określonego okresu przechowywania danych przez klienta.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia AWS CloudTrail z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
