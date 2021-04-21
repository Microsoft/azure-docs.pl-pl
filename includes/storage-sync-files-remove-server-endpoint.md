---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2f50a1d046b7b7d3fd7507ef87f8f29096a4faa4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774572"
---
Nie: usunięcie punktu końcowego serwera nie przypomina ponownego uruchamiania serwera. Usuwanie i ponowne tworzenie punktu końcowego serwera prawie nigdy nie jest odpowiednim rozwiązaniem problemów z synchronizacją, warstwami w chmurze lub innymi aspektami Azure File Sync. Usuwanie punktu końcowego serwera jest operacją destruktywną. Może to spowodować utratę danych w przypadku, gdy pliki warstwowe istnieją poza przestrzenią nazw punktu końcowego serwera. Aby uzyskać więcej informacji, zobacz Dlaczego pliki warstwowe istnieją [poza przestrzenią nazw punktu](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) końcowego serwera. Może to też spowodować niedostępność plików dla plików warstwowych, które istnieją w przestrzeni nazw punktu końcowego serwera. Te problemy nie rozwiążą problemu podczas ponownego tworzenia punktu końcowego serwera. Pliki warstwowe mogą istnieć w przestrzeni nazw punktu końcowego serwera, nawet jeśli nigdy nie włączono obsługi warstw w chmurze. Dlatego zaleca się, aby nie usuwać punktu końcowego serwera, chyba że chcesz przestać używać programu Azure File Sync z tym konkretnym folderem lub jeśli inżynier firmy Microsoft jawnie tego nie zaleci. Aby uzyskać więcej informacji na temat usuwania punktów końcowych serwera, zobacz [Usuwanie punktu końcowego serwera](../articles/storage/file-sync/file-sync-server-endpoint.md#remove-a-server-endpoint).    
