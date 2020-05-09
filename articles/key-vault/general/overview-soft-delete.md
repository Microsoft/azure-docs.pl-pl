---
title: Azure Key Vault usuwania nietrwałego | Microsoft Docs
description: Funkcja usuwania nietrwałego w Azure Key Vault umożliwia odzyskanie usuniętych magazynów kluczy i obiektów magazynu kluczy, takich jak klucze, wpisy tajne i certyfikaty.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: 347f8a4cf1fb95849bcf1008e91d17878f3d01f8
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598525"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Azure Key Vault — omówienie usuwania nietrwałego

Funkcja usuwania nietrwałego Key Vault umożliwia Odzyskiwanie usuniętych magazynów i obiektów magazynu, zwanych nietrwałego usuwania. W tym celu należy zająć się następującymi scenariuszami:

- Obsługa odwracalnego usuwania magazynu kluczy
- Obsługa odwracalnego usuwania obiektów magazynu kluczy (np. klucze, wpisy tajne, certyfikaty)

## <a name="supporting-interfaces"></a>Interfejsy pomocnicze

Funkcja usuwania nietrwałego jest początkowo dostępna za pomocą interfejsów [rest](/rest/api/keyvault/), [CLI](soft-delete-cli.md), [PowerShell](soft-delete-powershell.md)i [.NET/C#](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet) , a także [szablonów ARM](https://docs.microsoft.com/azure/templates/microsoft.keyvault/2019-09-01/vaults).

## <a name="scenarios"></a>Scenariusze

Magazyny kluczy Azure to śledzone zasoby zarządzane przez Azure Resource Manager. Azure Resource Manager określa również dobrze zdefiniowane zachowanie do usunięcia, które wymaga pomyślnego usunięcia operacji, musi spowodować, że zasób nie jest już dostępny. Funkcja usuwania nietrwałego odnosi się do odzyskania usuniętego obiektu, bez względu na to, czy usunięcie było przypadkowe czy celowe.

1. W typowym scenariuszu użytkownik mógł przypadkowo usunąć magazyn kluczy lub obiekt magazynu kluczy; Jeśli ten magazyn kluczy lub obiekt magazynu kluczy był możliwy do odzyskania w ustalonym okresie, użytkownik może cofnąć operację usuwania i odzyskać swoje dane.

2. W innym scenariuszu nieautoryzowany użytkownik może próbować usunąć magazyn kluczy lub obiekt magazynu kluczy, taki jak klucz w magazynie, aby spowodować zakłócenia działania firmy. Oddzielenie usunięcia magazynu kluczy lub obiektu magazynu kluczy od rzeczywistego usunięcia danych źródłowych może służyć jako środek bezpieczeństwa przez, na przykład, ograniczenie uprawnień do usuwania danych do innej zaufanej roli. Takie podejście efektywnie wymaga kworum dla operacji, co może spowodować natychmiastowe utratę danych.

### <a name="soft-delete-behavior"></a>Zachowanie nietrwałego usuwania

Po włączeniu usuwania nietrwałego zasoby oznaczone jako usunięte zasoby są zachowywane przez określony czas (domyślnie 90 dni). Usługa zapewnia Dodatkowo mechanizm odzyskiwania usuniętego obiektu, zasadniczo cofając operację usuwania.

Podczas tworzenia nowego magazynu kluczy, usuwanie nietrwałe jest domyślnie włączone. Możesz utworzyć magazyn kluczy bez usuwania nietrwałego za pomocą [interfejsu wiersza polecenia platformy Azure](soft-delete-cli.md) lub [Azure PowerShell](soft-delete-powershell.md). Po włączeniu usuwania nietrwałego w magazynie kluczy nie można go wyłączyć

Domyślny okres przechowywania to 90 dni, ale podczas tworzenia magazynu kluczy można ustawić interwał zasad przechowywania na wartość z przedziału od 7 do 90 dni za pośrednictwem Azure Portal. Zasady przechowywania ochrony przed przeczyszczeniem używają tego samego interwału. Po ustawieniu nie można zmienić interwału zasad przechowywania.

Nie można ponownie użyć nazwy magazynu kluczy, który został usunięty jako nietrwały, dopóki nie upłynie okres przechowywania.

### <a name="purge-protection"></a>Ochrona przeczyszczania 

Ochrona przed czyszczeniem jest opcjonalnym zachowaniem Key Vault i **nie jest włączona domyślnie**. Ochronę przeczyszczania można włączyć tylko po włączeniu usuwania nietrwałego.  Można ją włączyć za pomocą [interfejsu wiersza polecenia](soft-delete-cli.md#enabling-purge-protection) lub [programu PowerShell](soft-delete-powershell.md#enabling-purge-protection).

Gdy ochrona przed przeczyszczeniem jest włączona, nie można wyczyścić magazynu ani obiektu w stanie usuniętym, dopóki nie upłynie okres przechowywania. Nieusunięte magazyny i obiekty nadal mogą być odzyskiwane, co oznacza, że zostaną zastosowane zasady przechowywania. 

Domyślny okres przechowywania to 90 dni, ale można ustawić interwał zasad przechowywania na wartość z przedziału od 7 do 90 dni za pośrednictwem Azure Portal. Po ustawieniu interwału zasad przechowywania i zapisaniu go nie można go zmienić w tym magazynie. 

### <a name="permitted-purge"></a>Dozwolone przeczyszczanie

Trwałe usuwanie, przeczyszczanie magazynu kluczy jest możliwe za pośrednictwem operacji POST na zasobie serwera proxy i wymaga specjalnych uprawnień. Ogólnie rzecz biorąc, tylko właściciel subskrypcji będzie mógł przeczyścić Magazyn kluczy. Operacja POST wyzwala natychmiastowe i nieodwracalne usuwanie tego magazynu. 

Wyjątki są następujące:
- Gdy subskrypcja platformy Azure została oznaczona jako *undeletable*. W takim przypadku tylko usługa może wykonać rzeczywiste usunięcie i tak jak w przypadku zaplanowanego procesu. 
- Gdy `--enable-purge-protection flag` jest włączona w magazynie, W takim przypadku Key Vault będzie czekać przez 90 dni od momentu, gdy oryginalny obiekt tajny został oznaczony do usunięcia, aby usunąć go trwale.

### <a name="key-vault-recovery"></a>Odzyskiwanie magazynu kluczy

Po usunięciu magazynu kluczy Usługa tworzy zasób serwera proxy w ramach subskrypcji, dodając wystarczające metadane do odzyskania. Zasób serwera proxy jest przechowywanym obiektem, który jest dostępny w tej samej lokalizacji co usunięty Magazyn kluczy. 

### <a name="key-vault-object-recovery"></a>Odzyskiwanie obiektu magazynu kluczy

Po usunięciu obiektu magazynu kluczy, takiego jak klucz, usługa umieści obiekt w stanie usunięte, co uniemożliwi dostęp do żadnych operacji pobierania. W tym stanie obiekt magazynu kluczy może być wyświetlany na liście, odzyskany lub wymuszony/trwale usunięty. 

W tym samym czasie Key Vault Zaplanuj usunięcie danych źródłowych odpowiadających usuniętemu magazynowi kluczy lub obiektowi magazynu kluczy do wykonania po upływie wstępnie określonego interwału przechowywania. Rekord DNS odpowiadający magazynowi również jest zachowywany na czas trwania interwału przechowywania.

### <a name="soft-delete-retention-period"></a>Okres przechowywania nietrwałego usuwania

Zasoby usunięte przez program są przechowywane przez określony czas, 90 dni. Podczas okresu przechowywania nietrwałego usuwania należy zastosować następujące czynności:

- Możesz wyświetlić listę wszystkich magazynów kluczy i obiektów magazynu kluczy w stanie nietrwałego usuwania dla Twojej subskrypcji, a także uzyskać dostęp do informacji dotyczących ich usuwania i odzyskiwania.
    - Tylko użytkownicy z uprawnieniami specjalnymi mogą wyświetlać usunięte magazyny. Firma Microsoft zaleca, aby naszym użytkownikom utworzyć rolę niestandardową z tymi specjalnymi uprawnieniami do obsługi usuniętych magazynów.
- Nie można utworzyć magazynu kluczy o tej samej nazwie w tej samej lokalizacji; nie można utworzyć obiektu magazynu kluczy w danym magazynie, jeśli Magazyn kluczy zawiera obiekt o tej samej nazwie i który jest w stanie usunięty. 
- Tylko specjalny użytkownik uprzywilejowany może przywrócić magazyn kluczy lub obiekt magazynu kluczy, wydając polecenie Recover na odpowiednim zasobie serwera proxy.
    - Użytkownik, członek roli niestandardowej, który ma uprawnienia do tworzenia magazynu kluczy w ramach grupy zasobów, może przywrócić magazyn.
- Tylko użytkownik z uprawnieniami specjalnymi może wymusić usunięcie magazynu kluczy lub obiektu magazynu kluczy, wydając polecenie usunięcia na odpowiednim zasobie serwera proxy.

Jeśli magazyn kluczy lub obiekt magazynu kluczy nie zostanie odzyskany, na końcu interwału przechowywania usługa wykonuje przeczyszczanie usuniętego nietrwałego magazynu kluczy lub obiektu magazynu kluczy i jego zawartości. Nie można ponownie zaplanować usunięcia zasobu.

### <a name="billing-implications"></a>Implikacje rozliczeń

Ogólnie rzecz biorąc, gdy obiekt (Magazyn kluczy lub klucz lub wpis tajny) jest w stanie usunięte, dostępne są tylko dwie operacje: "przeczyszczanie" i "Odzyskaj". Wszystkie inne operacje zakończą się niepowodzeniem. W związku z tym, mimo że obiekt istnieje, nie można wykonać żadnych operacji i dlatego nie wystąpił żaden rachunek. Istnieją jednak następujące wyjątki:

- akcje "Wyczyść" i "Odzyskaj" będą wliczane do normalnych operacji magazynu kluczy i będą naliczane opłaty.
- Jeśli obiekt jest kluczem modułu HSM, opłata za klucz chroniony przez moduł HSM na wersję klucza miesięcznie będzie stosowana, jeśli w ciągu ostatnich 30 dni zostanie użyta wersja klucza. Po tym, ponieważ obiekt jest w stanie usunięty, nie można wykonywać żadnych operacji, więc opłaty nie będą naliczane.

## <a name="next-steps"></a>Następne kroki

Poniższe dwa przewodniki oferują podstawowe scenariusze użycia do korzystania z funkcji usuwania nietrwałego.

- [Jak używać usuwania nietrwałego w usłudze Key Vault z programem PowerShell](soft-delete-powershell.md) 
- [Jak używać usuwania nietrwałego w usłudze Key Vault z interfejsem wiersza polecenia](soft-delete-cli.md)

