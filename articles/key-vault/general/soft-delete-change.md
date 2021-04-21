---
title: Włączanie usuwania nie soft na wszystkich obiektach magazynu kluczy — Azure Key Vault | Microsoft Docs
description: Skorzystaj z tego dokumentu, aby wprowadzić zmiany w aplikacjach i administracji w celu uniknięcia błędów konfliktów w przypadku wszystkich magazynów kluczy.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 7e1b2ee95864affa6e5e72e1f8354767dc95bdb1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753330"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>Usuwanie nie soft -delete zostanie włączone we wszystkich magazynach kluczy

> [!WARNING]
> Zmiana przerywana: możliwość rezygnacji z usuwania nie programowego zostanie wkrótce wycofana. Azure Key Vault i administratorzy powinni natychmiast włączyć usuwanie nie soft-delete w swoich magazynach kluczy.
>
> W Azure Key Vault HSM usuwanie nie soft-delete jest domyślnie włączone i nie można go wyłączyć.

Gdy klucz tajny zostanie usunięty z magazynu kluczy bez ochrony usuwania nie soft-delete, ten klucz tajny zostanie trwale usunięty. Użytkownicy mogą obecnie zrezygnować z usuwania nie soft-delete podczas tworzenia magazynu kluczy. Jednak firma Microsoft wkrótce włączy ochronę przed nieumyślne usunięciem we wszystkich magazynach kluczy, aby chronić wpisy tajne przed przypadkowym lub złośliwym usunięciem przez użytkownika. Użytkownicy nie będą już mogli zrezygnować z usuwania nie programowego ani go wyłączyć.

:::image type="content" source="../media/softdeletediagram.png" alt-text="Diagram przedstawiający sposób usuwania magazynu kluczy z ochroną nie soft-delete w porównaniu z bez ochrony usuwania nie soft-delete.":::

Aby uzyskać szczegółowe informacje na temat funkcji usuwania nie soft-delete, [zobacz Azure Key Vault omówienie usuwania nie soft-delete](soft-delete-overview.md).

## <a name="can-my-application-work-with-soft-delete-enabled"></a>Czy moja aplikacja może działać z włączoną obsługą usuwania nie soft?

> [!Important] 
> Przed włączeniem usuwania nie soft-delete dla magazynów kluczy dokładnie zapoznaj się z poniższymi informacjami.

Nazwy magazynów kluczy są globalnie unikatowe. Nazwy wpisów tajnych przechowywanych w magazynie kluczy są również unikatowe. Nie będzie można ponownie użyć nazwy magazynu kluczy lub obiektu magazynu kluczy, która istnieje w stanie usunięcia nieu programowego. 

Jeśli na przykład aplikacja programowo utworzy magazyn kluczy o nazwie "Vault A", a później usunie magazyn A, magazyn kluczy zostanie przeniesiony do stanu usunięcia nieu programowego. Aplikacja nie będzie mogła ponownie utworzyć innego magazynu kluczy o nazwie "Vault A", dopóki magazyn kluczy nie zostanie wyczyszony ze stanu usunięcia nieu programowego. 

Ponadto jeśli aplikacja utworzy klucz o nazwie w "magazynie A", a później usunie ten klucz, aplikacja nie będzie mogła utworzyć nowego klucza o nazwie w magazynie A, dopóki obiekt nie zostanie wyczyszony ze stanu usunięcia nie `test key` `test key` `test key` soft-deleted. 

Próba usunięcia obiektu magazynu kluczy i ponownego utworzenia go o tej samej nazwie bez uprzedniego przeczyszczania go ze stanu usunięcia nie soft-deleted może powodować błędy konfliktów. Te błędy mogą spowodować niepowodzenie aplikacji lub automatyzacji. Przed podjęciem następujących wymaganych zmian w aplikacjach i administracji skontaktuj się z zespołem deweloperów. 

### <a name="application-changes"></a>Zmiany aplikacji

Jeśli aplikacja zakłada, że usuwanie nie soft-delete nie jest włączone i oczekuje, że usunięte nazwy klucza tajnego lub magazynu kluczy są dostępne do natychmiastowego ponownego użycia, należy wprowadzić następujące zmiany w logice aplikacji.

1. Usuń oryginalny magazyn kluczy lub klucz tajny.
1. Przeczyść magazyn kluczy lub klucz tajny w stanie usunięcia nieu programowego.
1. Poczekaj na ukończenie przeczyszczania. Natychmiastowe ponowne utworzenie może spowodować konflikt.
1. Utwórz ponownie magazyn kluczy o tej samej nazwie.
1. Jeśli operacja tworzenia nadal powoduje błąd konfliktu nazw, spróbuj ponownie utworzyć magazyn kluczy. Azure DNS zaktualizowanie rekordów w scenariuszu najgorszego przypadku może potrwać do 10 minut.

### <a name="administration-changes"></a>Zmiany administracji

Podmioty zabezpieczeń, które potrzebują dostępu do trwałego usuwania wpisów tajnych, muszą mieć przyznane więcej uprawnień zasad dostępu w celu przeczyszczania tych wpisów tajnych i magazynu kluczy.

Wyłącz wszystkie zasady platformy Azure w magazynach kluczy, które nakazuje wyłączenie usuwania nie soft-delete. Może być konieczne eskalowanie tego problemu do administratora, który kontroluje zasady platformy Azure stosowane do Twojego środowiska. Jeśli te zasady nie są wyłączone, możesz utracić możliwość tworzenia nowych magazynów kluczy w zakresie zastosowanych zasad.

Jeśli Twoja organizacja podlega wymaganiom prawnymi w zakresie zgodności i nie może zezwolić na to, aby usunięte magazyny kluczy i wpisy tajne pozostawały w stanie do odzyskania przez dłuższy czas, musisz dostosować okres przechowywania usuwania nie soft-delete, aby spełniał standardy organizacji. Okres przechowywania można skonfigurować tak, aby trwał od 7 do 90 dni.

## <a name="procedures"></a>Procedury

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Inspekcja magazynów kluczy w celu sprawdzenia, czy włączono usuwanie nie soft-delete

1. Zaloguj się w witrynie Azure Portal.
1. Wyszukaj **Azure Policy**.
1. Wybierz **pozycję Definicje.**
1. W **obszarze** Kategoria wybierz **Key Vault** w filtrze.
1. Wybierz pozycję **Key Vault powinna mieć włączone zasady usuwania nie programowego.**
1. Wybierz opcję **Przypisz**.
1. Ustaw zakres na subskrypcję.
1. Upewnij się, że efekt zasad został ustawiony na **Inspekcja**.
1. Wybierz pozycję **Przejrzyj i utwórz**. Pełne skanowanie środowiska może potrwać do 24 godzin.
1. W **okienku Azure Policy** wybierz pozycję **Zgodność.**
1. Wybierz zastosowane zasady.

Teraz możesz filtrować i zobaczyć, które magazyny kluczy mają włączoną funkcję usuwania nie soft-delete (zgodne zasoby), a które magazyny kluczy nie mają włączonego usuwania nie soft-delete (niezgodne zasoby).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Włączanie usuwania nie programowego dla istniejącego magazynu kluczy

1. Zaloguj się w witrynie Azure Portal.
1. Wyszukaj magazyn kluczy.
1. Wybierz **pozycję Właściwości** w obszarze **Ustawienia.**
1. W **obszarze Soft-Delete**(Usuwanie nie soft-delete) wybierz **opcję Enable recovery of this vault and its objects (Włącz** odzyskiwanie tego magazynu i jego obiektów).
1. Ustaw okres przechowywania dla usuwania nie programowego.
1. Wybierz pozycję **Zapisz**.

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Przyznawanie uprawnień zasad dostępu do przeczyszczania dla podmiotu zabezpieczeń

1. Zaloguj się w witrynie Azure Portal.
1. Wyszukaj magazyn kluczy.
1. Wybierz **pozycję Zasady dostępu** w obszarze **Ustawienia.**
1. Wybierz jednostkę usługi, do których chcesz udzielić dostępu.
1. Przejdź przez poszczególne menu rozwijane w obszarze **Uprawnienia klucza,** **klucza tajnego** i **certyfikatu,** aż zobaczysz pozycję **Operacje uprzywilejowane.** Wybierz **uprawnienie Przeczyść.**

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="does-this-change-affect-me"></a>Czy ta zmiana ma na mnie wpływ?

Jeśli masz już włączone usuwanie nie soft-delete lub jeśli nie usuniesz i nie utworzysz ponownie obiektów magazynu kluczy o tej samej nazwie, prawdopodobnie nie zauważysz żadnych zmian w zachowaniu magazynu kluczy.

Jeśli masz aplikację, która często usuwa i ponownie tworzy obiekty magazynu kluczy z taką samą konwencją nazewnictwa, musisz wprowadzić zmiany w logice aplikacji, aby zachować oczekiwane zachowanie. Zobacz [sekcję Zmiany aplikacji](#application-changes) w tym artykule.

### <a name="how-do-i-benefit-from-this-change"></a>Jak mogę korzyści z tej zmiany?

Ochrona przed usuwaniem nie soft-delete zapewnia organizacji kolejną warstwę ochrony przed przypadkowym lub złośliwym usunięciem. Jako administrator magazynu kluczy możesz ograniczyć dostęp do uprawnień odzyskiwania i przeczyszczania.

Jeśli użytkownik przypadkowo usunie magazyn kluczy lub klucz tajny, możesz udzielić im uprawnień dostępu do odzyskiwania klucza tajnego bez tworzenia ryzyka trwałego usunięcia klucza tajnego lub magazynu kluczy. Ten samoobsługowy proces minimalizuje przestoje w środowisku i gwarantuje dostępność wpisów tajnych.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Jak mogę się, czy muszę podjąć działania?

Wykonaj kroki opisane w sekcji Audit your key vaults (Inspekcja [magazynów kluczy),](#audit-your-key-vaults-to-check-if-soft-delete-is-enabled) aby sprawdzić, czy usuwanie nie softowe jest włączone w tym artykule. Ta zmiana będzie mieć wpływ na każdy magazyn kluczy, który nie ma włączonego usuwania nie soft-delete.

### <a name="what-action-do-i-need-to-take"></a>Jaką akcję muszę wykonać?

Po potwierdzeniu, że nie trzeba wprowadzać zmian w logice aplikacji, włącz usuwanie nie soft-delete we wszystkich magazynach kluczy.

### <a name="when-do-i-need-to-take-action"></a>Kiedy należy podjąć działania?

Aby upewnić się, że nie ma to wpływu na aplikacje, włącz usuwanie nie soft-delete w magazynach kluczy tak szybko, jak to możliwe.

## <a name="next-steps"></a>Następne kroki

- Jeśli masz pytania dotyczące tej zmiany, skontaktuj się z nami pod adres [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) .
- Zapoznaj się z [omówieniem usuwania nie softowego.](soft-delete-overview.md)
