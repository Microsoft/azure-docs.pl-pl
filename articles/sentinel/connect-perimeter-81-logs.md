---
title: Połącz dane obwodu 81 z platformą Azure wskaźnikowego | Microsoft Docs
description: Dowiedz się, jak połączyć dane obwodowe 81 z platformą Azure.
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
ms.openlocfilehash: a0eb234d9677c479d2cb404b065da408854168e3
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85368440"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>Łączenie dzienników aktywności obwodowej 81 z platformą Azure

W tym artykule wyjaśniono, jak połączyć urządzenie [dziennika aktywności obwodu 81](https://www.perimeter81.com/) z platformą Azure. Łącznik dzienników aktywności obwodowej 81 pozwala łatwo przenieść dane z obwodu 81 do platformy Azure, aby można było wyświetlać je w skoroszytach, używać do tworzenia alertów niestandardowych i dołączać je do usprawnienia badania.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

- Użytkownik musi mieć uprawnienia do odczytu kluczy udostępnionych dla obszaru roboczego.

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>Konfigurowanie i łączenie dzienników aktywności obwodowej 81

Dzienniki aktywności obwodowej 81 umożliwiają integrację i eksportowanie dzienników bezpośrednio do funkcji wskaźnikowej platformy Azure.

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych** w menu nawigacji.

1. Wybierz pozycję **obwodowe dzienniki aktywności 81** z galerii, a następnie kliknij przycisk **Otwórz stronę łącznika** .

1. Na stronie łącznika dzienników aktywności obwodowej 81 Skopiuj **Identyfikator obszaru roboczego** i **klucz podstawowy** i wklej je na obrzeżu 81, [zgodnie z instrukcjami w tym miejscu](https://support.perimeter81.com/hc/en-us/articles/360012680780).

1. Po zakończeniu tych instrukcji zobaczysz połączone typy danych na stronie łącznika Azure wskaźnikowego.

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w obszarze **CustomLogs**  -  **Perimeter81_CL**.

Rozpoczęcie wyświetlania dzienników może zająć więcej niż 20 minut.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia dzienników aktywności obwodowej 81 z platformą Azure. Aby w pełni wykorzystać możliwości wbudowane w ten łącznik danych, kliknij kartę **następne kroki** na stronie Łącznik danych. Znajdziesz gotowy skoroszyt i kilka przykładowych zapytań, dzięki czemu można rozpocząć Znajdowanie przydatnych informacji.

Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
