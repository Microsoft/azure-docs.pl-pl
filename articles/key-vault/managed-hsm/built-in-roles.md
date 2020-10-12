---
title: Wbudowane role RBAC lokalnego modułu HSM — Azure Key Vault | Microsoft Docs
description: Omówienie zarządzanych wbudowanych ról modułu HSM, które mogą być przypisane do użytkowników, podmiotów usługi, grup i tożsamości zarządzanych
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 6b39c1d6fe2e84055e7c94dde076514d04a24f36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91000766"
---
# <a name="managed-hsm-local-rbac-built-in-roles"></a>Zarządzane role lokalnych RBAC modułu HSM

Lokalna RBAC modułu HSM ma kilka wbudowanych ról, które można przypisać do użytkowników, podmiotów usługi, grup i zarządzanych tożsamości. Aby zezwolić podmiotowi zabezpieczeń na wykonanie operacji, należy przypisać im rolę, która przyznaje im uprawnienia do wykonywania tych operacji. Wszystkie te role i operacje umożliwiają tylko Zarządzanie uprawnieniami dla operacji na płaszczyźnie danych. Aby zarządzać uprawnieniami płaszczyzny kontroli dla zarządzanego zasobu modułu HSM (takich jak tworzenie nowego zarządzanego modułu HSM lub aktualizowanie, przenoszenie, Usuwanie istniejącego), należy użyć [kontroli dostępu opartej na rolach (RBAC) platformy Azure](../../role-based-access-control/overview.md).

## <a name="built-in-roles"></a>Wbudowane role

|Nazwa roli|Opis|ID (Identyfikator)|
|---|---|---|
|Zarządzany administrator modułu HSM| Przyznaje pełen dostęp do wszystkich akcji danych.|a290e904-7015-4bba-90c8-60543313cdb4|
|Zarządzany oficer kryptograficzny modułu HSM| Przyznaje pełen dostęp do wszystkich operacji zarządzania kluczami i klucza kryptograficznego|515eb02d-2335-4d2d-92f2-b1cbdf9c3778|
|Zarządzany użytkownik kryptograficzny modułu HSM|Przyznaje dostęp do tworzenia i używania kluczy dla operacji kryptograficznych. Nie można trwale usunąć kluczy.|21dbd100-6940-42c2-9190-5d6cb909625b|
|Administrator zarządzanych zasad modułu HSM| Przyznaje uprawnienia do tworzenia i usuwania przypisań ról|4bd23610-CDCF-4971-bdee-bdc562cc28e4|
|Zarządzany audytor kryptograficzny modułu HSM|Przyznaje uprawnienia do odczytu do odczytu (ale nie używania) kluczy|2c18b078-7c48-4d3a-af88-5a3a1b3f82b3|
|Szyfrowanie zarządzanej usługi kryptograficznej modułu HSM| Przyznaje uprawnienia do używania klucza do szyfrowania usług. |33413926-3206-4cdd-b39a-83574fe37a17|
|Zarządzana kopia zapasowa modułu HSM| Przyznaje uprawnienia do wykonywania kopii zapasowych pojedynczego klucza lub całego modułu HSM. |7b127d3c-77bd-4e3e-bbe0-dbb8971fa7f8|

## <a name="permitted-operations"></a>Dozwolone operacje
> [!NOTE]  
> - Symbol "X" wskazuje, że rola może wykonać akcję danych. Pusta komórka wskazuje, że rola nie ma pemission do wykonania tej akcji danych.
> - Wszystkie nazwy akcji danych mają prefiks "Microsoft. managedHsm", który został pominięty w tabelach poniżej dla zwięzłości.
> - Wszystkie nazwy ról mają prefiks "zarządzany moduł HSM", który został pominięty w poniższej tabeli dla zwięzłości.

|Akcja danych | Administrator | Oficer kryptograficzny | Użytkownik kryptograficzny | Administrator zasad | Szyfrowanie usługi kryptograficznej | Backup | Audytor kryptograficzny|
|---|---|---|---|---|---|---|---|
|**Zarządzanie domeną zabezpieczeń**|
/securitydomain/download/action|<center>Y</center>||||||
/securitydomain/upload/action|<center>Y</center>||||||
/securitydomain/upload/read|<center>Y</center>||||||
/securitydomain/transferkey/read|<center>Y</center>||||||
|**Zarządzanie kluczami**|
|/keys/read/action|<center>Y</center>|<center>Y</center>|<center>Y</center>||<center>Y</center>||<center>Y</center>|
|/keys/write/action|<center>Y</center>|<center>Y</center>|<center>Y</center>||||
|/keys/create|<center>Y</center>|<center>Y</center>|<center>Y</center>||||
|/keys/delete|<center>Y</center>|<center>Y</center>|||||
|/keys/deletedKeys/read/action|<center>Y</center>|<center>Y</center>|||||
|/keys/deletedKeys/recover/action|<center>Y</center>|<center>Y</center>|||||
|/keys/deletedKeys/delete|<center>Y</center>|<center>Y</center>|||||<center>Y</center>|
|/keys/backup/action|<center>Y</center>|<center>Y</center>|<center>Y</center>|||<center>Y</center>|
|/keys/restore/action|<center>Y</center>|<center>Y</center>|||||
|/keys/export/action|<center>Y</center>|<center>Y</center>|||||
|/keys/import/action|<center>Y</center>|<center>Y</center>|||||
|**Operacje kryptograficzne kluczy**|
|/keys/encrypt/action|<center>Y</center>|<center>Y</center>|<center>Y</center>||||
|/keys/decrypt/action|<center>Y</center>|<center>Y</center>|<center>Y</center>||||
|/keys/wrap/action|<center>Y</center>|<center>Y</center>|<center>Y</center>||<center>Y</center>||
|/keys/unwrap/action|<center>Y</center>|<center>Y</center>|<center>Y</center>||<center>Y</center>||
|/keys/sign/action|<center>Y</center>|<center>Y</center>|<center>Y</center>||||
|/keys/verify/action|<center>Y</center>|<center>Y</center>|<center>Y</center>||||
|**Zarządzanie rolami**|
|/roleAssignments/delete/action|<center>Y</center>|||<center>Y</center>|||
|/roleAssignments/read/action|<center>Y</center>|||<center>Y</center>|||
|/roleAssignments/write/action|<center>Y</center>|||<center>Y</center>|||
|/roleDefinitions/read/action|<center>Y</center>|||<center>Y</center>|||
|**Zarządzanie kopiami zapasowymi/przywracaniem**|
|/backup/start/action|<center>Y</center>|||||<center>Y</center>|
|/backup/status/action|<center>Y</center>|||||<center>Y</center>|
|/restore/start/action|<center>Y</center>||||||
|/restore/status/action|<center>X</center>||||||
||||||||

## <a name="next-steps"></a>Następne kroki

- Zobacz Omówienie [kontroli dostępu opartej na rolach (RBAC) na platformie Azure](../../role-based-access-control/overview.md).
- Zobacz Samouczek dotyczący [zarządzanego zarządzania rolami modułu HSM](role-management.md)
