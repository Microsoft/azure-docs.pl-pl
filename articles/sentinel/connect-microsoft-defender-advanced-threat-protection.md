---
title: Łączenie danych programu Microsoft Defender dla punktów końcowych z platformą Azure Microsoft Docs
description: Dowiedz się, jak połączyć dane usługi Microsoft Defender dla punktów końcowych (dawniej informacje o usłudze Microsoft Defender ATP) z platformą Azure.
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
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 0db4e0fe0472c75f1eae392980ae697f53007244
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98623370"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Łączenie alertów z usługi Microsoft Defender dla punktu końcowego (dawniej usługa Microsoft Defender ATP)

> [!IMPORTANT]
>
> - Usługa **Microsoft Defender dla punktu końcowego** była znana wcześniej jako usługa **Microsoft Defender Advanced Threat Protection** lub **MDATP**.
>
>     W danym okresie można zobaczyć starą nazwę, która jest nadal używana w produkcie (w tym Łącznik danych na platformie Azure.

Łącznik usługi [Microsoft Defender dla punktu końcowego](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) umożliwia strumieniowe przesyłanie alertów z programu Microsoft Defender dla punktu końcowego do platformy Azure. Pozwoli to na dokładniejsze przeanalizowanie zdarzeń zabezpieczeń w organizacji oraz kompilację elementy PlayBook na potrzeby skutecznej i natychmiastowej reakcji.

> [!NOTE]
>
> Aby pozyskać nowe nieprzetworzone dzienniki danych z usługi Microsoft Defender dla [zaawansowanego polowania](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview)punktu końcowego, Użyj nowego łącznika usługi Microsoft 365 Defender (dawniej jest znana ochrona przed zagrożeniami firmy Microsoft, [Zobacz dokumentację](./connect-microsoft-365-defender.md)).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć ważną licencję usługi Microsoft Defender for Endpoint, zgodnie z opisem w temacie [Konfigurowanie usługi Microsoft Defender for Endpoint Deployment](/windows/security/threat-protection/microsoft-defender-atp/licensing). 

- Musisz być administratorem globalnym lub administratorem zabezpieczeń w dzierżawie wskaźnikowej platformy Azure.

## <a name="connect-to-microsoft-defender-for-endpoint"></a>Łączenie z usługą Microsoft Defender dla punktu końcowego

Jeśli program Microsoft Defender for Endpoint został wdrożony i pozyskuje dane, alerty można łatwo przesłać strumieniowo do usługi Azure wskaźnikowej.

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych**, wybierz pozycję **Microsoft Defender for Endpoint** (może być nadal wywoływana usługa *Microsoft Defender Advanced Threat Protection*) z galerii, a następnie wybierz pozycję **Otwórz stronę łącznika**.

1. Kliknij przycisk **Połącz**. 

1. Aby wysłać zapytanie dotyczące alertów usługi Microsoft Defender dla punktów końcowych w **dziennikach**, wprowadź **SecurityAlert** w oknie zapytania i Dodaj filtr, gdzie **Nazwa dostawcy** to **MDATP**.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia z usługą Microsoft Defender dla punktu końcowego z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](./tutorial-detect-threats-built-in.md).