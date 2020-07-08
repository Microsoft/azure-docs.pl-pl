---
title: Łączenie danych usługi Microsoft Defender ATP z platformą Azure — wskaźnikiem Microsoft Docs
description: Dowiedz się, jak połączyć dane z zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender do platformy Azure.
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
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80756353"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Łączenie alertów z poziomu zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender 


> [!IMPORTANT]
> Pozyskiwanie alertów zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Łącznik [zaawansowanej ochrony przed zagrożeniami w usłudze Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) umożliwia strumieniowe przesyłanie alertów z usługi Microsoft Defender Advanced Threat Protection na platformę Azure. Pozwoli to na dokładniejsze przeanalizowanie zdarzeń zabezpieczeń w organizacji oraz kompilację elementy PlayBook na potrzeby skutecznej i natychmiastowej reakcji.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć ważną licencję na usługę Microsoft Defender Advanced Threat Protection, zgodnie z opisem w temacie [set up the Microsoft Defender ATP Deployment](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Musisz być administratorem lub administratorem zabezpieczeń w dzierżawie wskaźnikowej platformy Azure.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Łączenie z usługą Microsoft Defender Advanced Threat Protection

Jeśli Zaawansowana ochrona przed zagrożeniami w usłudze Microsoft Defender zostanie wdrożona i pozyskuje dane, alerty mogą być łatwo przesyłane strumieniowo do platformy Azure.


1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych**, kliknij kafelek **Microsoft Defender Advanced Threat Protection** , a następnie wybierz **stronę Otwórz łącznik**.
1. Kliknij pozycję **Połącz**. 
1. Aby użyć odpowiedniego schematu w Log Analytics dla alertów usługi Defender ATP, wyszukaj ciąg **SecurityAlert** , a **Nazwa dostawcy** to **MDATP**.




## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia z usługą Microsoft Defender ATP do usługi Azure wskaźnikowej. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).
