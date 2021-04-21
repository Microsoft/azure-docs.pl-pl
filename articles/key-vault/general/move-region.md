---
title: Przenoszenie magazynu kluczy do innego regionu — Azure Key Vault | Microsoft Docs
description: Ten artykuł zawiera wskazówki dotyczące przenoszenia magazynu kluczy do innego regionu.
services: key-vault
author: msmbaldwin
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: ac2f6347776c2f5d230065b80b1c0336e21e181c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751800"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Przenoszenie magazynu kluczy platformy Azure między regionami

Azure Key Vault nie obsługuje operacji przenoszenia zasobów, która zezwala na przenoszenie magazynu kluczy z jednego regionu do innego. W tym artykule o mowa o obejściach dla organizacji, które mają potrzebę przenoszenia magazynu kluczy do innego regionu. Każda opcja obejścia ma ograniczenia. Zanim spróbujemy zastosować je w środowisku produkcyjnym, należy zrozumieć implikacje tych obejść.

Aby przenieść magazyn kluczy do innego regionu, należy utworzyć magazyn kluczy w tym drugim regionie, a następnie ręcznie skopiować każdy pojedynczy klucz tajny z istniejącego magazynu kluczy do nowego magazynu kluczy. Można to zrobić przy użyciu jednej z następujących dwóch opcji.

## <a name="design-considerations"></a>Zagadnienia dotyczące projektowania

Przed rozpoczęciem należy pamiętać o następujących pojęciach:

* Nazwy magazynów kluczy są globalnie unikatowe. Nie można ponownie użyć nazwy magazynu.
* Należy ponownie skonfigurować zasady dostępu i ustawienia konfiguracji sieci w nowym magazynie kluczy.
* Należy ponownie skonfigurować usuwanie nie soft-delete i ochronę przed przeczyszczaniem w nowym magazynie kluczy.
* Operacja tworzenia kopii zapasowej i przywracania nie zachowuje ustawień autorotacji. Może być konieczne ponowne skonfigurowanie ustawień.

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>Opcja 1. Używanie poleceń tworzenia kopii zapasowej i przywracania magazynu kluczy

Kopię zapasową każdego klucza tajnego, klucza i certyfikatu w magazynie można utworzyć za pomocą polecenia backup. Wpisy tajne są pobierane jako zaszyfrowane obiekty blob. Następnie możesz przywrócić obiekt blob do nowego magazynu kluczy. Aby uzyskać listę poleceń, zobacz [Azure Key Vault polecenia](/powershell/module/azurerm.keyvault#key_vault).

Korzystanie z poleceń tworzenia kopii zapasowej i przywracania ma dwa ograniczenia:

* Nie można utworzyć kopii zapasowej magazynu kluczy w jednej lokalizacji geograficznej i przywrócić go do innej lokalizacji geograficznej. Aby uzyskać więcej informacji, zobacz [Lokalizacje geograficzne platformy Azure.](https://azure.microsoft.com/global-infrastructure/geographies/)

* Polecenie kopii zapasowej tworzy kopię zapasową wszystkich wersji każdego wpisu tajnego. Jeśli masz wpis tajny o dużej liczbie poprzednich wersji (więcej niż 10), żądany rozmiar może przekroczyć dozwoloną wartość maksymalną i operacja może zakończyć się niepowodzeniem.

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>Opcja 2. Ręczne pobieranie i przekazywanie wpisów tajnych magazynu kluczy

Niektóre typy tajnych można pobrać ręcznie. Możesz na przykład pobrać certyfikaty jako plik PFX. Ta opcja eliminuje ograniczenia geograficzne dla niektórych typów tajnych, takich jak certyfikaty. Pliki PFX można przekazać do dowolnego magazynu kluczy w dowolnym regionie. Wpisy tajne są pobierane w formacie innym niż chroniony hasłem. Użytkownik jest odpowiedzialny za zabezpieczanie wpisów tajnych podczas przenoszenia.