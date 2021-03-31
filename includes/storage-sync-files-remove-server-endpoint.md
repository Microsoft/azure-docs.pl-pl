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
ms.openlocfilehash: f730b2abad244132b06c0fa21f07a171559d22f7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96309952"
---
Nie: Usuwanie punktu końcowego serwera nie jest podobne do ponownego rozruchu serwera. Usunięcie i ponowne utworzenie punktu końcowego serwera jest niemal nigdy odpowiednie rozwiązanie do rozwiązywania problemów z synchronizacją, warstwami w chmurze lub innymi aspektami Azure File Sync. Usuwanie punktu końcowego serwera jest operacją niszczącą. Może to spowodować utratę danych w przypadku, gdy pliki warstwowe istnieją poza przestrzenią nazw punktu końcowego serwera. Zobacz [Dlaczego pliki warstwowe istnieją poza przestrzenią nazw punktu końcowego serwera,](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) Aby uzyskać więcej informacji. Lub może to spowodować brak dostępnych plików dla plików warstwowych, które znajdują się w przestrzeni nazw punktu końcowego serwera. Te problemy nie zostaną rozwiązane po odtworzeniu punktu końcowego serwera. Pliki warstwowe mogą znajdować się w przestrzeni nazw punktu końcowego serwera, nawet jeśli nie włączono obsługi warstw w chmurze. Dlatego zalecamy, aby nie usuwać punktu końcowego serwera, chyba że chcesz zrezygnować z używania Azure File Sync z tym konkretnym folderem lub zostały one jawnie przekazane przez inżyniera firmy Microsoft. Aby uzyskać więcej informacji na temat usuwania punktów końcowych serwera, zobacz [Usuwanie punktu końcowego serwera](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    
