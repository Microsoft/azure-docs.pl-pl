---
title: Włącz usuwanie nietrwałe dla wszystkich magazynów kluczy Azure | Microsoft Docs
description: Ten dokument służy do przyjmowania nietrwałego usuwania dla wszystkich magazynów kluczy.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: sudbalas
ms.openlocfilehash: e512cccdbfdc56500fa7c69372ca38f59d3195c2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590090"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>Usuwanie nietrwałe zostanie włączone dla wszystkich magazynów kluczy

> [!WARNING]
> **Zmiana podziału**: możliwość rezygnacji z usuwania nietrwałego zostanie wkrótce wycofana. Azure Key Vault Użytkownicy i Administratorzy powinni natychmiast włączyć funkcję trwałego usuwania dla swoich magazynów kluczy.
>
> W przypadku zarządzanego modułu HSM funkcja usuwania nietrwałego jest domyślnie włączona i nie można jej wyłączyć.

Po usunięciu wpisu tajnego z magazynu kluczy bez ochrony przed usunięciem nietrwałego wpis tajny zostanie trwale usunięty. Użytkownicy mogą obecnie zrezygnować z usuwania nietrwałego podczas tworzenia magazynu kluczy, ale aby chronić wpisy tajne przed przypadkowym lub złośliwym usunięciem przez użytkownika, firma Microsoft wkrótce włączy ochronę nietrwałego usuwania **wszystkich** magazynów kluczy, a użytkownicy nie będą już mogli zrezygnować ani wyłączyć usuwania nietrwałego.

:::image type="content" source="../media/softdeletediagram.png" alt-text="<tekst alternatywny>":::

Aby uzyskać szczegółowe informacje na temat funkcji usuwania nietrwałego, zobacz [Azure Key Vault narzędzia do usuwania nietrwałego](soft-delete-overview.md).

## <a name="can-my-application-work-with-soft-delete-enabled"></a>Czy moja aplikacja może współpracować z włączonym niemiękkim usuwaniem?

> [!Important] 
> **Przed włączeniem usuwania nietrwałego dla Twoich magazynów kluczy należy uważnie zapoznać się z poniższymi informacjami.**

Nazwy Key Vault są unikatowe globalnie. Nazwy wpisów tajnych przechowywane w magazynie kluczy są również unikatowe. Nie będzie można ponownie użyć nazwy magazynu kluczy lub obiektu magazynu kluczy, który istnieje w stanie usunięte nietrwałe. 

**Przykład #1** Jeśli aplikacja programowo tworzy magazyn kluczy o nazwie "magazyn A", a następnie usuwa element "magazyn A". Magazyn kluczy zostanie przeniesiony do stanu nietrwałego usunięcia. Aplikacja nie będzie mogła utworzyć ponownie nowego magazynu kluczy o nazwie "magazyn A", dopóki Magazyn kluczy nie zostanie usunięty z nietrwałego stanu. 

**Przykład #2** Jeśli aplikacja tworzy klucz o nazwie `test key` w magazynie kluczy a, a następnie usuwa klucz z magazynu a, aplikacja nie będzie mogła utworzyć nowego klucza o nazwie `test key` w magazynie kluczy a do momentu, gdy `test key` obiekt zostanie usunięty ze stanu nietrwałego. 

Może to spowodować błędy konfliktów, jeśli spróbujesz usunąć obiekt magazynu kluczy i utworzyć go ponownie o tej samej nazwie bez wcześniejszego usunięcia go z stanu nietrwałego. Może to spowodować niepowodzenie aplikacji lub automatyzacji. Zapoznaj się z zespołem deweloperskim przed wprowadzeniem wymaganych zmian w aplikacji i administracji poniżej. 

### <a name="application-changes"></a>Zmiany aplikacji

Jeśli aplikacja założono, że nietrwałe usuwanie nie jest włączone i oczekuje, że usunięte klucze tajne lub nazwy magazynu kluczy są dostępne do ponownego użycia, logika aplikacji musi wprowadzić następujące zmiany, aby zastosować tę zmianę.

1. Usuń oryginalny Magazyn kluczy lub klucz tajny
2. Przeczyść Magazyn kluczy lub klucz tajny w stanie nieusuniętym.
3. Czekaj — natychmiastowe ponowne utworzenie może spowodować konflikt.
4. Utwórz ponownie Magazyn kluczy o tej samej nazwie.
5. Zaimplementuj ponowną próbę, jeśli operacja tworzenia nadal powoduje błąd konfliktu nazw. aktualizacja rekordów DNS w scenariuszu najgorszego przypadku może potrwać do 10 minut.

### <a name="administration-changes"></a>Zmiany administracyjne

Podmioty zabezpieczeń, które muszą mieć dostęp do trwale usuniętych wpisów tajnych, muszą mieć przyznane dodatkowe uprawnienia dostępu do przeczyszczania tych kluczy tajnych i magazynu kluczy.

Jeśli masz Azure Policy w magazynach kluczy, które są niezbędne do wyłączenia usuwania nietrwałego, te zasady będą musiały zostać wyłączone.  Może być konieczne eskalacja tego problemu administratorowi kontrolującemu zasady platformy Azure stosowane do danego środowiska. Jeśli ta zasada nie zostanie wyłączona, można utracić możliwość tworzenia nowych magazynów kluczy w zakresie stosowanych zasad.

Jeśli organizacja podlega wymaganiom dotyczącym zgodności i nie można zezwolić, aby usunięte magazyny kluczy i wpisy tajne pozostawały w stanie odzyskiwalnym przez dłuższy czas, konieczne będzie dostosowanie okresu przechowywania nietrwałego usuwania, który można skonfigurować od 7 do 90 dni, aby spełnić standardy organizacji.

## <a name="procedures"></a>Procedury

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Przeprowadź inspekcję magazynów kluczy, aby sprawdzić, czy jest włączona funkcja usuwania nietrwałego

1. Zaloguj się w witrynie Azure Portal.
2. Wyszukaj ciąg "Azure Policy".
3. Wybierz pozycję "Definicje".
4. W obszarze Kategoria wybierz pozycję "Key Vault" w filtrze.
5. Wybierz zasady "Key Vault powinna mieć włączone trwałe usuwanie".
6. Kliknij przycisk "Przypisz".
7. Ustaw zakres na subskrypcję.
8. Upewnij się, że wynik zasad jest ustawiony na wartość "inspekcja".
9. Wybierz pozycję "Przejrzyj + Utwórz".
10. Wykonanie pełnego skanowania środowiska w programie może potrwać do 24 godzin.
11. W bloku Azure Policy kliknij pozycję "zgodność".
12. Wybierz zastosowane zasady.

Teraz powinno być możliwe filtrowanie i przeglądanie, które magazyny kluczy mają włączone usuwanie trwałe (zgodne zasoby) i które magazyny kluczy nie mają włączonego usuwania niezgodnego.

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Włącz usuwanie nietrwałe dla istniejącego magazynu kluczy

1. Zaloguj się w witrynie Azure Portal.
2. Wyszukaj Key Vault.
3. W obszarze Ustawienia wybierz pozycję "właściwości".
4. W obszarze usuwanie nietrwałe wybierz przycisk radiowy odpowiadający opcji "Włącz odzyskiwanie tego magazynu i jego obiektów".
5. Ustaw okres przechowywania dla usuwania nietrwałego.
6. Wybierz pozycję "Zapisz".

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Przyznawanie uprawnień do przeczyszczania zasad dostępu do podmiotu zabezpieczeń

1. Zaloguj się w witrynie Azure Portal.
2. Wyszukaj Key Vault.
3. W obszarze Ustawienia wybierz pozycję "zasady dostępu".
4. Wybierz jednostkę usługi, do której chcesz udzielić dostępu.
5. Dla każdej listy rozwijanej w obszarze uprawnienia klucza, klucza tajnego i certyfikatu przewiń w dół do pozycji "operacje uprzywilejowane" i wybierz uprawnienie "przeczyszczanie".

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="does-this-change-affect-me"></a>Czy ta zmiana wpłynie na mnie?

Jeśli jest już włączona funkcja usuwania nietrwałego lub jeśli nie usuniesz i nie utworzysz obiektów magazynu kluczy o tej samej nazwie, prawdopodobnie nie zobaczysz żadnych zmian w zachowaniu magazynu kluczy.

Jeśli masz aplikację, która powoduje usunięcie i ponowne utworzenie obiektów magazynu kluczy z tymi samymi konwencjami nazewnictwa, musisz wprowadzić zmiany w logice aplikacji, aby zachować oczekiwane zachowanie. Zapoznaj się z tematem "Jak mogę odpowiedzieć na zmiany w przypadku zmian?". powyższej sekcji.

### <a name="how-do-i-benefit-from-this-change"></a>Jak mogę korzyść z tej zmiany?

Ochrona usuwania nietrwałego zapewnia organizacji dodatkową warstwę ochrony przed przypadkowym lub złośliwym usunięciem. Jako administrator magazynu kluczy możesz ograniczyć dostęp do uprawnień do odzyskiwania i przeczyszczania uprawnień.

Jeśli użytkownik przypadkowo usunie Magazyn kluczy lub klucz tajny, można przyznać im uprawnienia dostępu w celu odzyskania samego klucza tajnego bez tworzenia ryzyka trwałego usunięcia wpisu tajnego lub magazynu kluczy. Ten proces samoobsługowy zmniejsza czas w środowisku i gwarantuje dostępność kluczy tajnych.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Jak mogę sprawdzić, czy muszę podjąć działanie?

Wykonaj kroki opisane powyżej w sekcji zatytułowanej "procedura przeprowadzania inspekcji magazynów kluczy, aby sprawdzić, czy Soft-Delete jest włączone". Ta zmiana wpłynie na dowolny Magazyn kluczy, w którym nie włączono usuwania nietrwałego. Dodatkowe narzędzia do inspekcji będą dostępne wkrótce, a ten dokument zostanie zaktualizowany.

### <a name="what-action-do-i-need-to-take"></a>Jaką czynność należy wykonać?

Upewnij się, że nie musisz wprowadzać zmian w logice aplikacji. Po potwierdzeniu tego należy włączyć opcję usuwania nietrwałego dla wszystkich magazynów kluczy.

### <a name="by-when-do-i-need-to-take-action"></a>Kiedy muszę podejmować działania?

Aby upewnić się, że Twoje aplikacje nie mają zastosowania, Włącz nietrwałe usuwanie w magazynach kluczy, jak najszybciej.

## <a name="next-steps"></a>Następne kroki

- Skontaktuj się z nami, podając wszelkie pytania dotyczące tej zmiany w programie [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) .
- Przeczytaj [Omówienie usuwania nietrwałego](soft-delete-overview.md)
