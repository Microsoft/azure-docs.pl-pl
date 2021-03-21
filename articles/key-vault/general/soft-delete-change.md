---
title: Włącz funkcję usuwania nietrwałego we wszystkich obiektach magazynu kluczy — Azure Key Vault | Microsoft Docs
description: Ten dokument służy do przyjmowania nietrwałego usuwania wszystkich magazynów kluczy i wprowadzania zmian w aplikacjach i administracji w celu uniknięcia błędów konfliktów.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: sudbalas
ms.openlocfilehash: b96f2ca4f925846bd252e5cfd35088d832f5c216
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98572871"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>Usuwanie nietrwałe zostanie włączone dla wszystkich magazynów kluczy

> [!WARNING]
> Zmiana podziału: możliwość rezygnacji z usuwania nietrwałego zostanie wkrótce wycofana. Azure Key Vault Użytkownicy i Administratorzy powinni natychmiast włączyć funkcję trwałego usuwania dla swoich magazynów kluczy.
>
> W przypadku Azure Key Vault zarządzanego modułu HSM funkcja usuwania nietrwałego jest domyślnie włączona i nie można jej wyłączyć.

Po usunięciu wpisu tajnego z magazynu kluczy bez ochrony przed usunięciem nietrwałego wpis tajny zostanie trwale usunięty. Użytkownicy mogą obecnie zrezygnować z usuwania nietrwałego podczas tworzenia magazynu kluczy. Jednak firma Microsoft wkrótce włączy ochronę nietrwałego usuwania wszystkich magazynów kluczy w celu ochrony kluczy tajnych przed przypadkowym lub złośliwym usunięciem przez użytkownika. Użytkownicy nie będą już mogli zrezygnować z ani wyłączyć usuwania nietrwałego.

:::image type="content" source="../media/softdeletediagram.png" alt-text="Diagram przedstawiający sposób usuwania magazynu kluczy przy użyciu ochrony usuwania nietrwałego i bez usuwania nietrwałego.":::

Aby uzyskać szczegółowe informacje na temat funkcji usuwania nietrwałego, zobacz [Azure Key Vault narzędzia do usuwania nietrwałego](soft-delete-overview.md).

## <a name="can-my-application-work-with-soft-delete-enabled"></a>Czy moja aplikacja może współpracować z włączonym niemiękkim usuwaniem?

> [!Important] 
> Przed włączeniem usuwania nietrwałego dla magazynów kluczy należy uważnie zapoznać się z poniższymi informacjami.

Nazwy magazynów kluczy są unikatowe globalnie. Nazwy wpisów tajnych przechowywane w magazynie kluczy są również unikatowe. Nie będzie można ponownie użyć nazwy magazynu kluczy lub obiektu magazynu kluczy, który istnieje w stanie nieusunięty. 

Na przykład jeśli aplikacja programowo tworzy magazyn kluczy o nazwie "magazyn A" i później usunie "magazyn A", Magazyn kluczy zostanie przeniesiony do stanu nieusuniętego. Aplikacja nie będzie mogła utworzyć ponownie nowego magazynu kluczy o nazwie "magazyn A", dopóki nie zostanie usunięty Magazyn kluczy ze stanu nietrwałego. 

Ponadto, jeśli aplikacja tworzy klucz o nazwie `test key` w "magazynie a", a następnie usuwa ten klucz, aplikacja nie będzie mogła utworzyć nowego klucza o nazwie `test key` "magazyn a" do momentu, gdy `test key` obiekt zostanie usunięty ze stanu nietrwałego. 

Podjęto próbę usunięcia obiektu magazynu kluczy i ponownego utworzenia go o takiej samej nazwie, bez przeczyszczania go w stanie nieusuniętym, może spowodować błędy konfliktów. Te błędy mogą spowodować niepowodzenie aplikacji lub automatyzację. Zapoznaj się z zespołem deweloperskim, zanim wprowadzisz następujące wymagane zmiany dotyczące aplikacji i administrowania. 

### <a name="application-changes"></a>Zmiany aplikacji

Jeśli aplikacja założono, że nietrwałe usuwanie nie jest włączone i oczekuje, że usunięte klucze tajne lub nazwy magazynu kluczy są dostępne do natychmiastowego ponownego użycia, należy wprowadzić następujące zmiany w logice aplikacji.

1. Usuń oryginalny Magazyn kluczy lub klucz tajny.
1. Przeczyść Magazyn kluczy lub klucz tajny w stanie nieusuniętym.
1. Poczekaj na zakończenie przeczyszczania. Natychmiastowe ponowne utworzenie może spowodować konflikt.
1. Utwórz ponownie Magazyn kluczy o tej samej nazwie.
1. Jeśli operacja tworzenia nadal powoduje błąd konfliktu nazw, spróbuj ponownie utworzyć magazyn kluczy. Aktualizacja Azure DNS może potrwać do 10 minut w scenariuszu najgorszego przypadku.

### <a name="administration-changes"></a>Zmiany administracyjne

Podmioty zabezpieczeń, które muszą mieć dostęp do trwałego usuwania wpisów tajnych, muszą mieć przyznane więcej uprawnień dostępu, aby przeczyścić te wpisy tajne i Magazyn kluczy.

Wyłącz wszystkie zasady platformy Azure w magazynach kluczy, które są niezbędne do wyłączenia usuwania nietrwałego. Może być konieczne przekazanie tego problemu administratorowi kontrolującemu zasady platformy Azure stosowane do danego środowiska. Jeśli ta zasada nie zostanie wyłączona, można utracić możliwość tworzenia nowych magazynów kluczy w zakresie stosowanych zasad.

Jeśli organizacja podlega wymaganiom dotyczącym zgodności i nie może zezwolić na odzyskanie usuniętych magazynów kluczy i kluczy tajnych przez dłuższy czas, należy dostosować okres przechowywania nietrwałego usuwania, aby spełniał standardy organizacji. Okres przechowywania można skonfigurować jako ostatni z 7 do 90 dni.

## <a name="procedures"></a>Procedury

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Przeprowadź inspekcję magazynów kluczy, aby sprawdzić, czy jest włączona funkcja usuwania nietrwałego

1. Zaloguj się w witrynie Azure Portal.
1. Wyszukaj **Azure Policy**.
1. Wybierz pozycję **definicje**.
1. W obszarze **Kategoria** wybierz pozycję **Key Vault** w filtrze.
1. Wybierz **Key Vault powinny mieć zasady z włączonym usuwaniem nietrwałego** .
1. Wybierz opcję **Przypisz**.
1. Ustaw zakres na subskrypcję.
1. Upewnij się, że wynik zasad jest ustawiony na **inspekcję**.
1. Wybierz pozycję **Przejrzyj i utwórz**. Pełne skanowanie środowiska może potrwać do 24 godzin.
1. W okienku **Azure Policy** wybierz pozycję **zgodność**.
1. Wybierz zastosowane zasady.

Teraz można filtrować i dowiedzieć się, które magazyny kluczy mają włączony nietrwałe usuwanie (zgodne zasoby) i które magazyny kluczy nie mają włączonego usuwania niezgodnego (niezgodnych zasobów).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Włączanie usuwania nietrwałego dla istniejącego magazynu kluczy

1. Zaloguj się w witrynie Azure Portal.
1. Wyszukaj swój magazyn kluczy.
1. W obszarze **Ustawienia** wybierz pozycję **Właściwości** .
1. W obszarze **usuwanie nietrwałe** zaznacz opcję **Włącz odzyskiwanie tego magazynu i jego obiektów** .
1. Ustaw okres przechowywania dla usuwania nietrwałego.
1. Wybierz pozycję **Zapisz**.

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Przyznawanie uprawnień do przeczyszczania zasad dostępu do podmiotu zabezpieczeń

1. Zaloguj się w witrynie Azure Portal.
1. Wyszukaj swój magazyn kluczy.
1. Wybierz pozycję **zasady dostępu** w obszarze **Ustawienia**.
1. Wybierz jednostkę usługi, do której chcesz udzielić dostępu.
1. Poruszaj się po każdym menu rozwijanym w obszarze **uprawnienia** **Key**, **Secret** i Certificate, dopóki nie zobaczysz **uprzywilejowanych operacji**. Wybierz uprawnienie **Przeczyść** .

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="does-this-change-affect-me"></a>Czy ta zmiana wpłynie na mnie?

Jeśli jest już włączona funkcja usuwania nietrwałego lub jeśli nie usuniesz i ponownie utworzysz obiektów magazynu kluczy o tej samej nazwie, nie będzie można zauważyć żadnych zmian w zachowaniu magazynu kluczy.

Jeśli masz aplikację, która powoduje usunięcie i ponowne utworzenie obiektów magazynu kluczy z tymi samymi konwencjami nazewnictwa, musisz wprowadzić zmiany w logice aplikacji, aby zachować oczekiwane zachowanie. Zapoznaj się z sekcją [zmiany aplikacji](#application-changes) w tym artykule.

### <a name="how-do-i-benefit-from-this-change"></a>Jak mogę korzyść z tej zmiany?

Ochrona usuwania nietrwałego zapewnia organizacji inną warstwę ochrony przed przypadkowym lub złośliwym usunięciem. Jako administrator magazynu kluczy możesz ograniczyć dostęp do uprawnień do odzyskiwania i przeczyszczania uprawnień.

Jeśli użytkownik przypadkowo usunie Magazyn kluczy lub klucz tajny, można przyznać im uprawnienia dostępu w celu odzyskania samego klucza tajnego bez tworzenia ryzyka trwałego usunięcia wpisu tajnego lub magazynu kluczy. Ten proces samoobsługowy minimalizuje przestoje w środowisku i gwarantuje dostępność wpisów tajnych.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Jak mogę sprawdzić, czy muszę podjąć działanie?

Postępuj zgodnie z instrukcjami w obszarze [Inspekcja Twoich magazynów kluczy, aby sprawdzić, czy w tym artykule jest włączona funkcja usuwania nietrwałego](#audit-your-key-vaults-to-check-if-soft-delete-is-enabled) . Ta zmiana wpłynie na dowolny Magazyn kluczy, w którym nie włączono usuwania nietrwałego.

### <a name="what-action-do-i-need-to-take"></a>Jaką czynność należy wykonać?

Po potwierdzeniu, że nie musisz wprowadzać zmian w logice aplikacji, Włącz nietrwałe usunięcie wszystkich magazynów kluczy.

### <a name="when-do-i-need-to-take-action"></a>Kiedy muszę podejmować działania?

Aby upewnić się, że Twoje aplikacje nie mają wpływu, Włącz nietrwałe usuwanie w magazynach kluczy, jak najszybciej.

## <a name="next-steps"></a>Następne kroki

- Skontaktuj się z nami, podając pytania dotyczące tej zmiany w programie [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) .
- Przeczytaj [Omówienie usuwania nietrwałego](soft-delete-overview.md).
