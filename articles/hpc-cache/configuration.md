---
title: Konfigurowanie ustawień pamięci podręcznej HPC usługi Azure
description: W tym artykule wyjaśniono, jak skonfigurować dodatkowe ustawienia pamięci podręcznej, takie jak MTU i no-root-squash, oraz jak uzyskać dostęp do wyraźnych migawek z obiektów docelowych magazynu obiektów Blob platformy Azure.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 137e41a3f7d346bb612605660d7798ac2fc297d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538819"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Konfigurowanie dodatkowych ustawień pamięci podręcznej HPC usługi Azure

Strona **Konfiguracja** w witrynie Azure portal ma opcje dostosowywania kilku ustawień. Większość użytkowników nie musi zmieniać ich wartości z wartości domyślnych.

W tym artykule opisano również, jak używać funkcji migawki dla obiektów docelowych magazynu obiektów Blob platformy Azure. Funkcja migawki nie ma konfigurowalnych ustawień.

Aby wyświetlić ustawienia, otwórz stronę **konfiguracji** pamięci podręcznej w witrynie Azure portal.

![zrzut ekranu przedstawiający stronę konfiguracji w witrynie Azure portal](media/configuration.png)

## <a name="adjust-mtu-value"></a>Dostosowywanie wartości jednostki MTU
<!-- linked from troubleshoot-nas article -->

Maksymalny rozmiar jednostki transmisji dla pamięci podręcznej można wybrać za pomocą menu rozwijanego oznaczonego **jako rozmiar jednostki MTU**.

Wartość domyślna to 1500 bajtów, ale można ją zmienić na 1400.

> [!NOTE]
> Jeśli obniżysz rozmiar jednostki MTU pamięci podręcznej, upewnij się, że klienci i systemy magazynowania, które komunikują się z pamięcią podręczną, mają takie samo ustawienie jednostki MTU lub niższą wartość.

Obniżenie wartości jednostki MTU pamięci podręcznej może pomóc obejść ograniczenia rozmiaru pakietów w pozostałej części sieci pamięci podręcznej. Na przykład niektóre sieci VPN nie mogą pomyślnie przesyłać pełnowymiarowych pakietów 1500 bajtów. Zmniejszenie rozmiaru pakietów wysyłanych przez sieć VPN może wyeliminować ten problem. Należy jednak pamiętać, że ustawienie jednostki MTU niższej pamięci podręcznej oznacza, że każdy inny składnik, który komunikuje się z pamięcią podręczną — w tym klientów i systemów magazynu — musi mieć niższe ustawienie, aby uniknąć problemów z komunikacją z pamięcią podręczną.

Jeśli nie chcesz zmieniać ustawień jednostki MTU w innych składnikach systemu, nie należy obniżać ustawienia jednostki MTU pamięci podręcznej. Istnieją inne rozwiązania do obejść ograniczenia rozmiaru pakietów sieci VPN. Przeczytaj [artykuł Dopasowywanie ograniczeń rozmiaru pakietów sieci VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) w artykule rozwiązywania problemów z serwerem NAS, aby dowiedzieć się więcej o diagnozowaniu i rozwiązywaniu tego problemu.

Dowiedz się więcej o ustawieniach jednostek MTU w sieciach wirtualnych platformy Azure, czytając [dostrajanie wydajności TCP/IP dla maszyn wirtualnych platformy Azure.](../virtual-network/virtual-network-tcpip-performance-tuning.md)

## <a name="configure-root-squash"></a>Konfigurowanie dyni korzenia
<!-- linked from troubleshoot -->

Ustawienie **Włącz funkcję zgłębienia głównego** steruje tym, jak pamięć podręczna HPC platformy Azure umożliwia dostęp do roota. Root squash pomaga zapobiec dostępowi na poziomie roota od nieautoryzowanych klientów.

To ustawienie umożliwia użytkownikom kontrolowanie dostępu do roota na ``no_root_squash`` poziomie pamięci podręcznej, co może pomóc w zrekompensowaniu wymaganego ustawienia dla systemów NAS używanych jako obiekty docelowe pamięci masowej. (Przeczytaj więcej o [wymaganiach wstępnych miejsca docelowego magazynu NFS](hpc-cache-prereqs.md#nfs-storage-requirements).) Może również poprawić bezpieczeństwo, gdy jest używany z obiektami docelowymi magazynu obiektów Blob platformy Azure.

Domyślnym **ustawieniem**jest Tak . (Pamięci podręczne utworzone przed kwietniem 2020 r. mogą mieć ustawienie domyślne **Nr.)** Po włączeniu ta funkcja zapobiega również używaniu bitów uprawnień set-UID w żądaniach klientów do pamięci podręcznej.

## <a name="view-snapshots-for-blob-storage-targets"></a>Wyświetlanie migawek obiektów docelowych magazynu obiektów blob

Usługa Azure HPC Cache automatycznie zapisuje migawki magazynu dla obiektów docelowych magazynu obiektów blob platformy Azure. Migawki zapewniają szybki punkt odniesienia dla zawartości kontenera magazynu zaplecza. Migawki nie zastępują kopii zapasowych danych i nie zawierają żadnych informacji o stanie danych w pamięci podręcznej.

> [!NOTE]
> Ta funkcja migawki różni się od funkcji migawki zawartej w oprogramowaniu do przechowywania plików NetApp, Isilon lub ZFS. Te implementacje migawki opróżnić zmiany z pamięci podręcznej do systemu magazynu zaplecza przed wykonaniem migawki.
>
> Aby zwiększyć wydajność, migawka pamięci podręcznej HPC platformy Azure nie opróżnia zmian najpierw i rejestruje tylko dane, które zostały zapisane w kontenerze obiektów blob. Ta migawka nie reprezentuje stanu danych w pamięci podręcznej, więc ostatnie zmiany mogą być wykluczone.

Ta funkcja jest dostępna tylko dla obiektów docelowych magazynu obiektów Blob platformy Azure i nie można zmienić jej konfiguracji.

Migawki są pobierane co osiem godzin, w utc 0:00, 08:00 i 16:00.

Pamięć podręczna HPC usługi Azure przechowuje migawki codziennie, co tydzień i co miesiąc, dopóki nie zostaną zastąpione nowymi. Limity są następujące:

* do 20 codziennych migawek
* do 8 cotygodniowych migawek
* do 3 migawek miesięcznych

Dostęp do migawek z `.snapshot` katalogu w przestrzeni nazw obiektu docelowego magazynu obiektów blob.
