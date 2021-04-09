---
title: Konfigurowanie ustawień pamięci podręcznej platformy Azure HPC
description: Zawiera opis konfigurowania dodatkowych ustawień pamięci podręcznej, takich jak MTU, Custom NTP i Konfiguracja DNS oraz sposób uzyskiwania dostępu do migawek ekspresowych z obiektów docelowych magazynu obiektów blob platformy Azure.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/17/2021
ms.author: v-erkel
ms.openlocfilehash: 6e1e1283cb82dcb900da6473de65ef087a5cea82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773236"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Skonfiguruj dodatkowe ustawienia pamięci podręcznej platformy Azure HPC

Na stronie **Sieć** w Azure Portal są dostępne opcje dostosowywania kilku ustawień. Większość użytkowników nie musi zmieniać tych ustawień przy użyciu wartości domyślnych.

W tym artykule opisano również sposób korzystania z funkcji migawek dla obiektów docelowych usługi Azure Blob Storage. Funkcja Snapshot nie ma konfigurowalnych ustawień.

Aby wyświetlić ustawienia, Otwórz stronę **sieci** pamięci podręcznej w Azure Portal.

![zrzut ekranu strony sieci w Azure Portal](media/networking-page.png)

> [!NOTE]
> Poprzednia wersja tej strony zawiera ustawienie squash głównego poziomu pamięci podręcznej, ale to ustawienie zostało przeniesione do [zasad dostępu klienta](access-policies.md).

<!-- >> [!TIP]
> The [Managing Azure HPC Cache video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) shows the networking page and its settings. -->

## <a name="adjust-mtu-value"></a>Dostosuj wartość MTU
<!-- linked from troubleshoot-nas article -->

Można wybrać maksymalny rozmiar jednostki transmisji dla pamięci podręcznej przy użyciu menu rozwijanego z etykietą **rozmiar jednostki MTU**.

Wartość domyślna to 1500 bajtów, ale można ją zmienić na 1400.

> [!NOTE]
> W przypadku obniżenia rozmiaru jednostki MTU pamięci podręcznej upewnij się, że klienci i systemy magazynu, które komunikują się z pamięcią podręczną, mają to samo ustawienie jednostki MTU lub mniejszą wartość.

Zmniejszenie wartości MTU pamięci podręcznej może ułatwić obejście ograniczeń rozmiaru pakietu w pozostałej części sieci pamięci podręcznej. Na przykład niektóre sieci VPN nie mogą pomyślnie przesyłać pełnych pakietów 1500-bajtowych. Zmniejszenie rozmiaru pakietów wysyłanych przez sieć VPN może wyeliminować ten problem. Należy jednak pamiętać, że ustawienie mniejszej jednostki MTU pamięci podręcznej oznacza, że każdy inny składnik, który komunikuje się z pamięcią podręczną i systemami magazynu, również musi mieć mniejsze ustawienie MTU, aby uniknąć problemów z komunikacją.

Jeśli nie chcesz zmieniać ustawień jednostki MTU dla innych składników systemu, nie należy zmniejszać ustawienia jednostki MTU pamięci podręcznej. Istnieją inne rozwiązania, które umożliwiają obejście ograniczeń rozmiaru pakietów sieci VPN. Aby dowiedzieć się więcej na temat diagnozowania i rozwiązywania tego problemu, przeczytaj artykuł [Dostosowywanie ograniczeń rozmiaru pakietów sieci VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) w artykule Rozwiązywanie problemów z serwerem nas.

Dowiedz się więcej o ustawieniach jednostki MTU w sieciach wirtualnych platformy Azure, odczytując [dostrajanie wydajności protokołu TCP/IP dla maszyn wirtualnych platformy Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="customize-ntp"></a>Dostosowywanie NTP

Pamięć podręczna domyślnie korzysta z time.microsoft.com serwera czasu opartego na platformie Azure. Jeśli chcesz, aby pamięć podręczna korzystała z innego serwera NTP, określ go w sekcji **Konfiguracja NTP** . Użyj w pełni kwalifikowanej nazwy domeny lub adresu IP.

## <a name="set-a-custom-dns-configuration"></a>Ustawianie niestandardowej konfiguracji DNS

> [!CAUTION]
> Nie należy zmieniać konfiguracji DNS pamięci podręcznej, jeśli nie jest to konieczne. Błędy konfiguracji mogą mieć konsekwencje fatalne. Jeśli konfiguracja nie może rozpoznać nazw usług platformy Azure, wystąpienie pamięci podręcznej HPC stanie się trwale niedostępne.

Pamięć podręczna Azure HPC jest automatycznie konfigurowana do korzystania z bezpiecznego i wygodnego systemu Azure DNS. Jednak niektóre nietypowe konfiguracje wymagają, aby pamięć podręczna korzystała z oddzielnego lokalnego systemu DNS zamiast systemu Azure. Sekcja **konfiguracji DNS** strony **Sieć** służy do określenia tego rodzaju systemu.

Skontaktuj się z przedstawicielami platformy Azure lub skontaktuj się z działem pomocy technicznej firmy Microsoft, aby określić, czy chcesz użyć niestandardowej konfiguracji DNS pamięci podręcznej.

W przypadku skonfigurowania lokalnego systemu DNS do korzystania z pamięci podręcznej platformy Azure HPC należy upewnić się, że konfiguracja może rozpoznać nazwy punktów końcowych platformy Azure dla usług platformy Azure. W razie potrzeby należy skonfigurować niestandardowe środowisko DNS do przesyłania dalej określonych żądań rozpoznawania nazw do Azure DNS lub do innego serwera.

Sprawdź, czy konfiguracja DNS może pomyślnie rozwiązać te elementy przed użyciem jej w przypadku pamięci podręcznej platformy Azure HPC:

* ``*.core.windows.net``
* Pobieranie listy odwołania certyfikatów (CRL) i usługi weryfikacji protokołu stanu certyfikatów online (OCSP). Częściowa lista znajduje się w [elemencie reguły zapory](../security/fundamentals/tls-certificate-changes.md#will-this-change-affect-me) na końcu tego [artykułu protokołu TLS platformy Azure](../security/fundamentals/tls-certificate-changes.md), ale należy skontaktować się z przedstawicielem technicznym firmy Microsoft, aby poznać wszystkie wymagania.
* W pełni kwalifikowana nazwa domeny serwera NTP (time.microsoft.com lub niestandardowy serwer)

Jeśli musisz ustawić niestandardowy serwer DNS dla pamięci podręcznej, użyj podanych pól:

* **Domena wyszukiwania DNS** (opcjonalnie) — wprowadź domenę wyszukiwania, na przykład ``contoso.com`` . Dozwolona jest pojedyncza wartość lub można pozostawić ją pustą.
* Serwery **DNS** — wprowadź do trzech serwerów DNS. Określ je według adresu IP.

<!-- 
  > [!NOTE]
  > The cache will use only the first DNS server it successfully finds. -->

Rozważ użycie pamięci podręcznej testów do sprawdzenia i dostosowania konfiguracji DNS przed użyciem jej w środowisku produkcyjnym.

### <a name="refresh-storage-target-dns"></a>Odśwież docelowy serwer DNS magazynu

Jeśli serwer DNS aktualizuje adresy IP, skojarzone elementy docelowe magazynu NFS staną się tymczasowo niedostępne. Zapoznaj się z artykułem jak zaktualizować niestandardowe adresy IP systemu DNS w obszarze [Edytuj cele magazynu](hpc-cache-edit-storage.md#update-ip-address-custom-dns-configurations-only).

## <a name="view-snapshots-for-blob-storage-targets"></a>Wyświetlanie migawek dla obiektów docelowych magazynu obiektów BLOB

Pamięć podręczna Azure HPC automatycznie zapisuje migawki magazynu dla obiektów docelowych magazynu obiektów blob platformy Azure. Migawki zapewniają szybki punkt odniesienia zawartości kontenera magazynu zaplecza.

Migawki nie zastępują kopii zapasowych danych i nie zawierają żadnych informacji o stanie buforowanych danych.

> [!NOTE]
> Ta funkcja migawek różni się od funkcji migawek zawartej w oprogramowaniu NetApp lub Isilon Storage. Te implementacje migawek opróżniają zmiany z pamięci podręcznej do systemu magazynu zaplecza przed wykonaniem migawki.
>
> W celu uzyskania wydajności migawka pamięci podręcznej platformy Azure HPC nie najpierw opróżnia zmiany i rejestruje tylko dane zapisane w kontenerze obiektów BLOB. Ta migawka nie reprezentuje stanu danych w pamięci podręcznej, dlatego nie może zawierać ostatnio wprowadzonych zmian.

Ta funkcja jest dostępna tylko dla celów usługi Azure Blob Storage, a jej konfiguracja nie może zostać zmieniona.

Migawki są wykonywane co osiem godzin, przy UTC 0:00, 08:00 i 16:00.

W pamięci podręcznej Azure HPC są przechowywane codziennie, cotygodniowe i comiesięczne migawki, dopóki nie zostaną one zastąpione nowymi. Limity przechowywania migawek są następujące:

* Do 20 codziennych migawek
* Do 8 cotygodniowych migawek
* Do 3 comiesięcznych migawek

Uzyskaj dostęp do migawek z `.snapshot` katalogu w katalogu głównym zainstalowanego miejsca docelowego magazynu obiektów BLOB.
