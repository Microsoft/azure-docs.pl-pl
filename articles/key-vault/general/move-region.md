---
title: Przenoszenie magazynu kluczy do innego regionu — Azure Key Vault | Microsoft Docs
description: Ten artykuł zawiera wskazówki dotyczące przeniesienia magazynu kluczy do innego regionu.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: 7d58cd26b6e4ca77da98f8c2f82dbdb481ccbb50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88585756"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Przenoszenie magazynu kluczy platformy Azure między regionami

Azure Key Vault nie obsługuje operacji przenoszenia zasobów, która umożliwia przenoszenie magazynu kluczy z jednego regionu do innego. W tym artykule opisano obejścia dla organizacji, które wymagają przeniesienia magazynu kluczy do innego regionu. Każda opcja obejścia ma ograniczenia. Przed podjęciem próby zastosowania ich w środowisku produkcyjnym najważniejsze jest zrozumienie implikacji tych obejść.

Aby przenieść magazyn kluczy do innego regionu, należy utworzyć magazyn kluczy w tym innym regionie, a następnie ręcznie skopiować każde pojedyncze hasło z istniejącego magazynu kluczy do nowego magazynu kluczy. Można to zrobić przy użyciu jednej z następujących dwóch opcji.

## <a name="design-considerations"></a>Zagadnienia dotyczące projektowania

Przed rozpoczęciem należy pamiętać o następujących pojęciach:

* Nazwy magazynów kluczy są unikatowe globalnie. Nie można ponownie użyć nazwy magazynu.
* Należy ponownie skonfigurować zasady dostępu i ustawienia konfiguracji sieci w nowym magazynie kluczy.
* Należy ponownie skonfigurować ochronę nietrwałego usuwania i przeczyszczania w nowym magazynie kluczy.
* Operacja tworzenia kopii zapasowej i przywracania nie zachowuje ustawień autorotacji. Może być konieczne ponowne skonfigurowanie ustawień.

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>Opcja 1: użycie poleceń tworzenia kopii zapasowej i przywracania magazynu kluczy

Można utworzyć kopię zapasową poszczególnych wpisów tajnych, kluczy i certyfikatów w magazynie przy użyciu polecenia Backup. Wpisy tajne są pobierane jako zaszyfrowane obiekty blob. Następnie można przywrócić obiekt BLOB do nowego magazynu kluczy. Aby zapoznać się z listą poleceń, zobacz [Azure Key Vault polecenia](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault).

Użycie poleceń tworzenia kopii zapasowej i przywracania ma dwa ograniczenia:

* Nie można utworzyć kopii zapasowej magazynu kluczy w jednej lokalizacji geograficznej i przywrócić go do innej lokalizacji geograficznej. Aby uzyskać więcej informacji, zobacz [Azure lokalizacje geograficzne](https://azure.microsoft.com/global-infrastructure/geographies/).

* Polecenie kopii zapasowej tworzy kopię zapasową wszystkich wersji każdego wpisu tajnego. Jeśli masz wpis tajny o dużej liczbie poprzednich wersji (więcej niż 10), żądany rozmiar może przekroczyć dozwoloną wartość maksymalną i operacja może zakończyć się niepowodzeniem.

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>Opcja 2: ręczne pobieranie i przekazywanie wpisów tajnych magazynu kluczy

Niektóre typy tajne można pobrać ręcznie. Można na przykład pobrać certyfikaty jako plik PFX. Ta opcja eliminuje ograniczenia geograficzne dla niektórych typów tajnych, takich jak certyfikaty. Pliki PFX można przekazać do dowolnego magazynu kluczy w dowolnym regionie. Wpisy tajne są pobierane w formacie chronionym bez hasła. Użytkownik jest odpowiedzialny za zabezpieczenie wpisów tajnych podczas przenoszenia.
