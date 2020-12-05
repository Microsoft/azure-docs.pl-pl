---
title: plik dołączania
description: plik dołączania
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 372342611265640a2a64100f003880a430d61ca0
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620951"
---
W wersji zapoznawczej system plików NFS ma następujące ograniczenia:

- System plików NFS 4,1 obsługuje obecnie tylko większość funkcji ze [specyfikacji protokołu](https://tools.ietf.org/html/rfc5661). Niektóre funkcje, takie jak delegowanie i wywołania zwrotne wszystkich rodzajów, uaktualnienia blokad i obniżania poziomu, uwierzytelnianie Kerberos i szyfrowanie nie są obsługiwane.
- Jeśli większość żądań jest skoncentrowana na metadanych, opóźnienie będzie gorszyć w porównaniu z operacjami odczytu/zapisu/aktualizacji.
- Aby można było utworzyć udział NFS, należy utworzyć nowe konto magazynu.
- Obsługiwane są tylko interfejsy API REST płaszczyzny zarządzania. Interfejsy API REST płaszczyzny danych są niedostępne, co oznacza, że narzędzia takie jak Eksplorator usługi Storage nie będą działać z udziałami NFS ani nie będą mogły przeglądać danych udziału NFS w Azure Portal.
- AzCopy nie jest obecnie obsługiwana.
- Dostępne tylko dla warstwy Premium.
- Udziały NFS akceptują tylko numeryczne UID/GID. Aby uniknąć wysyłania alfanumerycznych identyfikatorów UID/GID przez klientów, należy wyłączyć mapowanie identyfikatorów.
- Udziały można instalować tylko z jednego konta magazynu na poszczególnych maszynach wirtualnych, korzystając z linków prywatnych. Próba zainstalowania udziałów z innych kont magazynu zakończy się niepowodzeniem.

### <a name="azure-storage-features-not-yet-supported"></a>Funkcje usługi Azure Storage nie są jeszcze obsługiwane

Ponadto następujące funkcje Azure Files nie są dostępne w udziałach NFS:

- Uwierzytelnianie oparte na tożsamościach
- Obsługa Azure Backup
- Migawki
- Usuwanie nietrwałe
- Pełna obsługa szyfrowania w trakcie przesyłania (szczegółowe informacje znajdują się w temacie [zabezpieczenia NFS](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (dostępne tylko dla klientów z systemem Windows, które nie obsługują systemu plików NFS 4,1)
