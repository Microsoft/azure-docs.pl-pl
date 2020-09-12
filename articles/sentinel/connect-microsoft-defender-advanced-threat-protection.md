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
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 69ce6cb42fd18364a7b93faa9cc01d1f793d5cd6
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657546"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Łączenie alertów z usługi Microsoft Defender dla punktu końcowego (dawniej usługa Microsoft Defender ATP) 


> [!IMPORTANT]
> Pozyskiwanie alertów usługi Microsoft Defender dla punktów końcowych jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Łącznik usługi [Microsoft Defender dla punktu końcowego](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) umożliwia strumieniowe przesyłanie alertów z programu Microsoft Defender dla punktu końcowego do platformy Azure. Pozwoli to na dokładniejsze przeanalizowanie zdarzeń zabezpieczeń w organizacji oraz kompilację elementy PlayBook na potrzeby skutecznej i natychmiastowej reakcji.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć ważną licencję usługi Microsoft Defender for Endpoint, zgodnie z opisem w temacie [Konfigurowanie usługi Microsoft Defender for Endpoint Deployment](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Musisz być administratorem lub administratorem zabezpieczeń w dzierżawie wskaźnikowej platformy Azure.


## <a name="connect-to-microsoft-defender-for-endpoint"></a>Łączenie z usługą Microsoft Defender dla punktu końcowego

Jeśli program Microsoft Defender for Endpoint został wdrożony i pozyskuje dane, alerty można łatwo przesłać strumieniowo do usługi Azure wskaźnikowej.


1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych**, wybierz pozycję **Microsoft Defender for Endpoint** (może być nadal wywoływana usługa Microsoft Defender Advanced Threat Protection) z galerii, a następnie wybierz pozycję **Otwórz stronę łącznika**.
1. Kliknij przycisk **Połącz**. 
1. Aby użyć odpowiedniego schematu w Log Analytics dla alertów usługi Defender ATP, wyszukaj ciąg **SecurityAlert** , a **Nazwa dostawcy** to **MDATP**.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia z usługą Microsoft Defender dla punktu końcowego z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).
