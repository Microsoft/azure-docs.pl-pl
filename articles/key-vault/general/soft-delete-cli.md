---
title: Usługa Azure Key Vault — jak używać usuwania nietrwałego za pomocą interfejsu wiersza polecenia
description: Przykłady przypadków usuwania nietrwałego z wycięciem kodu CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: c8e4083035d87696ec44d06a62bc55ba721c6f03
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422928"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Jak używać usuwania nietrwałego w usłudze Key Vault z interfejsem wiersza polecenia

Funkcja nietrwałego usuwania usługi Azure Key Vault umożliwia odzyskiwanie usuniętych magazynów i obiektów przechowalni. W szczególności soft-delete adresy następujące scenariusze:

- Obsługa możliwego do odzyskania usunięcia magazynu kluczy
- Obsługa możliwego do odzyskania usunięcia obiektów magazynu kluczy; klucze, wpisy tajne i certyfikaty

## <a name="prerequisites"></a>Wymagania wstępne

- Narzędzie cli platformy Azure — jeśli nie masz tej konfiguracji dla swojego środowiska, zobacz [Zarządzanie magazynem kluczy przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli2.md)).

Aby uzyskać informacje dotyczące określonych numerów kluczy w magazynie kluczy dla interfejsu wiersza polecenia, zobacz [Odwołanie do usługi Azure CLI Key Vault](https://docs.microsoft.com/cli/azure/keyvault).

## <a name="required-permissions"></a>Wymagane uprawnienia

Operacje magazynu kluczy są oddzielnie zarządzane za pomocą uprawnień kontroli dostępu opartej na rolach (RBAC) w następujący sposób:

| Operacja | Opis | Uprawnienie użytkownika |
|:--|:--|:--|
|List|Wyświetla listę usuniętych magazynów kluczy.|Microsoft.KeyVault/deletedVaults/read|
|Recover|Przywraca usunięty magazyn kluczy.|Microsoft.KeyVault/vaults/write|
|Purge|Trwale usuwa usunięty magazyn kluczy i całą jego zawartość.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Aby uzyskać więcej informacji na temat uprawnień i kontroli dostępu, zobacz [Zabezpieczanie magazynu kluczy](secure-your-key-vault.md)).

## <a name="enabling-soft-delete"></a>Włączanie usuwania nietrwałego

Można włączyć "soft-delete", aby umożliwić odzyskiwanie usuniętego magazynu kluczy lub obiektów przechowywanych w magazynie kluczy.

> [!IMPORTANT]
> Włączenie "usuwania nietrwałego" w magazynie kluczy jest nieodwracalną akcją. Po ustawieniu właściwości usuwania nietrwałego na "true", nie można jej zmienić ani usunąć.  

### <a name="existing-key-vault"></a>Istniejący magazyn kluczy

W przypadku istniejącego magazynu kluczy o nazwie ContosoVault włącz usuwanie programowe w następujący sposób. 

```azurecli
az keyvault update -n ContosoVault --enable-soft-delete true
```

### <a name="new-key-vault"></a>Nowy magazyn kluczy

Włączenie usuwania nietrwałego dla nowego magazynu kluczy odbywa się w czasie tworzenia, dodając flagę włącz soft-delete do polecenia create.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Weryfikowanie włączenia usuwania nietrwałego

Aby sprawdzić, czy magazyn kluczy ma włączone usuwanie nietrwałe, uruchom polecenie *show* i poszukaj opcji "Nieuichowane usuwanie włączone?". Atrybut:

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Usuwanie chronionego magazynu kluczy o odręcznym usuwaniu

Polecenie usunięcia magazynu kluczy zmienia zachowanie w zależności od tego, czy jest włączone usuwanie nietrwałe.

> [!IMPORTANT]
>Jeśli uruchomisz następujące polecenie dla magazynu kluczy, który nie ma włączonego usuwania nietrwałego, trwale usuniesz ten magazyn kluczy i całą jego zawartość bez opcji odzyskiwania!

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Jak usuwanie nietrwałe chroni magazyny kluczy

Z włączonym usuwaniem nietrwałym:

- Usunięty magazyn kluczy jest usuwany z grupy zasobów i umieszczany w zastrzeżonym obszarze nazw skojarzonym z lokalizacją, w której został utworzony. 
- Usunięte obiekty, takie jak klucze, wpisy tajne i certyfikaty, są niedostępne, o ile ich magazyn kluczy znajduje się w stanie usuniętym. 
- Nazwa DNS usuniętego magazynu kluczy jest zarezerwowana, co uniemożliwia tworzenie nowego magazynu kluczy o tej samej nazwie.  

Można wyświetlić usunięte magazyny kluczy stanu skojarzone z subskrypcją, używając następującego polecenia:

```azurecli
az keyvault list-deleted
```
- *Identyfikator* może służyć do identyfikowania zasobu podczas odzyskiwania lub czyszczenia. 
- *Identyfikator zasobu* jest oryginalnym identyfikatorem zasobu tego przechowalni. Ponieważ ten magazyn kluczy jest teraz w stanie usuniętym, nie istnieje żaden zasób o tym identyfikatorze zasobu. 
- *Zaplanowana data przeczyszczenie* to czas, w którym przechowalnia zostanie trwale usunięta, jeśli nie zostanie podjęta żadna akcja. Domyślny okres przechowywania używany do obliczania *zaplanowanej daty przeczyszczenia*wynosi 90 dni.

## <a name="recovering-a-key-vault"></a>Odzyskiwanie magazynu kluczy

Aby odzyskać magazyn kluczy, należy określić nazwę magazynu kluczy, grupę zasobów i lokalizację. Zanotuj lokalizację i grupę zasobów usuniętego magazynu kluczy, ponieważ są one potrzebne do procesu odzyskiwania.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

Po odzyskaniu magazynu kluczy tworzony jest nowy zasób z oryginalnym identyfikatorem zasobu magazynu kluczy. Jeśli oryginalna grupa zasobów zostanie usunięta, przed podjęciem próby odzyskania należy utworzyć ją o tej samej nazwie.

## <a name="deleting-and-purging-key-vault-objects"></a>Usuwanie i czyszczenie obiektów magazynu kluczy

Następujące polecenie spowoduje usunięcie klucza "ContosoFirstKey" w magazynie kluczy o nazwie "ContosoVault", który ma włączone usuwanie nietrwałe:

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Po włączeniu magazynu kluczy do usuwania nietrwałego usunięty klucz nadal wygląda tak, jakby został usunięty, z wyjątkiem sytuacji jawnego wystawiania lub pobierania usuniętych kluczy. Większość operacji na kluczu w stanie usuniętego zakończy się niepowodzeniem, z wyjątkiem listy usuniętego klucza, odzyskania go lub przeczyszczenie go. 

Na przykład, aby zażądać listy usuniętych kluczy w magazynie kluczy, użyj następującego polecenia:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Stan przejścia 

Usunięcie klucza w magazynie kluczy z włączonym usuwaniem nietrwałym może potrwać kilka sekund. Podczas tego przejścia może się wydawać, że klucz nie jest w stanie aktywnym lub usuniętym. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Używanie usuwania nietrwałego z obiektami magazynu kluczy

Podobnie jak magazyny kluczy, usunięty klucz, klucz tajny lub certyfikat pozostaje w stanie usuniętym przez okres do 90 dni, chyba że go odzyskasz lub przeczyścisz.

#### <a name="keys"></a>Klucze

Aby odzyskać klucz nieumiejętny:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Aby trwale usunąć (znany również jako przeczyszczanie) klucz nietrwale usunięty:

> [!IMPORTANT]
> Czyszczenie klucza spowoduje trwałe usunięcie go i nie będzie można go odzyskać! 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

Akcje **odzyskiwania** i **przeczyszczania** mają własne uprawnienia skojarzone z zasadami dostępu do magazynu kluczy. Aby użytkownik lub podmiot usługi mógł wykonać akcję **odzyskiwania** lub **przeczyszczanie,** musi mieć odpowiednie uprawnienia dla tego klucza lub klucza tajnego. Domyślnie **przeczyszczanie** nie jest dodawane do zasad dostępu magazynu kluczy, gdy skrót "wszystkie" jest używany do udzielania wszystkich uprawnień. Należy w szczególności udzielić uprawnień **przeczyścić.** 

#### <a name="set-a-key-vault-access-policy"></a>Ustawianie zasad dostępu do magazynu kluczy

Następujące polecenie user@contoso.com udziela uprawnień do używania kilku operacji na kluczach w *contosoVault,* w tym **przeczyszczania:**

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Jeśli masz istniejący magazyn kluczy, który właśnie został włączony soft-delete, możesz nie mieć uprawnień **do odzyskiwania** i **przeczyszczać.**

#### <a name="secrets"></a>Wpisy tajne

Podobnie jak klucze, wpisy tajne są zarządzane za pomocą własnych poleceń:

- Usuń klucz tajny o nazwie SQLPassword: 
  ```azurecli
  az keyvault secret delete --vault-name ContosoVault -name SQLPassword
  ```

- Wyświetl listę wszystkich usuniętych wpisów tajnych w magazynie kluczy: 
  ```azurecli
  az keyvault secret list-deleted --vault-name ContosoVault
  ```

- Odzyskaj klucz tajny w usuniętym stanie: 
  ```azurecli
  az keyvault secret recover --name SQLPassword --vault-name ContosoVault
  ```

- Przeczyść klucz tajny w usuniętym stanie: 

  > [!IMPORTANT]
  > Oczyszczenie tajemnicy spowoduje trwałe usunięcie go i nie będzie można go odzyskać! 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>Czyszczenie chronionego magazynu kluczy o nieumiejętnym usuwaniu

> [!IMPORTANT]
> Przeczyszczanie magazynu kluczy lub jednego z jego zawartych obiektów spowoduje trwałe usunięcie go, co oznacza, że nie będzie można go odzyskać!

Funkcja przeczyszczania służy do trwałego usuwania obiektu magazynu kluczy lub całego magazynu kluczy, który został wcześniej usunięty z użyciem nietrwałych. Jak pokazano w poprzedniej sekcji, obiekty przechowywane w magazynie kluczy z włączoną funkcją usuwania nietrwałego, mogą przechodzić przez wiele stanów:

- **Aktywny:** przed usunięciem.
- **Soft-Deleted**: po usunięciu, może być wymieniony i odzyskane z powrotem do stanu aktywnego.
- **Trwale usunięte:** po oczyszczeniu, nie można odzyskać.

To samo dotyczy magazynu kluczy. Aby trwale usunąć magazyn kluczy usuniętych z kluczem bez kluczy i jego zawartość, należy przeczyścić sam magazyn kluczy.

### <a name="purging-a-key-vault"></a>Czyszczenie magazynu kluczy

Gdy magazyn kluczy jest czyszczący, jego cała zawartość jest trwale usuwana, łącznie z kluczami, wpisami tajnymi i certyfikatami. Aby przeczyścić nietrwale `az keyvault purge` usuniętą przechowalnię kluczy, użyj polecenia. Lokalizację usuniętych magazynów kluczy subskrypcji można znaleźć `az keyvault list-deleted`za pomocą polecenia .

```azurecli
az keyvault purge --location westus --name ContosoVault
```

### <a name="purge-permissions-required"></a>Wymagane uprawnienia oczyszczania
- Aby wyczyścić usunięty magazyn kluczy, użytkownik potrzebuje uprawnień RBAC do operacji *Microsoft.KeyVault/locations/deletedVaults/purge/action.* 
- Aby wyświetlić listę usuniętego magazynu kluczy, użytkownik potrzebuje uprawnień RBAC do operacji *Microsoft.KeyVault/deletedVaults/read.* 
- Domyślnie tylko administrator subskrypcji ma te uprawnienia. 

### <a name="scheduled-purge"></a>Zaplanowane przeczyszczanie

Lista usuniętych obiektów magazynu kluczy pokazuje również, kiedy są one zaplanowane do usunięcia przez Przechowalnię kluczy. *Zaplanowana data przeczyszczenie* wskazuje, kiedy obiekt magazynu kluczy zostanie trwale usunięty, jeśli nie zostanie podjęta żadna akcja. Domyślnie okres przechowywania usuniętego obiektu magazynu kluczy wynosi 90 dni.

>[!IMPORTANT]
>Wyczyszczony obiekt przechowalni, wyzwalany przez pole *Zaplanowana data przeczyszczenie,* zostanie trwale usunięty. Nie można go odzyskać!

## <a name="enabling-purge-protection"></a>Włączanie ochrony przed przeczyszczaniem

Po włączeniu ochrony przed przeczyszczaniem nie można wyczyścić przechowalni lub obiektu w stanie usuniętym, dopóki nie upłynie 90-dniowy okres przechowywania. Taki przechowalnia lub obiekt nadal można odzyskać. Ta funkcja daje dodatkową pewność, że magazyn lub obiekt nigdy nie może zostać trwale usunięty, dopóki nie upłynie okres przechowywania.

Ochronę przed przeczyszczaniem można włączyć tylko wtedy, gdy jest również włączone usuwanie nietrwałe. 

Aby włączyć zarówno ochronę usuwania nietrwałego, jak i przeczyszczania podczas tworzenia przechowalni, użyj polecenia [az keyvault create:](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
```

Aby dodać ochronę przed przeczyszczaniem do istniejącego magazynu (który ma już włączone usuwanie nietrwałe), użyj polecenia [az keyvault update:](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

```azurecli
az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
```

## <a name="other-resources"></a>Inne zasoby

- Aby zapoznać się z omówieniem funkcji usuwania nietrwałego usługi Key Vault, zobacz [omówienie usuwania nietrwałego usługi Azure Key Vault](overview-soft-delete.md)).
- Aby uzyskać ogólne omówienie użycia usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](overview.md)).

