---
title: Podłącz dane wykrywania AI Vectra do usługi Azure wskaźnikowego | Microsoft Docs
description: Dowiedz się, jak połączyć dane z programu AI Vectra Detection z platformą Azure.
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
ms.openlocfilehash: 964bab2b81f6ce69e77fd69cab55f6d1894e4edb
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85368450"
---
# <a name="connect-ai-vectra-detect-to-azure-sentinel"></a>Połącz wykrywanie Vectra AI z platformą Azure — wskaźnik

W tym artykule wyjaśniono, jak połączyć urządzenie z [wykrywaniem AI Vectra](https://www.vectra.ai/product/cognito-detect) z platformą Azure. Łącznik danych Vectra wykrywania AI umożliwia łatwe przełączenie danych AI Vectra do systemu Azure, aby można było wyświetlać je w skoroszytach, używać go do tworzenia alertów niestandardowych i dołączać je do usprawnienia badania.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-your-ai-vectra-detect-appliance-to-send-cef-messages"></a>Skonfiguruj urządzenie wykrywające AI Vectra do wysyłania komunikatów CEF  

Skonfiguruj wykrywanie Vectra AI do przesyłania dalej CEFych komunikatów dziennika systemu do obszaru roboczego Log Analytics za pośrednictwem usługi przesyłania dalej dziennika systemowego skonfigurowanej w [kroku 1: wdrażanie usługi przesyłania dalej dzienników](connect-cef-agent.md).

1. W interfejsie Vectra przejdź do opcji Ustawienia > powiadomienia i wybierz pozycję Edytuj konfigurację dziennika systemowego. Postępuj zgodnie z poniższymi instrukcjami, aby skonfigurować połączenie:

    - Dodaj nowe miejsce docelowe (nazwa hosta [usługi przesyłania dalej dzienników](connect-cef-agent.md))
    - Ustaw port jako **514**
    - Ustaw protokół jako **UDP**
    - Ustaw format na **CEF**
    - Ustaw typy dzienników (zaznacz wszystkie dostępne typy dzienników)
    - Kliknij pozycję **Zapisz**

2. Możesz kliknąć przycisk **test** , aby wymusić wysłanie niektórych zdarzeń testowych do usługi przesyłania dalej dzienników.

3. Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń wykrywania Vectra AI, Wyszukaj pozycję **CommonSecurityLog**.

4. Przejdź do [kroku 3: weryfikowanie łączności](connect-cef-verify.md).

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób łączenia urządzeń z wykrywaniem AI Vectra na platformie Azure. Aby w pełni wykorzystać możliwości wbudowane w ten łącznik danych, kliknij kartę **następne kroki** na stronie Łącznik danych. Znajdziesz gotowe zapytania przykładowe, aby rozpocząć Znajdowanie przydatnych informacji.

Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
