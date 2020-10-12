---
title: Konfigurowanie Azure File Sync
description: Skonfiguruj Azure File Sync. Wspólny blok tekstu współużytkowany w dokumentach migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 562099c65273cad50596268b89055ec78d92d18a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82143627"
---
Ten krok polega na połączeniu wszystkich zasobów i folderów skonfigurowanych w wystąpieniu systemu Windows Server podczas poprzednich kroków.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Znajdź zasób usługi synchronizacji magazynu.
1. Utwórz nową *grupę synchronizacji* w ramach zasobu usługi synchronizacji magazynu dla każdego udziału plików platformy Azure. Zgodnie z terminologią Azure File Sync udział plików platformy Azure stanie się *punktem końcowym w chmurze* w topologii synchronizacji, które opisano w ramach tworzenia grupy synchronizacji. Podczas tworzenia grupy synchronizacji nadaj jej znaną nazwę, aby rozpoznać, który zestaw plików jest synchronizowany w tym miejscu. Upewnij się, że Przywołujesz udział plików platformy Azure o pasującej nazwie.
1. Po utworzeniu grupy synchronizacji zostanie wyświetlony wiersz na liście grup synchronizacji. Wybierz nazwę (łącze), aby wyświetlić zawartość grupy synchronizacji. W obszarze **punkty końcowe chmury**zobaczysz udział plików platformy Azure.
1. Znajdź przycisk **+ Dodaj punkt końcowy serwera** . Folder na serwerze lokalnym, który został zainicjowany, stanie się ścieżką dla tego *punktu końcowego serwera*.
