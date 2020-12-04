---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: db4bc51b937535b7b53273bf01057d9b6c5198b3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96584565"
---
W wersji zapoznawczej system plików NFS ma następujące ograniczenia:

- System plików NFS 4,1 obecnie obsługuje tylko funkcje obowiązkowe ze [specyfikacji protokołu](https://tools.ietf.org/html/rfc5661). Funkcje opcjonalne, takie jak delegowanie i wywołania zwrotne wszelkiego rodzaju, uaktualnienia blokad i obniżania poziomu oraz uwierzytelnianie i szyfrowanie Kerberos nie są obsługiwane.
- Jeśli większość żądań jest skoncentrowana na metadanych, opóźnienie będzie gorszyć w porównaniu z operacjami odczytu/zapisu/aktualizacji.
- Aby można było utworzyć udział NFS, należy utworzyć nowe konto magazynu.
- Obsługiwane są tylko interfejsy API REST płaszczyzny zarządzania. Interfejsy API REST płaszczyzny danych są niedostępne, co oznacza, że narzędzia takie jak Eksplorator usługi Storage nie będą działać z udziałami NFS ani nie będą mogły przeglądać danych udziału NFS w Azure Portal.
- AzCopy nie jest obecnie obsługiwana.
- Dostępne tylko dla warstwy Premium.
- Obecnie dostępne tylko w magazynie lokalnie nadmiarowy (LRS).

### <a name="azure-storage-features-not-yet-supported"></a>Funkcje usługi Azure Storage nie są jeszcze obsługiwane

Ponadto następujące funkcje Azure Files nie są dostępne w udziałach NFS:

- Uwierzytelnianie oparte na tożsamościach
- Obsługa Azure Backup
- Migawki
- Usuwanie nietrwałe
- Pełna obsługa szyfrowania w trakcie przesyłania (szczegółowe informacje znajdują się w temacie [zabezpieczenia NFS](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (dostępne tylko dla klientów z systemem Windows, które nie obsługują systemu plików NFS 4,1)
