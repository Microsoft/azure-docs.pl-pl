---
title: Łączenie danych Alcide kAudit z Azure Sentinel| Microsoft Docs
description: Dowiedz się, jak połączyć dane Alcide kAudit z Azure Sentinel.
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
ms.openlocfilehash: b146e228de13109975a76b0e4c6c9fd183fd362d
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600408"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Łączenie aplikacji Alcide kAudit z Azure Sentinel

> [!IMPORTANT]
> Łącznik danych Alcide kAudit w Azure Sentinel jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) pomaga identyfikować anomalie zachowania kubernetes i skoncentrować się na naruszeniach i zdarzeniach kubernetes przy jednoczesnym skróceniu czasu wykrywania. W tym artykule wyjaśniono, jak połączyć rozwiązanie Alcide kAudit z Azure Sentinel. Łącznik danych Alcide kAudit pozwala łatwo wprowadzić dane dziennika kAudit do usługi Azure Sentinel, aby można było wyświetlać je w skoroszytach, używać go do tworzenia alertów niestandardowych i dołączać je w celu usprawnienia badania. Integracja między Alcide kAudit i Azure Sentinel korzysta z interfejsu API REST.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym są uruchomione Azure Sentinel.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w Azure Sentinel roboczym.

- Musisz mieć uprawnienia do odczytu kluczy udostępnionych dla obszaru roboczego.

## <a name="configure-and-connect-alcide-kaudit"></a>Konfigurowanie i łączenie alcide kAudit

Alcide kAudit może eksportować dzienniki bezpośrednio do Azure Sentinel.

1. W portalu Azure Sentinel kliknij pozycję **Łączniki danych** w menu nawigacji.

1. Wybierz **pozycję Alcide kAudit** z galerii, a następnie kliknij **przycisk Otwórz stronę łącznika.**

1. Postępuj zgodnie z instrukcjami krok po kroku podanymi w przewodniku instalacji aplikacji [Alcide kAudit.](https://awesomeopensource.com/project/alcideio/kaudit?categoryPage=29#before-installing-alcide-kaudit)

1. Po proszeniu o identyfikator obszaru roboczego i klucz podstawowy możesz skopiować je ze strony łącznika danych Alcide kAudit.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="Identyfikator obszaru roboczego i klucz podstawowy":::

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane są wyświetlane w obszarze **Dzienniki** w obszarze następujących typów danych w **dziennikach niestandardowych:**

- **alcide_kaudit_detections_1_CL** — wykrywanie Alcide kAudit 
- **alcide_kaudit_activity_1_CL** — dzienniki aktywności Alcide kAudit
- **alcide_kaudit_selections_count_1_CL** — liczba działań Alcide kAudit
- **alcide_kaudit_selections_details_1_CL** — szczegóły działania Alcide kAudit

Aby użyć odpowiedniego schematu w dziennikach dla Alcide kAudit, wyszukaj typy danych wymienione powyżej.

Może upłynieć do 20 minut, dopóki dzienniki nie zaczną pojawiać się w u użytkownikach usługi Log Analytics.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie opisano sposób łączenia aplikacji Alcide kAudit z Azure Sentinel. Aby w pełni wykorzystać możliwości wbudowane w ten łącznik danych, kliknij kartę **Następne** kroki na stronie łącznika danych. Znajdziesz tam kilka gotowych do użycia przykładowych zapytań, aby rozpocząć znajdowanie przydatnych informacji.

Aby dowiedzieć się więcej o Azure Sentinel, zobacz następujące artykuły:

- Dowiedz się, [jak uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpoczynanie [wykrywania zagrożeń za pomocą Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Monitoruj dane](tutorial-monitor-your-data.md) przy użyciu skoroszytów.
