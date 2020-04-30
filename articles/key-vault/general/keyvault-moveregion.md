---
title: Azure Key Vault przeniesienie magazynu do innego regionu | Microsoft Docs
description: Wskazówki dotyczące przeniesienia magazynu kluczy do innego regionu.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: e65a723ac9daafdc09896a50e197034104408df2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254150"
---
# <a name="moving-an-azure-key-vault-across-regions"></a>Przesuwanie Azure Key Vault w różnych regionach

## <a name="overview"></a>Omówienie

Key Vault nie obsługuje operacji przenoszenia zasobów, która umożliwia przenoszenie magazynu kluczy do innego regionu. W tym artykule opisano obejścia, jeśli istnieje potrzeba przeniesienia magazynu kluczy do innego regionu. Każda opcja ma ograniczenia i ma kluczowe znaczenie dla zrozumienia implikacji tych obejść przed podjęciem próby ich w środowisku produkcyjnym.

Jeśli potrzebujesz przenieść magazyn kluczy do innego regionu, rozwiązanie to utworzenie nowego magazynu kluczy w żądanym regionie i ręczne skopiowanie poszczególnych wpisów tajnych z istniejącego magazynu kluczy do nowego magazynu kluczy. Tę operację można wykonać przy użyciu jednego z następujących sposobów wymienionych poniżej.

## <a name="design-considerations"></a>Zagadnienia projektowe

* Nazwy Key Vault są unikatowe globalnie. Nie będziesz w stanie ponownie używać tej samej nazwy magazynu.

* Konieczne będzie ponowne skonfigurowanie zasad dostępu i ustawień konfiguracji sieci w nowym magazynie kluczy.

* Należy ponownie skonfigurować ochronę nietrwałego usuwania i przeczyszczania w nowym magazynie kluczy.

* Operacja wykonywania kopii zapasowej i przywracania nie będzie zachować ustawień autorotacji, może być konieczne ponowne skonfigurowanie tych ustawień.

## <a name="option-1---use-the-key-vault-backup-and-restore-commands"></a>Opcja 1 — Używanie poleceń tworzenia kopii zapasowej i przywracania magazynu kluczy

Za pomocą polecenia Backup można utworzyć kopię zapasową poszczególnych wpisów tajnych, kluczy i certyfikatów w magazynie. Wpisy tajne zostaną pobrane jako zaszyfrowane obiekty blob. Następnie można przywrócić obiekt BLOB do nowego magazynu kluczy. Polecenia są udokumentowane w poniższym łączu.

[Polecenia Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault)

### <a name="limitations"></a>Ograniczenia

* Nie można utworzyć kopii zapasowej magazynu kluczy w jednej lokalizacji geograficznej i przywrócić go do innej lokalizacji geograficznej. Dowiedz się więcej o usłudze Azure lokalizacje geograficzne. [Łącze](https://azure.microsoft.com/global-infrastructure/geographies/)

* Polecenie Backup tworzy kopię zapasową wszystkich wersji każdego klucza tajnego. Jeśli masz klucz tajny z dużą liczbą poprzednich wersji (więcej niż 10), żądanie przekroczy maksymalny dozwolony rozmiar żądania, a operacja może zakończyć się niepowodzeniem.

## <a name="option-2---manually-download-and-upload-secrets"></a>Opcja 2 — ręczne pobieranie i przekazywanie wpisów tajnych

Niektóre typy tajne można pobrać ręcznie. Można na przykład pobrać certyfikaty jako plik PFX. Ta opcja eliminuje ograniczenia geograficzne dla niektórych typów tajnych, takich jak certyfikaty. Pliki. pfx można przekazać do dowolnego magazynu kluczy w dowolnym regionie. Wpis tajny zostanie pobrany w formacie chronionym bez hasła. Użytkownik jest odpowiedzialny za zabezpieczenie swoich wpisów tajnych, gdy opuszczają Key Vault podczas przenoszenia.
