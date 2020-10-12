---
title: Połącz dane programu Microsoft Defender for Identity (dawniej Azure ATP) z platformą Azure. Microsoft Docs
description: Dowiedz się, jak przesyłać strumieniowo dzienniki z usługi Microsoft Defender pod kątem tożsamości (dawniej usługa Azure Advanced Threat Protection) (ATP) w przypadku jednego kliknięcia.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 64c1a7155d0cc4e80f97db138a0626d6e9fdc9e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89657821"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Łączenie danych z usługi Microsoft Defender dla tożsamości (dawniej ochrona przed zagrożeniami na platformie Azure)

> [!IMPORTANT]
> Łącznik usługi Microsoft Defender dla danych tożsamości na platformie Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Za pomocą jednego kliknięcia można przesyłać strumieniowo dzienniki z usługi [Microsoft Defender na potrzeby tożsamości](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) .

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik z uprawnieniami administratora globalnego lub administratora zabezpieczeń
- Musisz być klientem wersji zapoznawczej usługi Microsoft Defender, aby zapewnić tożsamość i włączyć integrację usługi Microsoft Defender pod kątem tożsamości i Microsoft Cloud App Security. Aby uzyskać więcej informacji, zobacz [Microsoft Defender for Identity Integration](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## <a name="connect-to-microsoft-defender-for-identity"></a>Łączenie z usługą Microsoft Defender dla tożsamości

Upewnij się, że [w sieci jest włączona](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)wersja zapoznawcza usługi Microsoft Defender for Identity.
Jeśli usługa Microsoft Defender dla tożsamości została wdrożona i pozyskuje dane, podejrzane alerty mogą być łatwo przesyłane strumieniowo do usługi Azure wskaźnikowej. Rozpoczęcie przesyłania strumieniowego na platformę Azure jest możliwe dopiero po 24 godzinach.


1. Aby nawiązać połączenie z usługą Microsoft Defender w celu uzyskania tożsamości na platformie Azure, musisz najpierw włączyć integrację usługi Microsoft Defender dla tożsamości i Microsoft Cloud App Security. Aby uzyskać informacje o tym, jak to zrobić, zobacz [Microsoft Defender for Identity Integration](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Microsoft Defender for Identity (wersja zapoznawcza)** .

1. Możesz wybrać, czy alerty z usługi Microsoft Defender mają być automatycznie generowane na podstawie zdarzeń na platformie Azure. W obszarze **Tworzenie zdarzeń** wybierz pozycję **Włącz** , aby włączyć domyślną regułę analityczną, która automatycznie tworzy zdarzenia z alertów generowanych w połączonej usłudze zabezpieczeń. Następnie można edytować tę regułę w obszarze **Analiza** , a następnie **aktywne reguły**.

1. Kliknij przycisk **Połącz**.

1. Aby użyć odpowiedniego schematu w Log Analytics dla alertów dotyczących tożsamości usługi Microsoft Defender, Wyszukaj pozycję **SecurityAlert**.

> [!NOTE]
> Jeśli alerty są większe niż 30 KB, wskaźnik na platformie Azure zatrzyma wyświetlanie pola jednostki w alertach.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia z usługą Microsoft Defender pod kątem tożsamości na platformie Azure wskaźnikowej. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

