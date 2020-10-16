---
title: Zapobiegaj przypadkowemu usuwaniu — udziały plików platformy Azure
description: Dowiedz się więcej o usuwaniu nietrwałego dla udziałów plików platformy Azure i sposobach ich użycia do odzyskiwania danych i zapobiegania przypadkowemu usunięciu.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 50dbca7e32548380c6dbf338260b98ce59cda715
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122419"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Zapobiegaj przypadkowemu usunięciu udziałów plików platformy Azure

Usługa Azure Storage oferuje teraz nietrwałe usuwanie udziałów plików (wersja zapoznawcza). Funkcja usuwania nietrwałego umożliwia odzyskanie udziału plików, gdy zostanie on usunięty przez aplikację lub innego użytkownika konta magazynu.

## <a name="how-soft-delete-preview-works"></a>Jak działa usuwanie nietrwałe (wersja zapoznawcza)

Po włączeniu usuwania nietrwałego dla udziałów plików platformy Azure, jeśli udział plików zostanie usunięty, przechodzi do nietrwałego stanu usuniętego, a nie na stałe wymazywanie. Można skonfigurować ilość czasu nietrwałego usuwania danych, zanim zostanie on trwale usunięty, i Cofnij usunięcie udziału w dowolnym momencie w tym okresie przechowywania. Po cofnięciu usunięcia, udział i cała zawartość, łącznie z migawkami, zostaną przywrócone do stanu, w którym znajdował się przed usunięciem. Usuwanie nietrwałe działa tylko na poziomie udziału plików — poszczególne pliki, które są usuwane, nadal będą trwale wymazywane.

Usuwanie nietrwałe można włączyć dla nowych lub istniejących udziałów plików. Usuwanie nietrwałe jest również zgodne z poprzednimi wersjami, więc nie musisz wprowadzać żadnych zmian w aplikacjach, aby korzystać z ochrony usuwania nietrwałego. 

Aby trwale usunąć udział plików w stanie usuwania nietrwałego przed upływem jego czasu, należy cofnąć usunięcie tego udziału, wyłączyć usuwanie nietrwałe, a następnie usunąć udział ponownie. Następnie należy ponownie włączyć usuwanie trwałe, ponieważ wszystkie inne udziały plików na tym koncie magazynu będą narażone na przypadkowe usunięcie, podczas gdy usuwanie nietrwałe jest wyłączone.

W przypadku nietrwałego usuwania udziałów plików w warstwie Premium przydział udziału plików (udostępniony rozmiar udziału plików) jest używany w łącznym obliczaniu limitu przydziału konta magazynu do momentu usunięcia nietrwałej daty wygaśnięcia udziałów, gdy udział zostanie całkowicie usunięty.

## <a name="availability"></a>Dostępność

Usuwanie nietrwałe dla udziałów plików platformy Azure (wersja zapoznawcza) jest dostępne we wszystkich warstwach magazynowania, wszystkich typach kont magazynu i w każdym regionie, w którym Azure Files jest dostępna.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

### <a name="enabling-or-disabling-soft-delete"></a>Włączanie lub wyłączanie usuwania nietrwałego

Usuwanie nietrwałe dla udziałów plików jest włączone na poziomie konta magazynu. z tego powodu ustawienia usuwania nietrwałego mają zastosowanie do wszystkich udziałów plików na koncie magazynu. Można włączać lub wyłączać usuwanie nietrwałe w dowolnym momencie. Podczas tworzenia nowego konta magazynu nietrwałe usuwanie udziałów plików jest domyślnie wyłączone, gdy ta funkcja jest w publicznej wersji zapoznawczej. Gdy usuwanie nietrwałe staną się ogólnie dostępne, zostanie włączone domyślnie. Usuwanie nietrwałe pozostanie domyślnie wyłączone dla istniejących kont magazynu. Jeśli skonfigurowano [kopię zapasową udziału plików platformy](../../backup/azure-file-share-backup-overview.md) Azure dla udziału plików platformy Azure, usuwanie nietrwałe dla udziałów plików platformy Azure zostanie automatycznie włączone na koncie magazynu tego udziału.

Jeśli włączysz usuwanie nietrwałe dla udziałów plików, usuń niektóre udziały plików, a następnie Wyłącz usuwanie nietrwałe, jeśli udziały zostały zapisane w tym okresie, możesz nadal uzyskiwać dostęp do tych udziałów plików i odzyskiwać je. Po włączeniu usuwania nietrwałego należy również skonfigurować okres przechowywania.

### <a name="retention-period"></a>Okres przechowywania

Okres przechowywania to czas, przez który nietrwałe usunięte udziały plików są przechowywane i dostępne do odzyskania. W przypadku jawnie usuniętych udziałów plików zegar okresu przechowywania jest uruchamiany, gdy dane zostaną usunięte. Obecnie można określić okres przechowywania od 1 do 365 dni. Okres przechowywania nietrwałego usuwania można zmienić w dowolnym momencie. Zaktualizowany okres przechowywania będzie dotyczył tylko udziałów usuniętych po zaktualizowaniu okresu przechowywania. Udziały usunięte przed aktualizacją okresu przechowywania wygasną na podstawie okresu przechowywania, który został skonfigurowany podczas usuwania tych danych.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

W przypadku udziałów plików w warstwach Standardowa i Premium są naliczane opłaty za wykorzystaną pojemność, a nie za zainicjowaną pojemność. Ponadto udziały plików w warstwie Premium są rozliczane według szybkości migawek w stanie usuwania nietrwałego. Standardowe udziały plików są rozliczane według zwykłej stawki w stanie usuwania nietrwałego. Po upływie skonfigurowanego okresu przechowywania nie będzie naliczana opłata za dane, które zostały trwale usunięte.

Aby uzyskać więcej informacji na temat cen usługi Azure File Storage ogólnie, zobacz [stronę cennika usługi azure File Storage](https://azure.microsoft.com/pricing/details/storage/files/).

Po pierwszym włączeniu usuwania nietrwałego zalecamy użycie małego okresu przechowywania, aby lepiej zrozumieć, jak ta funkcja ma wpływ na rachunek.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak włączyć i użyć usuwania nietrwałego, Kontynuuj [Włączanie usuwania nietrwałego](storage-files-enable-soft-delete.md).
