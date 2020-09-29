---
title: Usługa Azure Defender dla Key Vault — korzyści i funkcje
description: Dowiedz się więcej o zaletach i funkcjach usługi Azure Defender dla Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 6649a8d470a75922aac423bf0b411163bdd79f71
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449095"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Wprowadzenie do usługi Azure Defender dla Key Vault

Azure Key Vault to usługa w chmurze, która chroni klucze szyfrowania i wpisy tajne, takie jak certyfikaty, ciągi połączeń i hasła. 

Włącz **usługę Azure Defender dla Key Vault** na platformie Azure — natywna, zaawansowana ochrona przed zagrożeniami dla Azure Key Vault, zapewniając dodatkową warstwę analizy zabezpieczeń. 

## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólnie dostępna (GA)|
|Wpisaną|**Usługa Azure Defender dla Key Vault** jest rozliczana zgodnie z opisem na [stronie cennika](security-center-pricing.md)|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Nie](./media/icons/no-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Jakie korzyści ma usługa Azure Defender dla Key Vault?

Usługa Azure Defender wykrywa nietypowe i potencjalnie szkodliwe próby dostępu do kont Key Vault lub ich wykorzystania. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń bez konieczności postanowień eksperta w zakresie bezpieczeństwa, bez konieczności zarządzania systemami monitorowania zabezpieczeń innych firm.  

W przypadku wystąpienia nietypowych działań usługa Azure Defender wyświetla alerty i opcjonalnie wysyła je za pośrednictwem poczty e-mail do odpowiednich członków organizacji. Te alerty obejmują szczegóły podejrzanego działania i zalecenia dotyczące sposobu badania i korygowania zagrożeń. 

## <a name="azure-defender-for-key-vault-alerts"></a>Alerty usługi Azure Defender dla Key Vault
Po otrzymaniu alertu z usługi Azure Defender dla Key Vault zalecamy zbadanie alertu i jego odpowiedź zgodnie z opisem w artykule [reagowanie na usługę Azure Defender dla Key Vault](defender-for-key-vault-usage.md). Usługa Azure Defender dla Key Vault chroni aplikacje i poświadczenia, więc nawet w przypadku znajomości aplikacji lub użytkownika, który wyzwolił alert, ważne jest, aby sprawdzić sytuacje związane z każdym alertem.

Alerty są wyświetlane na stronie **zabezpieczenia** Key Vault, na pulpicie nawigacyjnym usługi Azure Defender i na stronie alertów Security Center.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Strona zabezpieczeń Azure Key Vault":::


> [!TIP]
> Możesz symulować usługę Azure Defender pod kątem alertów Key Vault, postępując zgodnie z instrukcjami podanymi w temacie [Sprawdzanie poprawności Azure Key Vault wykrywania zagrożeń w Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336).


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o usłudze Azure Defender dla Key Vault.

W przypadku pokrewnego materiału zapoznaj się z następującymi artykułami: 

- [Key Vault alerty zabezpieczeń](alerts-reference.md#alerts-azurekv)— sekcja Key Vault w tabeli referencyjnej dla wszystkich Azure Security Centerych alertów
- [Eksportowanie alertów do SIEM](continuous-export.md)
- [Pomijanie alertów z usługi Azure Defender](alerts-suppression-rules.md)