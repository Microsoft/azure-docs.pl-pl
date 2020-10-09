---
title: Azure Key Vault — jak używać nietrwałego usuwania przy użyciu programu PowerShell
description: Dowiedz się, jak za pomocą Azure PowerShell używać funkcji usuwania nietrwałego Azure Key Vault, która umożliwia odzyskiwanie magazynów kluczy i obiektów magazynu kluczy.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/11/2020
ms.author: sudbalas
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 05ff5d2f7ff87d2ce58f135330487f746cc5c1c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90528367"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Jak używać usuwania nietrwałego w usłudze Key Vault z programem PowerShell

Funkcja usuwania nietrwałego Azure Key Vault umożliwia Odzyskiwanie usuniętych magazynów i obiektów magazynu. W przypadku usuwania nietrwałego są stosowane następujące scenariusze:

- Obsługa odwracalnego usuwania magazynu kluczy
- Obsługa odwracalnego usuwania obiektów magazynu kluczy; klucze, wpisy tajne i certyfikaty

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 lub nowszy — Jeśli nie masz już tej konfiguracji, Zainstaluj Azure PowerShell i skojarz ją z subskrypcją platformy Azure, zobacz [jak zainstalować i skonfigurować Azure PowerShell](https://docs.microsoft.com/powershell/azure/). 

>[!NOTE]
> Istnieje nieaktualna wersja Key Vault naszego pliku formatowania danych wyjściowych programu PowerShell, który **może** zostać załadowany do środowiska, a nie w poprawnej wersji. Przewidujemy, że zaktualizowana wersja programu PowerShell będzie zawierać wymaganą poprawkę dla formatowania danych wyjściowych i zaktualizuje ten temat w tym czasie. Bieżące obejście, jeśli wystąpi ten problem z formatowaniem, to:
> - Jeśli zauważysz, że nie widzisz właściwości Enabled z włączonym usuwaniem w tym temacie, użyj następującego zapytania: `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete` .


Aby uzyskać szczegółowe informacje dotyczące Key Vault programu PowerShell, zobacz [Azure Key Vault Dokumentacja programu PowerShell](/powershell/module/az.keyvault).

## <a name="required-permissions"></a>Wymagane uprawnienia

Operacje Key Vault są zarządzane oddzielnie za pośrednictwem uprawnień kontroli dostępu opartej na rolach (RBAC) w następujący sposób:

| Operacja | Opis | Uprawnienie użytkownika |
|:--|:--|:--|
|Lista|Wyświetla listę usuniętych magazynów kluczy.|Microsoft./Magazyn kluczy/deletedVaults/odczyt|
|Recover|Przywraca usunięty Magazyn kluczy.|Microsoft./magazyny kluczy/magazynu/zapis|
|Purge|Trwale usuwa usunięty Magazyn kluczy i całą jego zawartość.|Microsoft./Magazyn kluczy/lokalizacji/deletedVaults/przeczyszczanie/akcja|

Aby uzyskać więcej informacji na temat uprawnień i kontroli dostępu, zobacz temat [Zabezpieczanie magazynu kluczy](secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Włączanie usuwania nietrwałego

Włączenie "nietrwałe usuwanie" pozwala na odzyskanie usuniętego magazynu kluczy lub obiektów przechowywanych w magazynie kluczy.

> [!IMPORTANT]
> Włączenie elementu "Soft-Delete" w magazynie kluczy jest akcją nieodwracalną. Po ustawieniu właściwości nietrwałego usuwania na wartość "true" nie można jej zmienić ani usunąć.  

### <a name="existing-key-vault"></a>Istniejący magazyn kluczy

W przypadku istniejącego magazynu kluczy o nazwie ContosoVault Włącz program unsoft-DELETE w następujący sposób. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Nowy magazyn kluczy

Automatyczne usuwanie jest domyślnie włączone dla wszystkich nowych magazynów kluczy. 31 grudnia 2020 nie będzie można wyłączyć usuwania nietrwałego w żadnym magazynie kluczy. 

### <a name="verify-soft-delete-enablement"></a>Weryfikuj Włączanie usuwania nietrwałego

Aby sprawdzić, czy w magazynie kluczy jest włączona funkcja usuwania nietrwałego, uruchom polecenie *Pokaż* i poszukaj "nietrwałego usuwania włączonego?". przypisane

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Usuwanie nietrwałego chronionego magazynu kluczy

Polecenie usuwania zmian w magazynie kluczy w zależności od tego, czy jest włączone usuwanie nietrwałe.

> [!IMPORTANT]
>W przypadku uruchomienia następującego polecenia dla magazynu kluczy, który nie ma włączonej opcji usuwania nietrwałego, ten magazyn kluczy zostanie trwale usunięty i cała jego zawartość nie będzie dostępna w ramach odzyskiwania.

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Jak usuwanie nietrwałe chroni Twoje magazyny kluczy

Z włączonym niemiękkim usuwaniem:

- Usunięty Magazyn kluczy zostanie usunięty z grupy zasobów i umieszczony w zarezerwowanej przestrzeni nazw skojarzonej z lokalizacją, w której została utworzona. 
- Usunięte obiekty, takie jak klucze, wpisy tajne i certyfikaty, są niedostępne, o ile ich Magazyn kluczy jest w stanie usunięty. 
- Nazwa DNS usuniętego magazynu kluczy jest zastrzeżona, uniemożliwiając utworzenie nowego magazynu kluczy o tej samej nazwie.  

Można wyświetlić usunięte magazyny kluczy stanu skojarzone z subskrypcją za pomocą następującego polecenia:

```powershell
Get-AzKeyVault -InRemovedState 
```

- *Identyfikatora* można użyć do zidentyfikowania zasobu podczas odzyskiwania lub przeczyszczania. 
- *Identyfikator zasobu* to oryginalny identyfikator zasobu tego magazynu. Ponieważ ten magazyn kluczy jest teraz w stanie usunięty, nie istnieje żaden zasób z tym IDENTYFIKATORem zasobu. 
- *Zaplanowana data przeczyszczania* oznacza, że magazyn zostanie trwale usunięty, jeśli nie zostanie podjęta żadna akcja. Domyślny okres przechowywania używany do obliczania *zaplanowanej daty przeczyszczania*to 90 dni.

## <a name="recovering-a-key-vault"></a>Odzyskiwanie magazynu kluczy

Aby odzyskać Magazyn kluczy, należy określić nazwę magazynu kluczy, grupę zasobów i lokalizację. Zanotuj lokalizację i grupę zasobów usuniętego magazynu kluczy, ponieważ są one potrzebne do procesu odzyskiwania.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Po odzyskaniu magazynu kluczy zostanie utworzony nowy zasób z oryginalnym IDENTYFIKATORem zasobu magazynu kluczy. Jeśli oryginalna Grupa zasobów zostanie usunięta, przed podjęciem próby odzyskania należy utworzyć jedną z nich o tej samej nazwie.

## <a name="deleting-and-purging-key-vault-objects"></a>Usuwanie i przeczyszczanie obiektów magazynu kluczy

Następujące polecenie spowoduje usunięcie klucza "ContosoFirstKey" w magazynie kluczy o nazwie "ContosoVault", który ma włączony program Undelete:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Jeśli magazyn kluczy został włączony do usuwania nietrwałego, usunięty klucz nadal wydaje się zostać usunięty, chyba że jawnie wyświetlona zostanie lista usuniętych kluczy. Większość operacji na kluczu w stanie usuniętych zakończy się niepowodzeniem, z wyjątkiem tworzenia listy, odzyskiwania, przeczyszczania usuniętego klucza. 

Na przykład następujące polecenie wyświetla listę usuniętych kluczy w magazynie kluczy "ContosoVault":

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Stan przejścia 

Po usunięciu klucza w magazynie kluczy z włączonym nieaktywnym usuwaniem, ukończenie przejścia może potrwać kilka sekund. W trakcie tego przejścia może pojawić się, że klucz nie jest w stanie aktywnym lub został usunięty. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Używanie nietrwałego usuwania z obiektami magazynu kluczy

Podobnie jak w przypadku magazynów kluczy, usunięty klucz, wpis tajny lub certyfikat, pozostaje w stanie usunięte przez maksymalnie 90 dni, chyba że zostanie odzyskany lub przeczyszczony. 

#### <a name="keys"></a>Klucze

Aby odzyskać klucz nietrwały:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Aby trwale usunąć (nazywane także przeczyszczaniem) klucz nietrwałego:

> [!IMPORTANT]
> Przeczyszczanie klucza spowoduje jego trwałe usunięcie i nie będzie można go odzyskać. 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

Akcje **odzyskania** i **przeczyszczania** mają własne uprawnienia skojarzone z zasadami dostępu magazynu kluczy. Aby użytkownik lub usługa mogła wykonać akcję **odzyskania** lub **przeczyszczania** , muszą mieć odpowiednie uprawnienia do tego klucza lub wpisu tajnego. Domyślnie **przeczyszczanie** nie jest dodawane do zasad dostępu magazynu kluczy, gdy skrót "wszystkie" jest używany do udzielania wszystkich uprawnień. Musisz jawnie udzielić uprawnienia do **przeczyszczania** . 

#### <a name="set-a-key-vault-access-policy"></a>Ustawianie zasad dostępu do magazynu kluczy

Następujące polecenie przyznaje user@contoso.com uprawnienia do korzystania z kilku operacji na kluczach w *ContosoVault* , w tym **przeczyszczania**:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Jeśli masz już Magazyn kluczy, który ma włączoną opcję usuwania nietrwałego, możesz nie mieć uprawnień do **odzyskiwania** i **przeczyszczania** .

#### <a name="secrets"></a>Wpisy tajne

Podobnie jak klucze, wpisy tajne są zarządzane przy użyciu własnych poleceń:

- Usuń wpis tajny o nazwie SQLPassword: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Wyświetl listę wszystkich usuniętych wpisów tajnych w magazynie kluczy: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Odzyskaj wpis tajny w stanie usunięte: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Przeczyszczanie wpisu tajnego w stanie usunięte: 

  > [!IMPORTANT]
  > Przeczyszczanie wpisu tajnego spowoduje jego trwałe usunięcie i nie będzie można go odzyskać.

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

#### <a name="certificates"></a>Certyfikaty

Można zarządzać certyfikatami za pomocą poniższych poleceń:

- Usuń certyfikat: 
  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

- Wyświetl listę wszystkich usuniętych certyfikatów w magazynie kluczy: 
  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

- Odzyskaj certyfikat w stanie usunięte: 
  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

- Przeczyść certyfikat w stanie usunięte: 

  > [!IMPORTANT]
  > Przeczyszczanie certyfikatu spowoduje jego trwałe usunięcie i nie będzie można go odzyskać.

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState 
  ```
  
## <a name="purging-a-soft-delete-protected-key-vault"></a>Przeczyszczanie nietrwałego, chronionego magazynu kluczy

> [!IMPORTANT]
> Przeczyszczanie magazynu kluczy lub jednego z zawartych w nim obiektów spowoduje trwałe usunięcie go, co oznacza, że nie będzie można go odzyskać.

Funkcja przeczyszczania służy do trwałego usuwania obiektu magazynu kluczy lub całego magazynu kluczy, który został wcześniej usunięty. Jak pokazano w poprzedniej sekcji, obiekty przechowywane w magazynie kluczy z włączoną funkcją usuwania nietrwałego mogą przechodzić przez wiele stanów:
- **Aktywny**: przed usunięciem.
- **Nietrwałe usunięte**: po usunięciu można je wyświetlić i odzyskać z powrotem do stanu aktywnego.
- **Trwale usunięto**: po przeczyszczeniu, nie można odzyskać.


Ta sama wartość dotyczy magazynu kluczy. W celu trwałego usunięcia nietrwałego magazynu kluczy i jego zawartości należy przeczyścić sam magazyn kluczy.

### <a name="purging-a-key-vault"></a>Przeczyszczanie magazynu kluczy

Po przeczyszczeniu magazynu kluczy jego cała zawartość jest trwale usuwana, w tym kluczy, wpisów tajnych i certyfikatów. Aby przeczyścić usunięty nietrwale Magazyn kluczy, użyj `Remove-AzKeyVault` polecenia z opcją `-InRemovedState` i określając lokalizację usuniętego magazynu kluczy z `-Location location` argumentem. Lokalizację usuniętego magazynu można znaleźć za pomocą polecenia `Get-AzKeyVault -InRemovedState` .

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Wymagane uprawnienia przeczyszczania
- Aby przeczyścić usunięty Magazyn kluczy, użytkownik musi mieć uprawnienia RBAC do operacji *Microsoft. Key/Locations/deletedVaults/przeczyszczania/akcji* . 
- Aby wyświetlić usunięty Magazyn kluczy, użytkownik musi mieć uprawnienia RBAC do operacji *Microsoft. Key/deletedVaults/Read* . 
- Domyślnie tylko administrator subskrypcji ma te uprawnienia. 

### <a name="scheduled-purge"></a>Zaplanowane przeczyszczanie

Wyświetlanie listy usuniętych obiektów magazynu kluczy również pokazuje, kiedy mają być przeczyszczone przez Key Vault. *Zaplanowana data przeczyszczania* wskazuje, kiedy obiekt magazynu kluczy zostanie trwale usunięty, jeśli nie zostanie podjęta żadna akcja. Domyślnie okres przechowywania usuniętego obiektu magazynu kluczy wynosi 90 dni.

>[!IMPORTANT]
>Przeczyszczony obiekt magazynu wyzwalany przez *zaplanowaną wartość pola Data przeczyszczania* zostanie trwale usunięty. Nie jest możliwe do odzyskania!

## <a name="enabling-purge-protection"></a>Włączanie ochrony przed czyszczeniem

Po włączeniu ochrony przed przeczyszczeniem nie można czyścić magazynu ani obiektu w stanie usuniętym, dopóki nie upłynie okres przechowywania. Taki magazyn lub obiekt nadal można odzyskać. Ta funkcja zapewnia dodatkową gwarancję, że magazyn lub obiekt nigdy nie można trwale usunąć, dopóki nie upłynie okres przechowywania. Domyślny okres przechowywania to 90 dni, ale podczas tworzenia magazynu kluczy można ustawić interwał zasad przechowywania na wartość z przedziału od 7 do 90 dni. Zasady przechowywania ochrony przed przeczyszczeniem używają tego samego interwału. Po ustawieniu nie można zmienić interwału zasad przechowywania.

Ochronę przeczyszczania można włączyć tylko wtedy, gdy jest również włączona funkcja usuwania nietrwałego. Wyłączenie ochrony przed czyszczeniem nie jest obsługiwane. 

Aby włączyć ochronę przed usuwaniem i przeczyszczaniem podczas tworzenia magazynu, użyj polecenia cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0) :

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Aby dodać ochronę przed przeczyszczeniem do istniejącego magazynu (ma już włączony program unsoft-Delete), Użyj poleceń cmdlet [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0), [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0)i [Set-AzResource](/powershell/module/az.resources/set-azresource?view=azps-1.5.0) :

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Inne zasoby

- Omówienie funkcji usuwania nietrwałego Key Vault można znaleźć w temacie [Azure Key Vault unsoft-Delete Overview](soft-delete-overview.md).
- Aby uzyskać ogólne omówienie użycia Azure Key Vault, zobacz [co to jest Azure Key Vault?](overview.md).
