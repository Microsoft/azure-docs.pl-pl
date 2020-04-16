---
title: Usuwanie nietrwałe usługi Azure Key Vault | Dokumenty firmy Microsoft
description: Usuwanie nietrwałe w usłudze Azure Key Vault umożliwia odzyskiwanie usuniętych magazynów kluczy i obiektów magazynu kluczy, takich jak klucze, wpisy tajne i certyfikaty.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: 6185f0d84f27b6be89e797fc7cfb22940d8c6401
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432101"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Azure Key Vault — omówienie usuwania nietrwałego

Funkcja nietrwałego usuwania w magazynie kluczy umożliwia odzyskiwanie usuniętych przechowalni i obiektów przechowalni, znanych jako usuwanie nietrwałe. W szczególności możemy rozwiązać następujące scenariusze:

- Obsługa możliwego do odzyskania usunięcia magazynu kluczy
- Obsługa możliwego do odzyskania usunięcia obiektów magazynu kluczy (np. klucze, wpisy tajne, certyfikaty)

## <a name="supporting-interfaces"></a>Interfejsy pomocnicze

Funkcja usuwania nietrwałego jest początkowo dostępna za pośrednictwem interfejsów [REST,](/rest/api/keyvault/) [CLI,](soft-delete-cli.md) [PowerShell](soft-delete-powershell.md) i [.NET/C#.](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet)

## <a name="scenarios"></a>Scenariusze

Usługi Azure Key Vaults są śledzone zasoby zarządzane przez usługę Azure Resource Manager. Usługa Azure Resource Manager określa również dobrze zdefiniowane zachowanie do usunięcia, które wymaga, że pomyślna operacja DELETE musi spowodować, że ten zasób nie jest już dostępny. Funkcja usuwania nietrwałego dotyczy odzyskiwania usuniętego obiektu, niezależnie od tego, czy usunięcie było przypadkowe, czy zamierzone.

1. W typowym scenariuszu użytkownik mógł przypadkowo usunąć magazyn kluczy lub obiekt magazynu kluczy; jeśli ten magazyn kluczy lub obiekt magazynu kluczy były możliwe do odzyskania przez określony czas, użytkownik może cofnąć usunięcie i odzyskać swoje dane.

2. W innym scenariuszu nieautoryzowany użytkownik może próbować usunąć magazyn kluczy lub obiekt magazynu kluczy, taki jak klucz wewnątrz magazynu, aby spowodować zakłócenia biznesowe. Oddzielenie usunięcia obiektu magazynu kluczy lub magazynu kluczy od rzeczywistego usunięcia danych źródłowych może służyć jako środek bezpieczeństwa, na przykład ograniczając uprawnienia do usuwania danych do innej, zaufanej roli. Takie podejście skutecznie wymaga kworum dla operacji, które w przeciwnym razie może spowodować natychmiastową utratę danych.

### <a name="soft-delete-behavior"></a>Zachowanie usuwania nietrwałego

Po włączeniu usuwania nietrwałego zasoby oznaczone jako usunięte zasoby są zachowywane przez określony czas (domyślnie 90 dni). Usługa dodatkowo zapewnia mechanizm odzyskiwania usuniętego obiektu, zasadniczo cofając usunięcie.

Podczas tworzenia nowego magazynu kluczy, soft-delete jest domyślnie włączony. Można utworzyć magazyn kluczy bez usuwania nietrwałego za pośrednictwem [interfejsu wiersza polecenia platformy Azure](soft-delete-cli.md) lub programu Azure [Powershell](soft-delete-powershell.md). Po włączeniu usuwania nietrwałego w przechowalni kluczy nie można go wyłączyć

Domyślny okres przechowywania wynosi 90 dni, ale podczas tworzenia magazynu kluczy można ustawić interwał zasad przechowywania na wartość od 7 do 90 dni za pośrednictwem witryny Azure portal. Zasady przechowywania ochrony przeczyszczania używa tego samego interwału. Po ustawieniu nie można zmienić interwału zasad przechowywania.

Nie można ponownie użyć nazwy magazynu kluczy, który został usunięty bez systemu, dopóki nie upłynie okres przechowywania.

### <a name="purge-protection"></a>Ochrona przed oczyszczaniem 

Ochrona przed przeczyszczaniem jest opcjonalnym zachowaniem magazynu kluczy i nie jest **domyślnie włączona.** Można go włączyć za pomocą [interfejsu WIERSZA LUB](soft-delete-cli.md#enabling-purge-protection) [programu Powershell.](soft-delete-powershell.md#enabling-purge-protection)

Gdy ochrona przed przeczyszczaniem jest wł., przechowalni lub obiekt w stanie usuniętym nie można wyczyścić, dopóki nie upłynie okres przechowywania. Nietrętne usunięte magazyny i obiekty nadal można odzyskać, zapewniając, że zasady przechowywania będą przestrzegane. 

Domyślny okres przechowywania wynosi 90 dni, ale można ustawić interwał zasad przechowywania na wartość od 7 do 90 dni za pośrednictwem witryny Azure portal. Po ustawieniu i zapisaniu interwału zasad przechowywania nie można go zmienić dla tego magazynu. 

### <a name="permitted-purge"></a>Dozwolone oczyszczanie

Trwale usuwanie, czyszczenie, magazyn kluczy jest możliwe za pomocą operacji POST na zasób serwera proxy i wymaga specjalnych uprawnień. Ogólnie rzecz biorąc tylko właściciel subskrypcji będzie mógł przeczyścić magazyn kluczy. Operacja POST wyzwala natychmiastowe i nieodwracalne usunięcie tego magazynu. 

Wyjątek stanowią:
- Gdy subskrypcja platformy Azure została oznaczona jako *cofalna*. W takim przypadku tylko usługa może następnie wykonać rzeczywiste usunięcie i robi to jako zaplanowany proces. 
- Gdy flaga --enable-purge-protection jest włączona w samym przechowalni. W takim przypadku Usługa Key Vault będzie czekać 90 dni od momentu oznaczenia oryginalnego obiektu tajnego do usunięcia w celu trwałego usunięcia obiektu.

### <a name="key-vault-recovery"></a>Odzyskiwanie magazynu kluczy

Po usunięciu magazynu kluczy usługa tworzy zasób serwera proxy w ramach subskrypcji, dodając wystarczające metadane do odzyskiwania. Zasób serwera proxy jest obiektem przechowywanym, dostępnym w tej samej lokalizacji co usunięty magazyn kluczy. 

### <a name="key-vault-object-recovery"></a>Odzyskiwanie obiektów magazynu kluczy

Po usunięciu obiektu magazynu kluczy, takiego jak klucz, usługa umieści obiekt w stanie usuniętym, co spowoduje, że będzie on niedostępny dla wszystkich operacji pobierania. W tym stanie obiekt magazynu kluczy można wymienić, odzyskać lub trwale/trwale usunąć. 

W tym samym czasie Usługa Key Vault zaplanuje usunięcie danych bazowych odpowiadających usuniętemu magazynowi kluczy lub obiektowi magazynu kluczy do wykonania po określonym interwale przechowywania. Rekord DNS odpowiadający przechowalni jest również zachowywany przez cały czas trwania interwału przechowywania.

### <a name="soft-delete-retention-period"></a>Okres przechowywania usuwania nietrwałego

Nietrwałe usunięte zasoby są zachowywane przez określony czas, czyli 90 dni. Podczas okresu przechowywania usuwania nietrwałego stosuje się następujące zasady:

- Można wyświetlić listę wszystkich magazynów kluczy i obiektów magazynu kluczy w stanie usuwania nietrwałego dla subskrypcji, a także uzyskać dostęp do informacji o ich usuwaniu i usuwaniu odzyskiwania.
    - Tylko użytkownicy ze specjalnymi uprawnieniami mogą wystawiać usunięte przechowalnie. Zaleca się, aby nasi użytkownicy tworzyli rolę niestandardową z tymi specjalnymi uprawnieniami do obsługi usuniętych magazynów.
- Nie można utworzyć magazynu kluczy o tej samej nazwie w tej samej lokalizacji; odpowiednio nie można utworzyć obiektu magazynu kluczy w danym przechowalni, jeśli ten magazyn kluczy zawiera obiekt o tej samej nazwie i który znajduje się w stanie usuniętym 
- Tylko specjalnie uprzywilejowany użytkownik może przywrócić magazyn kluczy lub obiekt magazynu kluczy, wydając polecenie odzyskiwania odpowiedniego zasobu serwera proxy.
    - Użytkownik, członek roli niestandardowej, który ma uprawnienia do tworzenia magazynu kluczy w grupie zasobów, może przywrócić przechowalnię.
- Tylko specjalnie uprzywilejowany użytkownik może wymęczeć usunąć przechowalnię kluczy lub obiekt magazynu kluczy, wydając polecenie usuwania odpowiedniego zasobu serwera proxy.

O ile nie zostanie odzyskana przechowalnia kluczy lub obiekt magazynu kluczy, po zakończeniu interwału przechowywania usługa wykonuje przeczyszczanie nietrwale usuniętego obiektu magazynu kluczy lub magazynu kluczy i jego zawartości. Usunięcie zasobów nie może być przełożone.

### <a name="billing-implications"></a>Implikacje rozliczeń

Ogólnie rzecz biorąc, gdy obiekt (magazyn kluczy lub klucz lub klucz lub klucz tajny) jest w stanie usuniętym, możliwe są tylko dwie operacje: "przeczyszczanie" i "odzyskać". Wszystkie inne operacje zakończy się niepowodzeniem. W związku z tym, mimo że obiekt istnieje, nie można wykonać żadnych operacji i w związku z tym nie nastąpi użycie, więc nie rachunek. Istnieją jednak następujące wyjątki:

- Akcje "przeczyścić" i "odzyskać" będą wliczane do normalnych operacji magazynu kluczy i będą rozliczane.
- Jeśli obiekt jest kluczem HSM, zostanie naliczona opłata "Klucz chroniony HSM" za wersję klucza miesięcznie, jeśli wersja klucza została użyta w ciągu ostatnich 30 dni. Następnie, ponieważ obiekt jest w stanie usunięte, nie można wykonać żadnych operacji przeciwko niemu, więc nie zostanie naliczona żadna opłata.

## <a name="next-steps"></a>Następne kroki

Następujące dwa przewodniki oferują podstawowe scenariusze użycia przy użyciu usuwania nietrwałego.

- [Jak używać usuwania nietrwałego w usłudze Key Vault z programem PowerShell](soft-delete-powershell.md) 
- [Jak używać usuwania nietrwałego w usłudze Key Vault z interfejsem wiersza polecenia](soft-delete-cli.md)

