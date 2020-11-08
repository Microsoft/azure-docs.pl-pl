---
title: Pełna kopia zapasowa/przywracanie i selektywne przywracanie dla zarządzanego modułu HSM platformy Azure
description: W tym dokumencie opisano pełną kopię zapasową/przywracanie i selektywne przywracanie
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: e051a36b3c91fadc0c3b602cb4ba8e3dbcff1294
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367135"
---
# <a name="full-backup-and-restore"></a>Pełna kopia zapasowa i przywracanie

> [!NOTE]
> Ta funkcja jest dostępna tylko dla zarządzanego modułu HSM typu zasobu.

Zarządzany moduł HSM obsługuje tworzenie pełnej kopii zapasowej całej zawartości modułu HSM, w tym wszystkich kluczy, wersji, atrybutów, tagów i przypisań ról. Kopia zapasowa jest zaszyfrowana przy użyciu kluczy kryptograficznych skojarzonych z domeną zabezpieczeń modułu HSM.

Kopia zapasowa jest operacją płaszczyzny danych. Obiekt wywołujący inicjujący operację tworzenia kopii zapasowej musi mieć uprawnienia do wykonywania akcji elementu **Microsoft., magazynu/managedHsm/tworzenia kopii zapasowej/uruchamiania/akcji**.

Tylko następujące wbudowane role mają uprawnienia do wykonywania pełnej kopii zapasowej:
- Zarządzany administrator modułu HSM
- Zarządzana kopia zapasowa modułu HSM

Aby wykonać pełną kopię zapasową, należy podać następujące informacje:
- Nazwa lub adres URL modułu HSM
- Nazwa konta magazynu
- Kontener magazynu obiektów BLOB konta magazynu
- Token SAS kontenera magazynu z uprawnieniami `crdw`

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="full-backup"></a>Pełna kopia zapasowa

Kopia zapasowa to długotrwała operacja, ale natychmiast zwróci identyfikator zadania. Stan procesu tworzenia kopii zapasowej można sprawdzić przy użyciu tego identyfikatora zadania. Proces tworzenia kopii zapasowej tworzy folder wewnątrz wyznaczonego kontenera o następującym wzorcu nazewnictwa **`mhsm-{HSM_NAME}-{YYYY}{MM}{DD}{HH}{mm}{SS}`** , gdzie HSM_NAME to nazwa zarządzanego modułu HSM, którego kopia zapasowa jest tworzona, a rrrr, mm, DD, hh, mm, mm, SS to rok, miesiąc, Data, godzina, minuty i sekundy daty/godziny w formacie UTC odebrania polecenia tworzenia kopii zapasowej.

Podczas wykonywania kopii zapasowej proces HSM może nie działać przy pełnej przepływności, ponieważ niektóre partycje HSM będą zajęte wykonywanie operacji tworzenia kopii zapasowej.

```azurecli-interactive
# time for 30 minutes later for SAS token expiry

end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Create a container

az storage container create --account-name  mhsmdemobackup --name mhsmdemobackupcontainer  --account-key $skey

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions crdw --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Backup HSM

az keyvault backup start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --subscription 361da5d4-a47a-4c79-afdd-d66f684f4070
```

## <a name="full-restore"></a>Pełne przywracanie

Pełne przywracanie umożliwia całkowite przywrócenie zawartości modułu HSM przy użyciu poprzedniej kopii zapasowej, w tym wszystkich kluczy, wersji, atrybutów, tagów i przypisań ról. Wszystko, co jest obecnie przechowywane w module HSM, zostanie wyczyszczone i powróci do tego samego stanu, który był w momencie utworzenia źródłowej kopii zapasowej.

> [!IMPORTANT]
> Pełne przywracanie to bardzo destrukcyjne i zakłócające działanie. W związku z tym, aby można było wykonać operację, należy wykonać pełną kopię zapasową w ciągu ostatnich 30 minut `restore` .

Przywracanie jest operacją płaszczyzny danych. Obiekt wywołujący, który uruchamia operację przywracania, musi mieć uprawnienia do wykonywania akcji elementu **Microsoft., magazynu/managedHsm/przywracania/uruchamiania/akcji**. Źródłowy moduł HSM, w którym utworzono kopię zapasową, oraz docelowy moduł HSM, w którym zostanie wykonane przywracanie, **musi** mieć tę samą domenę zabezpieczeń. Zobacz więcej na [temat zarządzanej domeny zabezpieczeń modułu HSM](security-domain.md).

Aby wykonać pełne przywracanie, należy podać następujące informacje:
- Nazwa lub adres URL modułu HSM
- Nazwa konta magazynu
- Kontener obiektów BLOB konta magazynu
- Token SAS kontenera magazynu z uprawnieniami `rl`
- Nazwa folderu kontenera magazynu, w którym jest przechowywana źródłowa kopia zapasowa

Przywracanie jest długotrwałą operacją, ale natychmiast zwróci identyfikator zadania. Stan procesu przywracania można sprawdzić przy użyciu tego identyfikatora zadania. Gdy proces przywracania jest w toku, moduł HSM przechodzi w tryb przywracania i wszystkie polecenia płaszczyzny danych (z wyjątkiem sprawdzenia stanu przywracania) są wyłączone.

```azurecli-interactive
#### time for 30 minutes later for SAS token expiry

end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions rl --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)
```

## <a name="backup-hsm"></a>Tworzenie kopii zapasowych modułu HSM

```
az keyvault restore start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-mhsmdemo-2020083120161860
```

## <a name="next-steps"></a>Następne kroki
- Zobacz [Zarządzanie zarządzanym modułem HSM przy użyciu interfejsu wiersza polecenia platformy Azure](key-management.md).
- Dowiedz się więcej na temat [zarządzanej domeny zabezpieczeń modułu HSM](security-domain.md)