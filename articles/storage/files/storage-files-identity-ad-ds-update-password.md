---
title: Aktualizowanie hasła konta magazynu AD DS
description: Dowiedz się, jak zaktualizować hasło do konta Active Directory Domain Services, które reprezentuje Twoje konto magazynu. Zapobiega to wyczyszczeniu konta magazynu po wygaśnięciu hasła, co uniemożliwia niepowodzenie uwierzytelniania.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 53dcc69b6e9ae94846330077f07ae57af1b0b414
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85214387"
---
# <a name="update-the-password-of-your-storage-account-identity-in-ad-ds"></a>Zaktualizuj hasło tożsamości konta magazynu w AD DS

Jeśli zarejestrowano tożsamość/konto Active Directory Domain Services (AD DS), które reprezentuje konto magazynu w jednostce organizacyjnej lub domenie, która wymusza czas wygaśnięcia hasła, należy zmienić hasło przed maksymalnym okresem ważności hasła. Organizacja może uruchamiać automatyczne czyszczenie skryptów, które usuwają konta po wygaśnięciu hasła. W związku z tym, jeśli nie zmienisz hasła przed jego wygaśnięciem, konto może zostać usunięte, co spowoduje utratę dostępu do udziałów plików platformy Azure.

Aby wyzwolić rotację haseł, można uruchomić `Update-AzStorageAccountADObjectPassword` polecenie z [modułu AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases). To polecenie musi być uruchamiane w lokalnym środowisku przyłączonym AD DS przy użyciu użytkownika hybrydowego z uprawnieniami właściciela do konta magazynu i uprawnień AD DS, aby zmienić hasło tożsamości reprezentującej konto magazynu. Polecenie wykonuje działania podobne do rotacji kluczy konta magazynu. W tym celu pobiera drugi klucz Kerberos konta magazynu i używa go do aktualizowania hasła zarejestrowanego konta w AD DS. Następnie generuje ponownie docelowy klucz Kerberos konta magazynu i aktualizuje hasło zarejestrowanego konta w AD DS. To polecenie należy uruchomić w środowisku przyłączonym do lokalnego AD DS.

```PowerShell
# Update the password of the AD DS account registered for the storage account
# You may use either kerb1 or kerb2
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```
