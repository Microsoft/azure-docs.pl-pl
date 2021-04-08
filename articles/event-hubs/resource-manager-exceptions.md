---
title: Event Hubs platformy Azure — Menedżer zasobów wyjątki | Microsoft Docs
description: Lista wyjątków Event Hubs platformy Azure, które zostały podane przez Azure Resource Manager i zalecane akcje.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: cec24696d0d49ba408860f6562c34dd14876c311
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91334212"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Event Hubs platformy Azure — wyjątki Menedżer zasobów
W tym artykule wymieniono wyjątki generowane podczas korzystania z usługi Azure Event Hubs przy użyciu szablonów Azure Resource Manager i bezpośrednich wywołań.

> [!IMPORTANT]
> Ten dokument jest często aktualizowany. Sprawdź aktualizacje.

W poniższych sekcjach przedstawiono różne wyjątki/błędy, które są przedstawiane za pomocą Azure Resource Manager.

## <a name="error-code-conflict"></a>Kod błędu: konflikt

| Kod błędu | Kod podkodu błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Konflikt | 40300 | Osiągnięto lub Przekroczono maksymalną liczbę zasobów typu EventHub. Rzeczywista: #, maksymalna dozwolona wartość: # | Przestrzeń nazw osiągnęła [limit przydziału](event-hubs-quotas.md) dla liczby Event Hubs, która może zawierać. | Usuń wszystkie nieużywane lub nadmiarowe centra zdarzeń z przestrzeni nazw lub Rozważ uaktualnienie do [dedykowanego klastra](event-hubs-dedicated-overview.md). |
| Konflikt | brak | Nie można usunąć konfiguracji odzyskiwania po awarii (DR), ponieważ replikacja jest w toku. Przełączenie w tryb failover lub przerwanie parowania przed próbą usunięcia konfiguracji DR. | [Replikacja GeoDR](event-hubs-geo-dr.md) jest w toku, dlatego nie można teraz usunąć konfiguracji. | Aby odblokować Usuwanie konfiguracji, poczekaj na zakończenie replikacji, wyzwól tryb failover lub Przerwij parowanie GeoDR. |
| Konflikt | brak | Aktualizacja przestrzeni nazw nie powiodła się z powodu konfliktu w zapleczu. | Inna operacja jest obecnie wykonywana w tej przestrzeni nazw. | Poczekaj na zakończenie bieżącej operacji, a następnie ponów próbę. |

## <a name="error-code-429"></a>Kod błędu: 429

| Kod błędu | Kod podkodu błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | brak | Inicjowanie obsługi przestrzeni nazw w przejściu | Inna operacja jest obecnie wykonywana w tej przestrzeni nazw. | Poczekaj na zakończenie bieżącej operacji, a następnie ponów próbę. |
| 429 | brak | Trwa operacja odzyskiwania po awarii. | Trwa operacja [GeoDR](event-hubs-geo-dr.md) na tej przestrzeni nazw lub parowania. | Poczekaj na zakończenie bieżącej operacji GeoDR, a następnie ponów próbę. |

## <a name="error-code-badrequest"></a>Kod błędu: nieprawidłowego żądania

| Kod błędu | Kod podkodu błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | Nie można zmienić PartitionCount dla centrum zdarzeń. | Warstwa Podstawowa lub Standardowa usługi Azure Event Hubs nie obsługuje zmieniania partycji. | Utwórz nowe centrum zdarzeń o odpowiedniej liczbie partycji w przestrzeni nazw warstwy Podstawowa lub standardowa. Skalowanie w poziomie partycji jest obsługiwane w przypadku [dedykowanych klastrów](event-hubs-dedicated-overview.md). |
| BadRequest | 40000 | Wartość "#" dla MessageRetentionInDays jest nieprawidłowa dla warstwy Podstawowa. wartość nie może przekroczyć następującej liczby dni: "1". | W warstwie Podstawowa Event Hubs przestrzenie nazw obsługują tylko przechowywanie komunikatów przez maksymalnie 1 dzień. | Jeśli potrzebujesz więcej niż jednego dnia przechowywania komunikatów, [Utwórz standardową przestrzeń nazw Event Hubs](event-hubs-create.md). | 
| BadRequest | brak | Podana nazwa jest niedostępna. | Nazwy przestrzeni nazw muszą być unikatowe, a określona nazwa jest już zajęta. | Jeśli jesteś właścicielem istniejącej przestrzeni nazw o określonej nazwie, możesz ją usunąć, co spowoduje utratę danych. Następnie spróbuj ponownie, używając tej samej nazwy. Jeśli przestrzeń nazw nie jest bezpieczna do usunięcia (lub jesteś właścicielem), wybierz inną nazwę przestrzeni nazw. |
| BadRequest | brak | Określona subskrypcja osiągnęła limit przydziału przestrzeni nazw. | Twoja subskrypcja osiągnęła [limit przydziału](event-hubs-quotas.md) liczby przestrzeni nazw, które mogą być przechowywane. | Rozważ usunięcie nieużywanych przestrzeni nazw w ramach tej subskrypcji, utworzenie innej subskrypcji lub uaktualnienie do [dedykowanego klastra](event-hubs-dedicated-overview.md). |
| BadRequest | brak | Nie można zaktualizować przestrzeni nazw, która jest pomocnicza | Nie można zaktualizować przestrzeni nazw, ponieważ jest to pomocnicza przestrzeń nazw w [GeoDR parowania](event-hubs-geo-dr.md). | W razie potrzeby wprowadź zmiany w podstawowej przestrzeni nazw w tym parowanie. W przeciwnym razie Przerwij parowanie GeoDR, aby wprowadzić zmiany. |
| BadRequest | brak | Nie można ustawić autodostrajania w podstawowej jednostce SKU | Nie można włączyć autodostrajania w warstwie Podstawowa Event Hubs przestrzenie nazw. | Aby [włączyć funkcję autodostrajania](event-hubs-auto-inflate.md) w przestrzeni nazw, upewnij się, że jest ona w warstwie Standardowa. |
| BadRequest | brak | Nie ma wystarczającej pojemności, aby utworzyć przestrzeń nazw. Skontaktuj się z administratorem Event Hubs. | Wybrany region jest w pojemności i nie można utworzyć więcej przestrzeni nazw. | Wybierz inny region, w którym znajduje się Twoja przestrzeń nazw. |
| BadRequest | brak | Nie można wykonać operacji dla typu jednostki "odbiorca", ponieważ w przestrzeni nazw "name" jest używana warstwa "Basic".  | Przestrzenie nazw Event Hubs warstwy Podstawowa mają przydział (Event-Hubs-quotas.md) jednej grupy konsumentów (wartość domyślna). Tworzenie większej liczby grup odbiorców nie jest obsługiwane. | Kontynuuj korzystanie z domyślnej grupy odbiorców ($Default) lub jeśli potrzebujesz więcej informacji, rozważ użycie w zamian Event Hubs przestrzeni nazw warstwy Standardowa. | 
| BadRequest | brak | Przestrzeń nazw "Namespace Name" nie istnieje. | Nie można znaleźć podanej przestrzeni nazw. | Sprawdź, czy nazwa przestrzeni nazw jest poprawna i czy można ją znaleźć w subskrypcji. Jeśli nie, [Utwórz przestrzeń nazw Event Hubs](event-hubs-create.md). | 
| BadRequest | brak | Właściwość Location zasobu nie pasuje do zawartej w nim przestrzeni nazw. | Utworzenie centrum zdarzeń w określonym regionie nie powiodło się, ponieważ nie jest on zgodny z regionem przestrzeni nazw. | Spróbuj utworzyć centrum zdarzeń w tym samym regionie, w którym znajduje się obszar nazw. | 

## <a name="error-code-internal-server-error"></a>Kod błędu: wewnętrzny błąd serwera

| Kod błędu | Kod podkodu błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Wewnętrzny błąd serwera | brak | Wewnętrzny błąd serwera. | Wystąpił błąd wewnętrzny usługi Event Hubs. | Spróbuj ponownie wykonać operację. Jeśli operacja nie powiedzie się, skontaktuj się z pomocą techniczną. |