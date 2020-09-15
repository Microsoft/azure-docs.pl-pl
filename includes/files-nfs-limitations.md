---
title: dołączanie pliku
description: dołączanie pliku
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7e009516721473554517d1677bdef7a7451e4007
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564942"
---
W wersji zapoznawczej system plików NFS ma następujące ograniczenia:

- System plików NFS 4,1 obecnie obsługuje tylko funkcje obowiązkowe ze [specyfikacji protokołu](https://tools.ietf.org/html/rfc5661). Funkcje opcjonalne, takie jak delegowanie i wywołania zwrotne wszelkiego rodzaju, uaktualnienia blokad i obniżania poziomu oraz uwierzytelnianie i szyfrowanie Kerberos nie są obsługiwane.
- Jeśli większość żądań jest skoncentrowana na metadanych, opóźnienie będzie gorszyć w porównaniu z operacjami otwierania/zamykania.
- Aby można było utworzyć udział NFS, należy utworzyć nowe konto magazynu.
- Obsługiwane są tylko interfejsy API REST płaszczyzny zarządzania. Interfejsy API REST płaszczyzny danych nie są dostępne, co oznacza, że narzędzia takie jak Eksplorator magazynu nie będą działać z udziałami NFS ani nie będą mogły przeglądać danych udziału NFS w Azure Portal.
- Dostępne tylko dla warstwy Premium.
- Obecnie dostępne tylko w przypadku magazynu lokalnego nadmiarowego.

### <a name="azure-storage-features-not-yet-supported"></a>Funkcje usługi Azure Storage nie są jeszcze obsługiwane

Ponadto następujące funkcje Azure Files nie są dostępne w udziałach NFS:

- Uwierzytelnianie oparte na tożsamościach
- Obsługa Azure Backup
- Migawki
- Usuwanie nietrwałe
- Pełna obsługa szyfrowania w trakcie przesyłania (szczegółowe informacje znajdują się w temacie [zabezpieczenia NFS](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (dostępne tylko dla klientów z systemem Windows, które nie obsługują systemu plików NFS 4,1)