---
title: Połącz dane programu Microsoft Defender dla pakietu Office 365 (dawniej pakietem Office 365 ATP) do usługi Azure wskaźnikowego | Microsoft Docs
description: Pozyskiwanie danych programu Microsoft Defender dla pakietu Office 365 w usłudze Azure wskaźnikowej w celu uzyskania wglądu i kompilowania zautomatyzowanych scenariuszy odpowiedzi.
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
ms.date: 09/06/2020
ms.author: yelevin
ms.openlocfilehash: 5ac72c9861e06773ffab87dcbf4e730562b798fe
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91346601"
---
# <a name="connect-alerts-from-microsoft-defender-for-office-365"></a>Łączenie alertów z usługi Microsoft Defender dla pakietu Office 365 

> [!IMPORTANT]
>
> - **Program Microsoft Defender for Office 365** był wcześniej znany jako **pakiet Office 365 Advanced Threat Protection (ATP)**.
>
>     W danym okresie można zobaczyć starą nazwę, która jest nadal używana w produkcie (w tym Łącznik danych na platformie Azure.
>
> - Pozyskiwanie alertów usługi Microsoft Defender dla pakietu Office 365 jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Usługa [Microsoft Defender dla pakietu Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-advanced-threat-protection-service-description) chroni organizację przed zerowym i innymi zaawansowanymi zagrożeniami, które są nieznane w wiadomościach e-mail, w łączach złośliwych adresów URL i narzędziach do współpracy. Dzięki przeniesieniu alertów programu Microsoft Defender for Office 365 do oprogramowania Azure wskaźnikowego, będziesz mieć możliwość użycia informacji na temat wiadomości e-mail, udostępniania plików i zagrożeń opartych na adresie URL w operacjach zabezpieczeń. Następnie można bardziej kompleksowo analizować zdarzenia zabezpieczeń w organizacji i kompilować elementy PlayBook na potrzeby skutecznej i natychmiastowej reakcji.

Łącznik importuje następujące alerty:

- Wykryto potencjalnie złośliwy adres URL 

- Wiadomości e-mail zawierające złośliwe oprogramowanie usunięte po dostarczeniu

- Wiadomości e-mail zawierające adresy URL witryn wyłudzających informacje usunięte po dostarczeniu 

- Wiadomość e-mail zgłoszona przez użytkownika jako złośliwe oprogramowanie lub wyłudzanie informacji 

- Wykryto podejrzane wzorce wysyłania wiadomości e-mail 

- Użytkownik nie ma uprawnienia do wysyłania wiadomości e-mail 

Te alerty mogą być widoczne dla klientów pakietu Office w **Centrum zabezpieczeń i zgodności pakietu Office**.

## <a name="prerequisites"></a>Wymagania wstępne

- Po włączeniu łącznika musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym wskaźnik platformy Azure.

- Musisz być administratorem globalnym lub administratorem zabezpieczeń w dzierżawie obszaru roboczego wskaźnikowego platformy Azure.

- Konieczne jest posiadanie ważnej licencji dla [pakietu office 365 ATP (plan 2](https://docs.microsoft.com/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) ) (dołączonego do pakietów Office 365 E5, Office 365 A5 i Microsoft 365 E5 i dostępnych do zakupu osobno). 

## <a name="connect-to-microsoft-defender-for-office-365"></a>Nawiązywanie połączenia z usługą Microsoft Defender dla pakietu Office 365

Jeśli zostanie wdrożona usługa Microsoft Defender dla pakietu Office 365 i jeśli zasady zostały skonfigurowane, alerty mogą być łatwo pozyskiwane na platformie Azure.

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** z menu nawigacji.

1. Wybierz pozycję **Microsoft Defender dla pakietu Office 365** (w galerii łączników nadal może być wywoływana usługa *Office 365 Advanced Threat Protection*) i wybierz pozycję **Otwórz stronę łącznika**.

1. W sekcji **Konfiguracja** kliknij pozycję **Połącz**. 

1. W sekcji **Tworzenie incydentów** kliknij pozycję **Włącz**.

1. Aby użyć odpowiedniego schematu do wykonywania zapytań dotyczących alertów usługi Office 365 ATP, wyszukaj ciąg **SecurityAlert** i określ **nazwę dostawcy** jako **OATP**.

1. Wybierz kartę **następne kroki** , aby zapoznać się z przykładami zapytań i szablonami reguł analizy powiązane z łącznikiem Microsoft Defender for Office 365.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób nawiązywania połączenia z usługą Microsoft Defender dla pakietu Office 365 z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).
