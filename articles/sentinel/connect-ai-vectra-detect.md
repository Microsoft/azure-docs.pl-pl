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
ms.openlocfilehash: c57f4a49ac89f1347fc88f8bacddce3abab7e44e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87038226"
---
# <a name="connect-ai-vectra-detect-to-azure-sentinel"></a>Połącz wykrywanie Vectra AI z platformą Azure — wskaźnik

> [!IMPORTANT]
> Łącznik danych Vectra wykrywania systemu AI na platformie Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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
