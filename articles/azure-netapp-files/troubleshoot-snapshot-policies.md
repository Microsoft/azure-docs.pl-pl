---
title: Rozwiązywanie problemów z zasadami migawek dla Azure NetApp Files | Microsoft Docs
description: Opisuje komunikaty o błędach i rozwiązania, które mogą pomóc w rozwiązywaniu problemów z zarządzaniem zasadami migawek dla Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 6ba8b18876bdae2754a6a772ce3909ff2f5a71b7
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651008"
---
# <a name="troubleshoot-snapshot-policies"></a>Rozwiązywanie problemów z zasadami migawek

W tym artykule opisano scenariusze błędów, które mogą wystąpić podczas zarządzania zasadami migawek Azure NetApp Files. Zawiera również rozwiązania, które mogą pomóc w rozwiązywaniu problemów.

## <a name="error-conditions-and-resolutions"></a>Warunki błędów i rozwiązania 

|     Błąd    |     Rozwiązanie    |
|-|-|
| Tworzenie zasad migawek kończy się niepowodzeniem z nieprawidłową nazwą zasad migawek. | Wystąpił błąd podczas tworzenia zasad migawek, jeśli nazwa zasad migawki jest nieprawidłowa. Następujące wytyczne dotyczą nazw zasad migawek:  <ul><li> Nazwa zasad migawek nie może zawierać znaków innych niż ASCII ani specjalnych. </li> <li> Nazwa zasad migawek musi rozpoczynać się od litery lub cyfry i może zawierać litery, cyfry, znaki podkreślenia ("_") i łączniki ("-"). </li> <li> Nazwa zasad migawek musi mieć długość od 1 do 64 znaków.  </li></ul> Popraw nazwę zasad migawek zgodnie z wytycznymi.  |
| Tworzenie zasad migawek kończy się niepowodzeniem z nieprawidłowymi wartościami. | Azure NetApp Files tworzenia zasad migawek nie powiodło się, jeśli wprowadzisz nieprawidłową wartość dla pola, takiego jak `Number of snapshots to keep` lub `Minute on the hour to take snapshot` . Prawidłowe wartości są następujące:  <ul><li>Wartość musi być prawidłową liczbą.</li> <li>Wartość musi zawierać się w przedziale od 0 do 59.</li></ul> Upewnij się, że dla pól podano prawidłową wartość. | 
| Tworzenie zasad migawek kończy się niepowodzeniem z powodu błędu `Total number of snapshots to keep exceeds 255` . | Każdy wolumin może mieć [maksymalnie 255 migawek](azure-netapp-files-resource-limits.md). Wartość maksymalna obejmuje sumę wszystkich migawek co godzinę, codziennie, co tydzień i co miesiąc. <br> Zmniejsz `Snapshots to keep` wartość i spróbuj ponownie. |
| Przypisywanie zasad do woluminu kończy się niepowodzeniem z powodu błędu `Total snapshot policy is over the max '255'` . | Każdy wolumin może mieć [maksymalnie 255 migawek](azure-netapp-files-resource-limits.md). Gdy suma wszystkich migawek na żądanie, co godzinę, codziennie, co tydzień i miesięcznie przekracza maksymalną wartość, wystąpi błąd. <br> Zmniejsz `snapshots to keep` wartość lub usuń niektóre migawki na żądanie i spróbuj ponownie. | 

## <a name="next-steps"></a>Następne kroki  

* [Zarządzanie zasadami migawek](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
