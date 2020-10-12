---
title: plik dołączany
description: plik dołączany
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be3ce42ccdb9bedd02b8dead2426ac629fa12ef2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "72391737"
---
Nie: Usuwanie punktu końcowego serwera nie jest podobne do ponownego rozruchu serwera. Usunięcie i ponowne utworzenie punktu końcowego serwera jest niemal nigdy odpowiednie rozwiązanie do rozwiązywania problemów z synchronizacją, warstwami w chmurze lub innymi aspektami Azure File Sync. Usuwanie punktu końcowego serwera jest operacją niszczącą. Może to spowodować utratę danych w przypadku, gdy pliki warstwowe istnieją poza przestrzenią nazw punktu końcowego serwera. Zobacz [Dlaczego pliki warstwowe istnieją poza przestrzenią nazw punktu końcowego serwera,](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) Aby uzyskać więcej informacji. Lub może to spowodować brak dostępnych plików dla plików warstwowych, które znajdują się w przestrzeni nazw punktu końcowego serwera. Te problemy nie zostaną rozwiązane po odtworzeniu punktu końcowego serwera. Pliki warstwowe mogą znajdować się w przestrzeni nazw punktu końcowego serwera, nawet jeśli nie włączono obsługi warstw w chmurze. Dlatego zaleca się, aby nie usuwać punktu końcowego serwera, chyba że chcesz zatrzymać korzystanie z Azure File Sync z tym konkretnym folderem lub zostały one jawnie nadane przez inżyniera firmy Microsoft. Aby uzyskać więcej informacji na temat usuwania punktów końcowych serwera, zobacz [Usuwanie punktu końcowego serwera](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    
