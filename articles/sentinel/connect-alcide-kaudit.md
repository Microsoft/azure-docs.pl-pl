---
title: Łączenie danych Alcide kAudit z platformą Azure Microsoft Docs
description: Dowiedz się, jak połączyć dane Alcide kAudit z platformą Azure.
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
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: 121c9258353505b6147aa059d327bc711e9fede4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85368446"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Połącz swoje Alcide kAudit z platformą Azure — wskaźnikiem

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) pomaga identyfikować nietypowe zachowania Kubernetes i skupiać się na Kubernetes naruszeniach i zdarzeniach przy skróceniu czasu wykrywania. W tym artykule wyjaśniono, jak połączyć rozwiązanie Alcide kAudit z platformą Azure. Łącznik danych Alcide kAudit umożliwia łatwe przekazanie danych dziennika kAudit do usługi Azure wskaźnikowej, aby można było wyświetlać ją w skoroszytach, używać jej do tworzenia niestandardowych alertów i uwzględniania ich w celu usprawnienia badania. Integracja między programem Alcide kAudit i platformą Azure wskaźnikiem wykorzystuje interfejs API REST.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

- Użytkownik musi mieć uprawnienia do odczytu kluczy udostępnionych dla obszaru roboczego.

## <a name="configure-and-connect-alcide-kaudit"></a>Konfigurowanie i łączenie Alcide kAudit

Alcide kAudit może eksportować dzienniki bezpośrednio do platformy Azure.

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych** w menu nawigacji.

1. Wybierz pozycję **Alcide kAudit** z galerii, a następnie kliknij przycisk **Otwórz stronę łącznika** .

1. Postępuj zgodnie z instrukcjami krok po kroku przedstawionymi w [podręczniku instalacji Alcide kAudit](https://get.alcide.io/hubfs/Azure%20Sentinel%20Integration%20with%20kAudit.pdf).

1. Gdy zostanie wyświetlony monit o identyfikator obszaru roboczego i klucz podstawowy, można je skopiować ze strony Łącznik danych Alcide kAudit.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="Identyfikator obszaru roboczego i klucz podstawowy":::

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** pod następującymi typami danych w **CustomLogs**:

- **alcide_kaudit_detections_1_CL** — wykrywanie kaudit Alcide 
- Dzienniki aktywności **alcide_kaudit_activity_1_CL** Alcide kaudit
- Liczba działań w **alcide_kaudit_selections_count_1_CL** Alcide kaudit
- **alcide_kaudit_selections_details_1_CL** — szczegóły działania Alcide kaudit

Aby użyć odpowiedniego schematu w dziennikach dla Alcide kAudit, Wyszukaj wymienione powyżej typy danych.

Rozpoczęcie wyświetlania dzienników w Log Analytics może zająć więcej niż 20 minut.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia Alcide kAudit z platformą Azure. Aby w pełni wykorzystać możliwości wbudowane w ten łącznik danych, kliknij kartę **następne kroki** na stronie Łącznik danych. Znajdziesz gotowe zapytania przykładowe, aby rozpocząć Znajdowanie przydatnych informacji.

Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
