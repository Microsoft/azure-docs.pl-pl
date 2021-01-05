---
title: Konfigurowanie ustawień pamięci podręcznej platformy Azure HPC
description: Wyjaśnia sposób konfigurowania dodatkowych ustawień pamięci podręcznej, takich jak MTU i No-root-squash, oraz sposobu uzyskiwania dostępu do migawek ekspresowych z obiektów docelowych usługi Azure Blob Storage.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 12/21/2020
ms.author: v-erkel
ms.openlocfilehash: 02bf862cdc3b20ef3e5fdb024f474267efa0c70d
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760507"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Skonfiguruj dodatkowe ustawienia pamięci podręcznej platformy Azure HPC

Na stronie **Konfiguracja** w Azure Portal są dostępne opcje dostosowywania kilku ustawień. Większość użytkowników nie musi zmieniać tych ustawień przy użyciu wartości domyślnych.

W tym artykule opisano również sposób korzystania z funkcji migawek dla obiektów docelowych usługi Azure Blob Storage. Funkcja Snapshot nie ma konfigurowalnych ustawień.

Aby wyświetlić ustawienia, Otwórz stronę **konfiguracji** pamięci podręcznej w Azure Portal.

![zrzut ekranu przedstawiający stronę konfiguracji w Azure Portal](media/configuration.png)

> [!TIP]
> W obszarze [Zarządzanie wideo w pamięci podręcznej platformy Azure HPC](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) wyświetlana jest strona Konfiguracja i jej ustawienia.

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
<!-- linked from troubleshoot and from access policies -->

Ustawienie **Włącz root squash** steruje sposobem, w jaki usługa Azure HPC cache traktuje żądania od użytkownika głównego na komputerach klienckich.

Po włączeniu elementu głównego squash użytkownicy root z klienta są automatycznie mapowana na użytkownika "nikt", gdy wysyłają żądania za pośrednictwem pamięci podręcznej platformy Azure HPC. Uniemożliwia ona również żądanie klienta z używania protokołu dostępu set-UID.

Jeśli główny squash jest wyłączony, żądanie od użytkownika głównego klienta (UID 0) jest przenoszona do systemu magazynu w pamięci podręcznej systemu plików NFS jako element główny. Ta konfiguracja może umożliwić niewłaściwy dostęp do pliku.

Ustawienie głównego squash w pamięci podręcznej może pomóc w zrekompensowaniu wymaganego ``no_root_squash`` Ustawienia w systemach nas, które są używane jako obiekty docelowe magazynu. (Dowiedz się więcej o [wymaganiach wstępnych dotyczących magazynu NFS](hpc-cache-prerequisites.md#nfs-storage-requirements)). Może również zwiększyć bezpieczeństwo, gdy jest używany z obiektami docelowymi usługi Azure Blob Storage.

Ustawieniem domyślnym jest **tak**. (Pamięć podręczna utworzona przed 2020 kwietnia może mieć ustawienie domyślne **nie**.)

> [!TIP]
> Można również ustawić squash głównych dla określonych eksportów magazynu, dostosowując [zasady dostępu klientów](access-policies.md#root-squash).

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
