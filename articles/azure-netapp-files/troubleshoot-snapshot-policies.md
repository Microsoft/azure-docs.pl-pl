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
ms.openlocfilehash: 1d7a91de8fa505fe4c2b06eea59f3acc2ae1f7e8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91345496"
---
# <a name="troubleshoot-snapshot-policies"></a>Rozwiązywanie problemów z zasadami migawek

W tym artykule opisano scenariusze błędów, które mogą wystąpić podczas zarządzania zasadami migawek Azure NetApp Files. Zawiera również rozwiązania, które mogą pomóc w rozwiązywaniu problemów.

## <a name="snapshot-policy-creation-failing-with-invalid-snapshot-policy-name"></a>Tworzenie zasad migawek kończy się niepowodzeniem z nieprawidłową nazwą zasad migawek

Wystąpił błąd podczas tworzenia zasad migawek, jeśli nazwa zasad migawki jest nieprawidłowa. Następujące wytyczne dotyczą nazw zasad migawek:  

* Nazwa zasad migawek nie może zawierać znaków innych niż ASCII ani specjalnych.
* Nazwa zasad migawek musi rozpoczynać się od litery lub cyfry i może zawierać litery, cyfry, znaki podkreślenia ("_") i łączniki ("-").
* Nazwa zasad migawek musi mieć długość od 1 do 64 znaków.  

Należy skorygować nazwę zasad migawek zgodnie z wytycznymi.  

## <a name="snapshot-policy-creation-failing-with-invalid-values"></a>Tworzenie zasad migawek kończy się niepowodzeniem z nieprawidłowymi wartościami 

Azure NetApp Files tworzenia zasad migawek nie powiodło się, jeśli wprowadzisz nieprawidłową wartość dla pola, takiego jak `Number of snapshots to keep` lub `Minute on the hour to take snapshot` .  
 
Prawidłowe wartości są następujące:   

* Wartość musi być prawidłową liczbą.
* Wartość musi zawierać się w przedziale od 0 do 59.

Upewnij się, że dla pól podano prawidłową wartość.

## <a name="snapshot-policy-creation-failing-with-total-number-of-snapshots-to-keep-exceeds-255-error"></a>Tworzenie zasad migawek kończy się niepowodzeniem z powodu błędu "Łączna liczba migawek do zachowania przekracza 255" 

Każdy wolumin może mieć [maksymalnie 255 migawek](azure-netapp-files-resource-limits.md). Wartość maksymalna obejmuje sumę wszystkich migawek co godzinę, codziennie, co tydzień i co miesiąc. Należy zmniejszyć `Snapshots to keep` wartość i spróbować ponownie.

## <a name="assigning-policy-to-a-volume-failing-with-total-snapshot-policy-is-over-the-max-255-error"></a>Przypisywanie zasad do woluminu kończy się niepowodzeniem z powodu błędu "Łączna liczba zasad migawek przekracza maksymalną wartość" 255 "

Każdy wolumin może mieć [maksymalnie 255 migawek](azure-netapp-files-resource-limits.md). Gdy suma wszystkich migawek na żądanie, co godzinę, codziennie, co tydzień i miesięcznie przekracza maksymalną wartość, wystąpi błąd. Zmniejsz `snapshots to keep` wartość lub usuń niektóre migawki na żądanie i spróbuj ponownie.

## <a name="next-steps"></a>Następne kroki  

* [Zarządzanie zasadami migawek](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
