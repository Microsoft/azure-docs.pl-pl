---
title: Zapobiegaj przypadkowemu usuwaniu — udziały plików platformy Azure
description: Dowiedz się więcej o usuwaniu nietrwałego dla udziałów plików platformy Azure i sposobach ich użycia do odzyskiwania danych i zapobiegania przypadkowemu usunięciu.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 96e3d5001d11455337ae092776a1a4c5c3738012
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83883072"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Zapobiegaj przypadkowemu usunięciu udziałów plików platformy Azure

Usługa Azure Storage oferuje teraz nietrwałe usuwanie udziałów plików. Funkcja usuwania nietrwałego umożliwia odzyskanie danych w przypadku pomyłkowego usunięcia ich przez aplikację lub innego użytkownika konta magazynu.

## <a name="how-soft-delete-works"></a>Jak działa usuwanie nietrwałe

Po włączeniu funkcja usuwania nietrwałego pozwala zapisywać i odzyskiwać udziały plików po ich usunięciu. Po usunięciu danych następuje przejście do nietrwałego stanu usuniętego zamiast wyczyszczenia. Można skonfigurować ilość czasu nietrwałego usuwania danych, zanim zostanie on trwale usunięty.

Usuwanie nietrwałe można włączyć dla nowych lub istniejących udziałów plików. Usuwanie nietrwałe jest również zgodne z poprzednimi wersjami, więc nie musisz wprowadzać żadnych zmian w aplikacjach, aby korzystać z ochrony usuwania nietrwałego. 

W przypadku nietrwałego usuwania udziałów plików w warstwie Premium przydział udziału plików (udostępniony rozmiar udziału plików) jest używany w łącznym obliczaniu limitu przydziału konta magazynu do momentu usunięcia nietrwałej daty wygaśnięcia udziałów, gdy udział zostanie całkowicie usunięty.

### <a name="availability"></a>Dostępność

Usuwanie nietrwałe dla udziałów plików platformy Azure jest dostępne we wszystkich warstwach magazynowania, wszystkich typach kont magazynu i w każdym regionie, w którym Azure Files jest dostępny w systemie.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

Usuwanie nietrwałe dla udziałów plików jest włączone na poziomie konta magazynu, ustawienia usuwania nietrwałego mają zastosowanie do wszystkich udziałów plików na koncie magazynu. Podczas tworzenia nowego konta magazynu, usuwanie nietrwałe jest domyślnie wyłączone. Usuwanie nietrwałe jest również domyślnie wyłączone dla istniejących kont magazynu. Można w dowolnym momencie włączyć lub wyłączyć funkcję usuwania nietrwałego.

Jeśli włączysz usuwanie nietrwałe dla udziałów plików, usuń niektóre udziały plików, a następnie Wyłącz usuwanie nietrwałe, jeśli udziały zostały zapisane w tym okresie, możesz nadal uzyskiwać dostęp do tych udziałów plików i odzyskiwać je. Po włączeniu usuwania nietrwałego należy również skonfigurować okres przechowywania.

Okres przechowywania wskazuje ilość czasu, przez który nietrwałe usunięte udziały plików są przechowywane i dostępne do odzyskania. W przypadku jawnie usuniętych udziałów plików zegar okresu przechowywania jest uruchamiany, gdy dane zostaną usunięte. Obecnie można zachować udziały nietrwałego usunięcia z zakresu od 1 do 365 dni.

Okres przechowywania nietrwałego usuwania można zmienić w dowolnym momencie. Zaktualizowany okres przechowywania będzie dotyczył tylko udziałów usuniętych po zaktualizowaniu okresu przechowywania. Udziały usunięte przed aktualizacją okresu przechowywania wygasną na podstawie okresu przechowywania, który został skonfigurowany podczas usuwania tych danych.

Aby trwale usunąć udział plików w stanie usuwania nietrwałego przed upływem jego czasu, należy cofnąć usunięcie tego udziału, wyłączyć usuwanie nietrwałe, a następnie usunąć udział ponownie. Następnie należy ponownie włączyć usuwanie nietrwałe, ponieważ wszystkie inne udziały plików na tym koncie magazynu będą narażone na przypadkowe usunięcie, podczas gdy usuwanie nietrwałe jest wyłączone.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

W przypadku udziałów plików w warstwach Standardowa i Premium są naliczane opłaty za wykorzystaną pojemność, a nie za zainicjowaną pojemność. Ponadto udziały plików w warstwie Premium są rozliczane według szybkości migawek w stanie usuwania nietrwałego. Standardowe udziały plików są rozliczane według zwykłej stawki w stanie usuwania nietrwałego. Po upływie skonfigurowanego okresu przechowywania nie będzie naliczana opłata za dane, które zostały trwale usunięte.

Aby uzyskać więcej informacji na temat cen usługi Azure File Storage ogólnie, zobacz [stronę cennika usługi azure File Storage](https://azure.microsoft.com/pricing/details/storage/files/).

Po pierwszym włączeniu usuwania nietrwałego zalecamy użycie małego okresu przechowywania, aby lepiej zrozumieć, jak ta funkcja ma wpływ na rachunek.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak włączyć i użyć usuwania nietrwałego, Kontynuuj [Włączanie usuwania nietrwałego](storage-files-enable-soft-delete.md)