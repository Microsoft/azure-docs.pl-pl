---
title: Konfigurowanie Azure File Sync
description: Skonfiguruj Azure File Sync. Wspólny blok tekstu współużytkowany w dokumentach migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 64b99976a306c3c8423f5115c95a15158a3ddb51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93043139"
---
Ten krok polega na połączeniu wszystkich zasobów i folderów skonfigurowanych w wystąpieniu systemu Windows Server podczas poprzednich kroków.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Znajdź zasób usługi synchronizacji magazynu.
1. Utwórz nową *grupę synchronizacji* w ramach zasobu usługi synchronizacji magazynu dla każdego udziału plików platformy Azure. Zgodnie z terminologią Azure File Sync udział plików platformy Azure stanie się *punktem końcowym w chmurze* w topologii synchronizacji, które opisano w ramach tworzenia grupy synchronizacji. Podczas tworzenia grupy synchronizacji nadaj jej znaną nazwę, aby rozpoznać, który zestaw plików jest synchronizowany w tym miejscu. Upewnij się, że Przywołujesz udział plików platformy Azure o pasującej nazwie.
1. Po utworzeniu grupy synchronizacji zostanie wyświetlony wiersz na liście grup synchronizacji. Wybierz nazwę (łącze), aby wyświetlić zawartość grupy synchronizacji. W obszarze **punkty końcowe chmury** zobaczysz udział plików platformy Azure.
1. Znajdź przycisk **+ Dodaj punkt końcowy serwera** . Folder na serwerze lokalnym, który został zainicjowany, stanie się ścieżką dla tego *punktu końcowego serwera*.
