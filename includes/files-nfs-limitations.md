---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6e819e1078ac90ef16070702e7961122b06c1d6f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107879403"
---
W wersji zapoznawczej NFS ma następujące ograniczenia:

- System plików NFS 4.1 obecnie obsługuje tylko większość funkcji ze [specyfikacji protokołu](https://tools.ietf.org/html/rfc5661). Niektóre funkcje, takie jak delegowanie i wywołania zwrotne wszelkiego rodzaju, uaktualnienia i obniżanie poziomu blokady, uwierzytelnianie Kerberos i szyfrowanie, nie są obsługiwane.
- Jeśli większość żądań jest zorientowana na metadane, opóźnienie będzie mniejsze w porównaniu z operacjami odczytu/zapisu/aktualizacji.
- Udziały NFS można włączyć/utworzyć tylko dla nowych kont magazynu, a nie istniejących
- Obsługiwane są tylko interfejsy API REST płaszczyzny zarządzania. Interfejsy API REST płaszczyzny danych nie są dostępne, co oznacza, że narzędzia takie jak Eksplorator usługi Storage nie będą działać z udziałami NFS ani nie będzie można przeglądać danych udziałów NFS w sieci Azure Portal.
- Program AzCopy nie jest obecnie obsługiwany.
- Dostępne tylko dla warstwy Premium.
- Udziały NFS akceptują tylko numeryczne identyfikatory UID/GID. Aby uniknąć wysyłania alfanumerycznych identyfikatorów UID/GID przez klientów, należy wyłączyć mapowanie identyfikatorów.
- Udziały można zainstalowane tylko z jednego konta magazynu na poszczególnych maszynach wirtualnych, korzystając z linków prywatnych. Próba instalacji udziałów z innych kont magazynu nie powiedzie się.
- Najlepiej jest polegać na uprawnieniach przypisanych do grupy podstawowej. Czasami uprawnienia przydzielone do grupy nie podstawowej użytkownika mogą spowodować odmowę dostępu z powodu znanej usterki.

### <a name="azure-storage-features-not-yet-supported"></a>Funkcje usługi Azure Storage nie są jeszcze obsługiwane

Ponadto następujące funkcje Azure Files nie są dostępne w udziałach NFS:

- Uwierzytelnianie oparte na tożsamościach
- Azure Backup pomocy technicznej
- Migawki
- Usuwanie nietrwałe
- Pełna obsługa szyfrowania podczas przesyłania (aby uzyskać szczegółowe informacje, zobacz [Zabezpieczenia systemu plików NFS)](../articles/storage/files/storage-files-compare-protocols.md#security)
- Azure File Sync (dostępne tylko dla klientów z systemem Windows, który nie obsługuje systemu plików NFS 4.1)
