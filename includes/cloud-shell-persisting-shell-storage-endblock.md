---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183568"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Przenoszenie plików lokalnych do Cloud Shell
`clouddrive`Katalog jest synchronizowany z blokiem magazynu Azure Portal. Ten blok służy do transferowania plików lokalnych do lub z udziału plików. Aktualizowanie plików z poziomu Cloud Shell jest odzwierciedlane w graficznym interfejsie użytkownika magazynu plików po odświeżeniu bloku.

### <a name="download-files"></a>Pobieranie plików

![Lista plików lokalnych](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. W Azure Portal przejdź do zainstalowanego udziału plików.
2. Wybierz plik docelowy.
3. Wybierz przycisk **Pobierz** .

### <a name="upload-files"></a>Przekazywanie plików

![Pliki lokalne do przekazania](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Przejdź do zainstalowanego udziału plików.
2. Wybierz przycisk **Przekaż**.
3. Wybierz plik lub pliki, które chcesz przekazać.
4. Potwierdź przekazywanie.

Pliki, które są dostępne w katalogu, powinny być teraz widoczne `clouddrive` w Cloud Shell.