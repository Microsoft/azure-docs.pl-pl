---
title: Jak reagować na alerty usługi Azure Defender dla usługi Key Vault
description: Dowiedz się więcej na temat kroków niezbędnych do reagowania na alerty z usługi Azure Defender dla Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 67c556e44f07240b1ad1bcde61f40042da46def8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96122197"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>Reagowanie na alerty usługi Azure Defender dla usługi Key Vault
Po otrzymaniu alertu z usługi Azure Defender dla Key Vault zalecamy badanie alertu i jego odpowiedź zgodnie z poniższym opisem. Usługa Azure Defender dla usługi Key Vault chroni aplikacje i poświadczenia, a więc nawet jeśli wiesz, która aplikacja wyzwoliła alert lub który użytkownik to zrobił, konieczne jest sprawdzenie okoliczności każdego alertu.  

Każdy alert z usługi Azure Defender dla Key Vault obejmuje następujące elementy:

- Identyfikator obiektu
- Główna nazwa użytkownika lub adres IP podejrzanego zasobu

> [!TIP]
> W zależności od *typu* dostępu, który wystąpił, niektóre pola mogą być niedostępne. Jeśli na przykład dostęp do usługi Key Vault został uzyskany przez aplikację, powiązana główna nazwa użytkownika nie zostanie wyświetlona. Jeśli ruch pochodził spoza platformy Azure, identyfikator obiektu nie zostanie wyświetlony.

## <a name="step-1-contact"></a>Krok 1. Kontakt

1. Sprawdź, czy ruch pochodzi z dzierżawy platformy Azure. Jeśli Zapora magazynu kluczy jest włączona, prawdopodobnie masz dostęp do użytkownika lub aplikacji, która wyzwoliła ten alert.
1. Jeśli nie możesz zweryfikować źródła ruchu, przejdź do [kroku 2. Natychmiastowe środki zaradcze](#step-2-immediate-mitigation).
1. Jeśli możesz zidentyfikować źródło ruchu w dzierżawie, skontaktuj się z użytkownikiem lub właścicielem aplikacji. 

> [!CAUTION]
> Usługa Azure Defender dla Key Vault została zaprojektowana w celu ułatwienia identyfikacji podejrzanych działań spowodowanych przez skradzione poświadczenia. **Nie** odrzucaj alertu po prostu, ponieważ rozpoznajesz użytkownika lub aplikację. Skontaktuj się z właścicielem aplikacji lub użytkownikiem i sprawdź, czy działanie było wiarygodne. W razie potrzeby można utworzyć regułę pomijania w celu wyeliminowania szumów. Dowiedz się więcej w temacie [pomijanie alertów z usługi Azure Defender](alerts-suppression-rules.md).


## <a name="step-2-immediate-mitigation"></a>Krok 2. Natychmiastowe łagodzenie 
Jeśli nie rozpoznajesz użytkownika lub aplikacji lub uważasz, że dostęp nie powinien być autoryzowany:

- Jeśli ruch pochodzi z nierozpoznanego adresu IP:
    1. Włącz zaporę Azure Key Vault zgodnie z opisem w temacie [Konfigurowanie zapór Azure Key Vault i sieci wirtualnych](../key-vault/general/network-security.md).
    1. Skonfiguruj zaporę przy użyciu zaufanych zasobów i sieci wirtualnych.

- Jeśli źródłem alertu była nieautoryzowana aplikacja lub podejrzany użytkownik:
    1. Otwórz ustawienia zasad dostępu magazynu kluczy.
    1. Usuń odpowiedni podmiot zabezpieczeń lub Ogranicz operacje, które może wykonywać podmiot zabezpieczeń.  

- Jeśli źródło alertu ma rolę Azure Active Directory w dzierżawie:
    1. Skontaktuj się z administratorem.
    1. Ustal, czy istnieje potrzeba zmniejszenia lub odwoływania uprawnień Azure Active Directory.

## <a name="step-3-identify-impact"></a>Krok 3. Określ wpływ 
Jeśli wpływ został skorygowany, zbadaj wpisy tajne w magazynie kluczy, których dotyczy ten problem:
1. Otwórz stronę "zabezpieczenia" na Azure Key Vault i Wyświetl alert wyzwalany.
1. Wybierz konkretny alert, który został wyzwolony.
    Przejrzyj listę kluczy tajnych, do których uzyskano dostęp, oraz sygnaturę czasową.
1. Opcjonalnie w przypadku włączenia dzienników diagnostycznych magazynu kluczy Przejrzyj poprzednie operacje dla odpowiedniego adresu IP wywołującego, nazwy głównej użytkownika lub identyfikatora obiektu.  

## <a name="step-4-take-action"></a>Krok 4. Wykonywanie akcji 
Po skompilowaniu listy kluczy tajnych, kluczy i certyfikatów, do których uzyskano dostęp podejrzanych użytkowników lub aplikacji, należy natychmiast obrócić te obiekty.

1. Zmodyfikowane klucze tajne powinny być wyłączone lub usunięte z magazynu kluczy.
1. Jeśli poświadczenia zostały użyte dla określonej aplikacji:
    1. Skontaktuj się z administratorem aplikacji i poproś o przeprowadzenie inspekcji środowiska pod kątem wszelkich zastosowań złamanych poświadczeń, ponieważ zostały naruszone.
    1. Jeśli użyte poświadczenia zostały naruszone, właściciel aplikacji powinien zidentyfikować informacje, do których uzyskano dostęp, i wyeliminować wpływ.


## <a name="next-steps"></a>Następne kroki

Ta strona wyjaśnia proces reagowania na alert z usługi Azure Defender dla Key Vault. Aby uzyskać powiązane informacje, zobacz następujące strony:

- [Wprowadzenie do usługi Azure Defender dla Key Vault](defender-for-key-vault-introduction.md)
- [Pomijanie alertów z usługi Azure Defender](alerts-suppression-rules.md)
- [Ciągłe eksportowanie danych Security Center](continuous-export.md)