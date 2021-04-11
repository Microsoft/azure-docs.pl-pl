---
title: Rezygnacja z zweryfikowanych poświadczeń (wersja zapoznawcza)
description: Dowiedz się, jak zrezygnować z zweryfikowanych poświadczeń w wersji zapoznawczej
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 93c493a9b9941913da270fe763175240c8abf9f3
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170213"
---
# <a name="opt-out-of-the-verifiable-credentials-preview"></a>Rezygnacja z zweryfikowanych poświadczeń (wersja zapoznawcza)

W tym artykule:

- Powód, dla którego może zajść konieczność rezygnacji.
- Wymagane kroki.
- Co się dzieje z danymi?
- Wpływ na istniejące poświadczenia podlegające weryfikacji.

> [!IMPORTANT]
> Azure Active Directory poświadczenia do zweryfikowania są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

- Ukończ proces dołączania do zweryfikowanych poświadczeń.

## <a name="potential-reasons-for-opting-out"></a>Potencjalne przyczyny rezygnacji

W tej chwili nie mamy możliwości wprowadzania modyfikacji informacji o domenie. W związku z tym, jeśli popełnisz błąd lub zdecydujesz, że chcesz wprowadzić zmianę, nie jest dostępna żadna inna opcja poza wyewidencjonowaniem i ponownym uruchomieniem.

## <a name="the-steps-required"></a>Wymagane kroki

1. W Azure Portal Wyszukaj poświadczenia do zweryfikowania.
2. Wybierz pozycję **Ustawienia** w menu po lewej stronie.
3. W sekcji **zresetuj swoją organizację**, wybierz pozycję **Usuń wszystkie poświadczenia i zrezygnuj z wersji zapoznawczej**.

   ![Resetowanie ustawień organizacji](media/how-to-opt-out/settings-reset.png)

4. Przeczytaj komunikat ostrzegawczy i wybierz pozycję **Usuń,** aby kontynuować.

   ![Usuń i zrezygnuj z ustawień](media/how-to-opt-out/delete-and-opt-out.png)

W wersji zapoznawczej poświadczeń z zweryfikowaniem. Zapoznaj się z informacjami na temat tego, co dzieje się na wyciągu.

## <a name="what-happens-to-your-data"></a>Co się dzieje z danymi?

Po zakończeniu rezygnacji z Azure Active Directory usługi poświadczeń do zweryfikowania zostaną wykonane następujące akcje:

- Klucze DID w Key Vault zostaną [usunięte nietrwałe](../../key-vault/general/soft-delete-overview.md).
- Obiekt Issuer zostanie usunięty z naszej bazy danych.
- Dzierżawa identyfikator zostanie usunięta z naszej bazy danych. 
- Wszystkie obiekty umów zostaną usunięte z naszej bazy danych.

Gdy nastąpi rezygnacja z programu, nie będzie można odzyskać ani wykonać żadnych operacji. Ten krok jest operacją jednokierunkową i konieczne jest ponowne zainicjowanie, co spowoduje utworzenie nowej.  

## <a name="effect-on-existing-verifiable-credentials"></a>Wpływ na istniejące poświadczenia podlegające weryfikacji.

Wszystkie zweryfikowane poświadczenia, które już wydano, będą nadal istnieć. Nie zostaną one kryptograficznie unieważnione, ponieważ pozostaną one rozpoznawalne przez jonu.
Jednak gdy jednostki uzależnione wywołują interfejs API stanu, zawsze będą otrzymywać komunikat o błędzie.

## <a name="next-steps"></a>Następne kroki

- Konfigurowanie zweryfikowanych poświadczeń w [dzierżawie platformy Azure](get-started-verifiable-credentials.md)