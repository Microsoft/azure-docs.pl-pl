---
title: Zarządzanie kluczami w zarządzanym module HSM — Azure Key Vault | Microsoft Docs
description: Ten artykuł służy do zarządzania kluczami w zarządzanym module HSM
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 8d0cbd35b53bc8460ac8a19e5197d1f560657263
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212046"
---
# <a name="manage-a-managed-hsm-using-the-azure-cli"></a>Zarządzanie zarządzanym modułem HSM przy użyciu interfejsu wiersza polecenia platformy Azure

> [!NOTE]
> Key Vault obsługuje dwa typy zasobów: magazyny i zarządzane sprzętowych modułów zabezpieczeń. Ten artykuł zawiera informacje o **zarządzanym module HSM**. Jeśli chcesz dowiedzieć się, jak zarządzać magazynem, zobacz [zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure](../general/manage-with-cli2.md).

Aby zapoznać się z omówieniem zarządzanego modułu HSM, zobacz [co to jest zarządzany moduł HSM?](overview.md)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz dysponować następującymi elementami:

* Subskrypcja platformy Microsoft Azure. Jeśli go nie masz, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial).
* Interfejs wiersza polecenia platformy Azure w wersji 2.12.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).
* Zarządzany moduł HSM w ramach subskrypcji. Zobacz [Szybki Start: udostępnianie i aktywowanie zarządzanego modułu HSM przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md) w celu aprowizacji i aktywowania zarządzanego modułu HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia, możesz wpisać:

```azurecli
az login
```

Aby uzyskać więcej informacji na temat opcji logowania za pośrednictwem interfejsu wiersza polecenia, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli)

> [!NOTE]
> Wszystkie poniższe polecenia pokazują dwie metody użycia. Jeden z nich przy użyciu parametrów **--HSM-Name** i **--name** (dla nazwy klucza) i innego parametru **--ID** , w którym można określić cały adres URL, włącznie z nazwą klucza, jeśli jest to konieczne. Druga metoda jest przydatna, gdy obiekt wywołujący (użytkownik lub aplikacja) nie ma dostępu do odczytu na płaszczyźnie kontroli i ma ograniczony dostęp do płaszczyzny danych.

## <a name="create-an-hsm-key"></a>Tworzenie klucza HSM

Użyj `az keyvault key create` polecenia, aby utworzyć klucz.

### <a name="create-an-rsa-key"></a>Tworzenie klucza RSA

W poniższym przykładzie pokazano, jak utworzyć 3072-bitowy klucz **RSA** , który będzie używany tylko w przypadku operacji **wrapKey i unwrapKey** (--Ops). 


```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072

## OR
# Note the key name (myrsakey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072
```

Należy zauważyć, że `get` operacja zwraca tylko atrybuty klucza publicznego i klucza. Nie zwraca klucza prywatnego (w przypadku klucza asymetrycznego lub materiału klucza (w przypadku klucza symetrycznego).

### <a name="create-an-ec-key"></a>Tworzenie klucza we

W poniższym przykładzie pokazano, jak utworzyć klucz **ce** z krzywą P-256, który będzie używany tylko w przypadku operacji **podpisywania i weryfikowania** (--Ops) i ma dwa Tagi, **użycie** i **nazwa_aplikacji**. Tagi ułatwiają dodawanie dodatkowych metadanych do klucza do śledzenia i zarządzania.

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256

## OR
# Note the key name (myec256key) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256
```

### <a name="create-a-256-bit-symmetric-key"></a>Utwórz 256-bitowy klucz symetryczny

W poniższym przykładzie pokazano, jak utworzyć 256-bitowy klucz **symetryczny** , który będzie używany tylko w przypadku operacji **szyfrowania i odszyfrowywania** (--Ops).

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myaeskey --ops encrypt decrypt  --tags --kty oct-HSM --size 256

## OR
# Note the key name (myaeskey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myaeskey --ops encrypt decrypt  --tags ‘usage=signing] appname=myapp’ --kty oct-HSM --size 256
```

## <a name="view-key-attributes-and-tags"></a>Wyświetl atrybuty i Tagi klucza

Użyj `az keyvault key show` polecenia, aby wyświetlić atrybuty, wersje i Tagi dla klucza.

```azurecli-interactive
az keyvault key show --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key show --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey

```

## <a name="list-keys"></a>Klucze list

Użyj `az keyvault key list` polecenia, aby wyświetlić listę wszystkich kluczy w zarządzanym module HSM.

```azurecli-interactive
az keyvault key list --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list --id https://ContosoMHSM.managedhsm.azure.net/

```

## <a name="delete-a-key"></a>Usuń klucz

Użyj `az keyvault key delete` polecenia, aby usunąć klucz z zarządzanego modułu HSM. Należy zauważyć, że usuwanie nietrwałe jest zawsze włączone. W związku z tym usunięty klucz pozostanie w stanie usunięte i będzie można go odzyskać do momentu, gdy zostanie przekroczona liczba dni przechowywania, gdy klucz zostanie trwale usunięty, bez możliwości odzyskania.

```azurecli-interactive
az keyvault key delete --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key delete --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey
```

## <a name="list-deleted-keys"></a>Wyświetl usunięte klucze

Użyj `az keyvault key list-deleted` polecenia, aby wyświetlić listę wszystkich kluczy w stanie usunięte w zarządzanym module HSM.

```azurecli-interactive
az keyvault key list-deleted --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list-deleted --id https://ContosoMHSM.managedhsm.azure.net/
```

## <a name="recover-undelete-a-deleted-key"></a>Odzyskaj (Cofnij usunięcie) usunięty klucz

Użyj `az keyvault key list-deleted` polecenia, aby wyświetlić listę wszystkich kluczy w stanie usunięte w zarządzanym module HSM. Jeśli konieczne jest odzyskanie (Cofnięcie usunięcia) klucza przy użyciu parametru--ID podczas odzyskiwania usuniętego klucza, należy zwrócić uwagę na `recoveryId` wartość klucza usuniętego uzyskaną w `az keyvault key list-deleted` poleceniu.

```azurecli-interactive
az keyvault key recover --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey
```

## <a name="purge-permanently-delete-a-key"></a>Przeczyszczanie (trwałe usuwanie) klucza

Użyj `az keyvault key purge` polecenia, aby przeczyścić (trwale usunąć) klucz.

> [!NOTE]
> Jeśli zarządzany moduł HSM ma włączoną ochronę przeczyszczania, operacja przeczyszczania nie będzie dozwolona. Klucz zostanie automatycznie przeczyszczony po upływie okresu przechowywania.

```azurecli-interactive
az keyvault key purge --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey

```

## <a name="create-a-single-key-backup"></a>Tworzenie kopii zapasowej pojedynczego klucza

Użyj `az keyvault key backup` , aby utworzyć kopię zapasową klucza. Plik kopii zapasowej jest zaszyfrowanym obiektem BLOB kryptograficznie związanym z domeną zabezpieczeń źródłowego modułu HSM. Można go przywrócić tylko w sprzętowych modułów zabezpieczeń, który współużytkuje tę samą domenę zabezpieczeń. Przeczytaj więcej na temat [domeny zabezpieczeń](security-domain.md).

```azurecli-interactive
az keyvault key backup --hsm-name ContosoHSM --name myrsakey --file myrsakey.backup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key backup --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey  --file myrsakey.backup

```

## <a name="restore-a-single-key-from-backup"></a>Przywracanie pojedynczego klucza z kopii zapasowej

Użyj `az keyvault key restore` , aby przywrócić pojedynczy klucz. Źródłowy moduł HSM, w którym utworzono kopię zapasową, musi współużytkować tę samą domenę zabezpieczeń co docelowy moduł HSM, w którym trwa Przywracanie klucza.

> [!NOTE]
> Przywracanie nie powiedzie się, jeśli klucz o takiej samej nazwie istnieje w stanie aktywny lub usunięty.

```azurecli-interactive
az keyvault key restore --hsm-name ContosoHSM --name myrsakey --file myrsakey.bakup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --file myrsakey.bakup

```

## <a name="import-a-key-from-a-file"></a>Importuj klucz z pliku

Użyj `az keyvault key import` polecenia, aby zaimportować klucz (tylko RSA i EC) z pliku. Plik certyfikatu musi mieć klucz prywatny i musi używać kodowania PEM (zgodnie z definicją w dokumentach RFC [1421](https://tools.ietf.org/html/rfc1421), [1422](https://tools.ietf.org/html/rfc1422), [1423](https://tools.ietf.org/html/rfc1423), [1424](https://tools.ietf.org/html/rfc1424)).

```azurecli-interactive
az keyvault key import --hsm-name ContosoHSM --name myrsakey --pem-file mycert.key --password 'mypassword'

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --pem-file mycert.key --password 'mypassword'
```

Aby zaimportować klucz z lokalnego modułu HSM do zarządzanego modułu HSM, zobacz [Importowanie kluczy chronionych przez moduł HSM do zarządzanego modułu HSM (BYOK).](hsm-protected-keys-byok.md)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać pełne informacje na temat interfejsu wiersza polecenia platformy Azure dla poleceń magazynu kluczy, zobacz temat [Informacje o Key Vault CLI](/cli/azure/keyvault).
- Aby uzyskać informacje dotyczące programowania, zobacz [przewodnik dewelopera Azure Key Vault](../general/developers-guide.md)
- Dowiedz się więcej na temat [zarządzania zarządzaną rolą modułu HSM](role-management.md)
- Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących zarządzanego modułu HSM](best-practices.md)
