---
title: Konfigurowanie ustawień pamięci podręcznej platformy Azure HPC
description: Wyjaśnia sposób konfigurowania dodatkowych ustawień pamięci podręcznej, takich jak MTU i No-root-squash, oraz sposobu uzyskiwania dostępu do migawek ekspresowych z obiektów docelowych usługi Azure Blob Storage.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: v-erkel
ms.openlocfilehash: 7938fcc0819fc3e5e0762cc8c3c2931594ed1c68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195064"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Skonfiguruj dodatkowe ustawienia pamięci podręcznej platformy Azure HPC

Na stronie **Konfiguracja** w Azure Portal są dostępne opcje dostosowywania kilku ustawień. Większość użytkowników nie musi zmieniać tych ustawień przy użyciu wartości domyślnych.

W tym artykule opisano również sposób korzystania z funkcji migawek dla obiektów docelowych usługi Azure Blob Storage. Funkcja Snapshot nie ma konfigurowalnych ustawień.

Aby wyświetlić ustawienia, Otwórz stronę **konfiguracji** pamięci podręcznej w Azure Portal.

![zrzut ekranu przedstawiający stronę konfiguracji w Azure Portal](media/configuration.png)

## <a name="adjust-mtu-value"></a>Dostosuj wartość MTU
<!-- linked from troubleshoot-nas article -->

Można wybrać maksymalny rozmiar jednostki transmisji dla pamięci podręcznej przy użyciu menu rozwijanego z etykietą **rozmiar jednostki MTU**.

Wartość domyślna to 1500 bajtów, ale można ją zmienić na 1400.

> [!NOTE]
> W przypadku obniżenia rozmiaru jednostki MTU pamięci podręcznej upewnij się, że klienci i systemy magazynu, które komunikują się z pamięcią podręczną, mają to samo ustawienie jednostki MTU lub mniejszą wartość.

Zmniejszenie wartości MTU pamięci podręcznej może ułatwić obejście ograniczeń rozmiaru pakietu w pozostałej części sieci pamięci podręcznej. Na przykład niektóre sieci VPN nie mogą pomyślnie przesyłać pełnych pakietów 1500-bajtowych. Zmniejszenie rozmiaru pakietów wysyłanych przez sieć VPN może wyeliminować ten problem. Należy jednak pamiętać, że ustawienie mniejszej jednostki MTU pamięci podręcznej oznacza, że każdy inny składnik, który komunikuje się z pamięcią podręczną i systemami magazynu, również musi mieć mniejsze ustawienie MTU, aby uniknąć problemów z komunikacją.

Jeśli nie chcesz zmieniać ustawień jednostki MTU dla innych składników systemu, nie należy zmniejszać ustawienia jednostki MTU pamięci podręcznej. Istnieją inne rozwiązania, które umożliwiają obejście ograniczeń rozmiaru pakietów sieci VPN. Aby dowiedzieć się więcej na temat diagnozowania i rozwiązywania tego problemu, przeczytaj artykuł [Dostosowywanie ograniczeń rozmiaru pakietów sieci VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) w artykule Rozwiązywanie problemów z serwerem nas.

Dowiedz się więcej o ustawieniach jednostki MTU w sieciach wirtualnych platformy Azure, odczytując [dostrajanie wydajności protokołu TCP/IP dla maszyn wirtualnych platformy Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="configure-root-squash"></a>Konfigurowanie katalogu głównego squash
<!-- linked from troubleshoot -->

Ustawienie **Włącz root squash** steruje sposobem, w jaki pamięć podręczna Azure HPC zezwala na dostęp do katalogu głównego. Squash główny pomaga zapobiegać dostępowi na poziomie głównym z nieautoryzowanych klientów.

To ustawienie umożliwia użytkownikom kontrolę dostępu do katalogu głównego na poziomie pamięci podręcznej, co może pomóc w ``no_root_squash`` zrekompensowaniu wymaganego ustawienia dla systemów nas używanych jako cele magazynu. (Dowiedz się więcej o [wymaganiach wstępnych dotyczących magazynu NFS](hpc-cache-prereqs.md#nfs-storage-requirements)). Może również zwiększyć bezpieczeństwo, gdy jest używany z obiektami docelowymi usługi Azure Blob Storage.

Ustawieniem domyślnym jest **tak**. (Pamięć podręczna utworzona przed 2020 kwietnia może mieć ustawienie domyślne **nie**.)

Jeśli ta funkcja jest włączona, uniemożliwia używanie w żądaniach klientów dostępu do pamięci podręcznej protokołu uprawnień Set-UID.

## <a name="view-snapshots-for-blob-storage-targets"></a>Wyświetlanie migawek dla obiektów docelowych magazynu obiektów BLOB

Pamięć podręczna Azure HPC automatycznie zapisuje migawki magazynu dla obiektów docelowych magazynu obiektów blob platformy Azure. Migawki zapewniają szybki punkt odniesienia zawartości kontenera magazynu zaplecza.

Migawki nie zastępują kopii zapasowych danych i nie zawierają żadnych informacji o stanie buforowanych danych.

> [!NOTE]
> Ta funkcja migawek różni się od funkcji migawek zawartej w oprogramowaniu NetApp lub Isilon Storage. Te implementacje migawek opróżniają zmiany z pamięci podręcznej do systemu magazynu zaplecza przed wykonaniem migawki.
>
> W celu uzyskania wydajności migawka pamięci podręcznej platformy Azure HPC nie najpierw opróżnia zmiany i rejestruje tylko dane zapisane w kontenerze obiektów BLOB. Ta migawka nie reprezentuje stanu danych w pamięci podręcznej, dlatego nie może zawierać ostatnio wprowadzonych zmian.

Ta funkcja jest dostępna tylko dla celów usługi Azure Blob Storage, a jej konfiguracja nie może zostać zmieniona.

Migawki są wykonywane co osiem godzin, przy UTC 0:00, 08:00 i 16:00.

W pamięci podręcznej Azure HPC są przechowywane codziennie, cotygodniowe i comiesięczne migawki, dopóki nie zostaną one zastąpione nowymi. Limity są następujące:

* do 20 codziennych migawek
* do 8 cotygodniowych migawek
* do 3 comiesięcznych migawek

Uzyskaj dostęp do migawek z `.snapshot` katalogu w przestrzeni nazw docelowego magazynu obiektów BLOB.
