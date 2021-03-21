---
title: Microsoft Azure Data Box Disk — często zadawane pytania | Microsoft Docs — dane
description: Ten artykuł zawiera często zadawane pytania dotyczące usługi Azure Data Box Disk, rozwiązania w chmurze umożliwiającego przenoszenie dużych ilości danych na platformę Azure, oraz odpowiedzi na te pytania
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: alkohli
ms.custom: references_regions
ms.openlocfilehash: 88aedb7daa375ae6b4a9107dceed1d25ed72ed92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039108"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Azure Data Box Disk: często zadawane pytania

Rozwiązanie w chmurze Microsoft Azure Data Box Disk umożliwia wysyłanie terabajtów danych na platformę Azure w szybki, niedrogi i niezawodny sposób. Często zadawane pytania zawierają pytania, które mogą się pojawić podczas korzystania z usługi Data Box Disk w witrynie Azure Portal, oraz odpowiedzi na te pytania. 

Pytania i odpowiedzi są podzielone na następujące kategorie:

- Informacje o usłudze
- Konfigurowanie i łączenie 
- Śledzenie stanu
- Migrowanie danych 
- Weryfikowanie i przekazywanie danych 


## <a name="about-the-service"></a>Informacje o usłudze

### <a name="q-what-is-azure-data-box-service"></a>PYTANIE: Co to jest usługa Azure Data Box? 
A.  Usługa Azure Data Box jest przeznaczona do pozyskiwania danych w trybie offline. Ta usługa zarządza szeregiem produktów dostosowanych do transportu danych dla różnych pojemności magazynu. 

### <a name="q-what-are-azure-data-box-disks"></a>PYTANIE: Co to są urządzenia Azure Data Box Disk?
A. Urządzenia Azure Data Box Disk umożliwiają szybki, niedrogi i bezpieczny transfer terabajtów danych do i z platformy Azure. Firma Microsoft dostarcza od 1 do 5 dysków o maksymalnej pojemności magazynu wynoszącej 35 TB. Za pomocą usługi Data Box w witrynie Azure Portal możesz łatwo konfigurować, łączyć i odblokowywać te dyski.  

Dyski są szyfrowane za pomocą funkcji szyfrowania dysków Microsoft BitLocker, a klucze szyfrowania są zarządzane w witrynie Azure Portal. Następnie należy skopiować dane z serwerów klienta. W centrum danych firma Microsoft migruje dane z dysku do chmury przy użyciu szybkiego połączenia przekazywania w sieci prywatnej i przekazuje je na platformę Azure.

### <a name="q-when-should-i-use-data-box-disks"></a>PYTANIE: Kiedy używać urządzeń Data Box Disk?
A. Jeśli masz nie więcej niż 40 TB danych, które chcesz przenieść na platformę Azure, korzystne będzie zastosowanie urządzeń Data Box Disk.

### <a name="q-what-is-the-price-of-data-box-disks"></a>PYTANIE: Jaki jest koszt urządzeń Data Box Disk?
A. Aby uzyskać informacje o cenach urządzeń Data Box Disk, przejdź do [strony cennika](https://azure.microsoft.com/pricing/details/databox/disk/).

### <a name="q-how-do-i-get-data-box-disks"></a>PYTANIE: Jak mogę otrzymać urządzenia Data Box Disk? 
A.  Aby otrzymać urządzenia Azure Data Box Disk, zaloguj się do witryny Azure Portal i utwórz zamówienie dysków w usłudze Data Box. Podaj informacje kontaktowe i szczegóły dotyczące powiadomień. Po złożeniu zamówienia, w zależności od dostępności, dyski zostaną dostarczone do Ciebie w ciągu 10 dni.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>PYTANIE: Jaka jest maksymalna ilość danych, które mogę jednorazowo przenieść za pomocą urządzeń Data Box Disk?
A. Na pięć dysków, z których każda ma pojemność 8 TB (7 TB pojemności do wykorzystania), maksymalna użyteczna pojemność to 35 TB. Dzięki temu można przenieść 35 TB danych w jednym wystąpieniu. Aby przenieść większą ilość danych, musisz zamówić więcej dysków.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>PYTANIE: Jak sprawdzić, czy urządzenia Data Box Disk są dostępne w moim regionie? 
A.  Aby sprawdzić, gdzie urządzenie Data Box dyski są obecnie dostępne, przejdź do [obszaru dostępność w regionie](data-box-disk-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>PYTANIE: W jakich regionach mogę zapisać dane, korzystając z usługi Data Box Disk?
A. Data Box Disk jest obsługiwana w przypadku wszystkich regionów w Stanach Zjednoczonych, Kanadzie, EU, ZJEDNOCZONYm, Australii, Singapur, Indie, Chiny, Hongkong SAR, Japonia, Korea i Republika Południowej Afryki. Obsługiwane są tylko regiony chmury publicznej Azure. Usługa Azure Government ani inne suwerenne chmury nie są obsługiwane.

### <a name="q-how-can-i-import-source-data-present-at-my-location-in-one-countryregion-to-an-azure-region-in-a-different-country"></a>PYTANIE: Jak mogę zaimportować dane źródłowe w mojej lokalizacji w jednym kraju/regionie do regionu świadczenia usługi Azure w innym kraju?
A. Data Box Disk obsługuje pozyskiwanie danych tylko w obrębie tego samego kraju/regionu co ich miejsce docelowe i nie będzie przekraczać granic międzynarodowych. Jedyny wyjątek dotyczy zamówień w Unii Europejskiej (UE), gdzie urządzenie Data Box dyski mogą być dostarczane do i z dowolnego kraju/regionu UE.

Na przykład jeśli chcesz przenieść dane z lokalizacji w Kanadzie do konta magazynu w regionie zachodnie stany USA, można to osiągnąć w następujący sposób:

#### <a name="option-1"></a>Opcja 1. 

Wyślij [obsługiwany dysk](../import-export/storage-import-export-requirements.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#supported-disks) zawierający dane za pomocą [usługi Azure Import/Export](../import-export/storage-import-export-service.md) z lokalizacji źródłowej w Kanadzie do lokalnego centrum danych platformy Azure zachodnie stany USA.

#### <a name="option-2"></a>Opcja 2.

1. Zamów Data Box Disk w Kanadzie, wybierając konto magazynu w Kanadzie centralnych. Dyski SSD są dostarczane z centrum danych platformy Azure w Kanadzie centralnych do adresu wysyłkowego (w Kanadzie), który jest dostarczany podczas tworzenia zamówienia.

2. Po skopiowaniu danych z serwera lokalnego do dysków należy zwrócić je do centrum danych platformy Azure w Kanadzie, korzystając z dostarczonych etykiet zwracanych przez firmę Microsoft. Dane znajdujące się na Data Box Diskach są następnie przekazywane do docelowego konta magazynu w regionie usługi Azure Kanada wybranym podczas tworzenia zamówienia.

3. Następnie możesz użyć narzędzia, takiego jak AzCopy, aby skopiować dane na konto magazynu w regionie zachodnie stany USA. Ten krok wiąże się ze [standardowymi](https://azure.microsoft.com/pricing/details/storage/) [opłatami za](https://azure.microsoft.com/pricing/details/bandwidth/) magazyn i przepustowość, które nie są uwzględnione w rozliczeniach Data Box Disk.

#### <a name="q-does-data-box-disk-store-any-customer-data-outside-of-the-service-region"></a>PYTANIE: Czy Data Box Disk przechowywać dane klienta poza regionem usługi?

A. Nie. Data Box Disk nie przechowuje żadnych danych klienta poza regionem usługi. Klient ma pełną własność swoich danych i umożliwia zapisanie danych w określonej lokalizacji na podstawie konta magazynu wybieranego podczas tworzenia zamówienia.  

Oprócz danych klienta dostępne są Data Box Disk dane, które zawierają metadane i dzienniki monitorowania. We wszystkich regionach (z wyjątkiem Brazylii Południowe i Azja Południowo-Wschodnia) Data Box Disk dane są przechowywane i replikowane w [sparowanym regionie](../best-practices-availability-paired-regions.md) za pośrednictwem konta magazynu geograficznie nadmiarowego w celu ochrony przed utratą danych.  

Ze względu na [wymagania dotyczące danych miejsca zamieszkania](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information) w regionie Brazylia Południowa i Azja Południowo-Wschodnia Data Box Disk dane są przechowywane w ramach konta magazynu Strefowo nadmiarowego (ZRS), aby było ono zawarte w jednym regionie. W przypadku Azja Południowo-Wschodnia wszystkie dane Data Box Disk są przechowywane w Singapurze i w Brazylii Południowej, dane są przechowywane w Brazylii. 

Jeśli wystąpi awaria usługi w regionie Brazylia Południowa i Azja Południowo-Wschodnia, klienci mogą tworzyć nowe zamówienia z innego regionu. Nowe zamówienia będą obsługiwane z regionu, w którym zostały utworzone, a klienci są zobowiązani do wysyłki do Data Box Disk i do niego.



### <a name="q-how-can-i-recover-my-data-if-an-entire-region-fails"></a>PYTANIE: Jak można odzyskać dane, jeśli cały region ulegnie awarii?

A. W skrajnych okolicznościach, gdy region zostanie utracony ze względu na znaczną awarię, firma Microsoft może zainicjować regionalną pracę w trybie failover. W tym przypadku nie jest wymagana żadna akcja. Zamówienie zostanie zrealizowane w regionie trybu failover, jeśli znajduje się w tym samym kraju lub na granicy handlu. Jednak niektóre regiony platformy Azure nie mają sparowanego regionu w tej samej granicy geograficznej lub handlowej. Jeśli w dowolnym z tych regionów występuje awaria, należy ponownie utworzyć zamówienie urządzenie Data Box z innego dostępnego regionu i skopiować dane na platformę Azure w nowym regionie. Aby uzyskać więcej informacji, zobacz [Business continuity and disaster recovery (BCDR): Azure Paired Regions](../best-practices-availability-paired-regions.md) (Ciągłość działalności biznesowej i odzyskiwanie po awarii — BCDR: regiony sparowane platformy Azure).

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>PYTANIE: Z kim należy się skontaktować, gdy pojawią się jakiekolwiek problemy z urządzeniami Data Box Disk?
A. Jeśli wystąpią problemy z urządzenie Data Box dyskami, [skontaktuj się z pomoc techniczna firmy Microsoft](./data-box-disk-contact-microsoft-support.md).

## <a name="order-device"></a>Zamawianie urządzenia

### <a name="q-how-do-i-get-data-box-disk"></a>PYTANIE: Jak mogę uzyskać Data Box Disk? 
A.  Aby uzyskać Azure Data Box Disk, zaloguj się do Azure Portal i Utwórz zamówienie Data Box Disk. Podaj informacje kontaktowe i szczegóły dotyczące powiadomień. Po umieszczeniu zamówienia na podstawie dostępności Data Box Disk zostanie wysłane do Ciebie w ciągu 10 dni. Aby uzyskać więcej informacji, zobacz temat [Zamawianie urządzenia Data Box](data-box-disk-deploy-ordered.md).

### <a name="q-i-couldnt-create-a-data-box-disk-order-in-the-azure-portal-why"></a>PYTANIE: Nie można utworzyć kolejności Data Box Disk w Azure Portal. Dlaczego?
A. Jeśli nie możesz utworzyć kolejności Data Box Disk, wystąpił problem z typem subskrypcji lub dostępem.

Sprawdź swoją subskrypcję. Data Box Disk jest dostępna tylko dla ofert subskrypcji Enterprise Agreement (EA) i dostawcy rozwiązań w chmurze (CSP). Jeśli nie masz żadnego z tych typów subskrypcji, skontaktuj się z pomoc techniczna firmy Microsoft, aby uaktualnić subskrypcję.

Jeśli masz obsługiwany typ oferty subskrypcji, sprawdź poziom dostępu swojej subskrypcji. Musisz być współautorem lub właścicielem subskrypcji, aby utworzyć zamówienie.

### <a name="q-how-long-will-my-order-take-from-order-creation-to-data-uploaded-to-azure"></a>PYTANIE: Jak długo moja kolejność zajmie się tworzeniem kolejności danych przekazywanych do platformy Azure?

A. Następujące szacowane czasy realizacji dla każdej fazy przetwarzania zamówień zapewniają dobry pomysł na to, czego oczekujesz.  

Te czasy realizacji są *szacunkowe*. Czas na każdy etap przetwarzania zamówień ma wpływ na obciążenie centrum danych, współbieżne zamówienia i inne warunki środowiska.

**Szacowany czas realizacji dla Data Box Disk zamówienia:**

1. Data Box Disk kolejności: kilka minut, od portalu
2. Przydział i przygotowanie dysku: do 5 dni roboczych, w zależności od dostępności spisu i liczby oczekujących zamówień do przetworzenia
3. Wysyłka: 2–3 dni robocze
4. Kopiowanie danych w lokacji klienta: zależy od rodzaju danych, rozmiaru i liczby plików.
5. Wysyłka zwrotna: 2–3 dni robocze
6. Przetwarzanie w centrum danych i przekazywanie na platformę Azure: przekazywanie danych rozpocznie się na poziomie centrum, gdy tylko zakończy się przetwarzanie operacyjne, a dysk jest połączony. Czas przekazywania zależy od rodzaju danych, rozmiaru i liczby plików.

## <a name="configure-and-connect"></a>Konfigurowanie i łączenie
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>PYTANIE: Czy mogę określić liczbę urządzeń Data Box Disk w zamówieniu?
A.  Nie. W zależności od rozmiaru danych i dostępności dysków można pobrać 8 TB dysków (maksymalnie pięć dysków).  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>PYTANIE: Jak mogę odblokować urządzenia Data Box Disk? 
A.  W witrynie Azure Portal przejdź do zamówienia urządzenia Data Box Disk, a następnie wybierz pozycję **Szczegóły urządzenia**. Skopiuj klucz dostępu. W witrynie Azure Portal pobierz i wyodrębnij narzędzie do odblokowywania dysków Data Box Disk dla Twojego systemu operacyjnego. Uruchom narzędzie na komputerze zawierającym dane, które chcesz skopiować na dyski. Podaj klucz dostępu, aby odblokować dyski. Ten sam klucz dostępu odblokowuje wszystkie dyski. 

Aby uzyskać instrukcje krok po kroku, zobacz [Odblokowywanie dysków na komputerze klienckim z systemem Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) lub [Odblokowywanie dysków na komputerze klienckim z systemem Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>PYTANIE: Czy mogę użyć komputera-hosta z systemem Linux, aby nawiązać połączenie i skopiować dane na urządzenia Data Box Disk?
A.  Tak. Zarówno komputer kliencki z systemem Linux, jak i z systemem Windows może zostać użyty do nawiązania połączenia i skopiowania danych na urządzenia Data Box Disk. Aby uzyskać więcej informacji, przejdź do listy [Obsługiwane systemy operacyjne](data-box-disk-system-requirements.md) komputera-hosta.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>PYTANIE: Moje dyski zostały wysłane, ale teraz chcę anulować to zamówienie. Dlaczego przycisk Anuluj nie jest dostępny?
A.  Zamówienie można anulować tylko po zamówieniu dysków, ale przed ich wysyłką. Po wysłaniu dysków nie można już anulować zamówienia. Można jednak zwrócić dyski za odpowiednią opłatą. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>PYTANIE: Czy mogę podłączyć wiele urządzeń Data Box Disk do tego samego komputera-hosta w celu transferu danych?
A. Tak. Do jednego komputera-hosta można podłączyć wiele urządzeń Data Box Disk na potrzeby transferu danych i można równolegle wykonywać wiele zadań kopiowania.

## <a name="track-status"></a>Śledzenie stanu

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>PYTANIE: Jak mogę śledzić stan dysków od momentu złożenia zamówienia do ich odesłania? 
A.  Stan zamówienia urządzenia Data Box Disk możesz śledzić w witrynie Azure Portal. Podczas tworzenia zamówienia jest również wyświetlany monit o podanie adresu e-mail do powiadomień. Jeśli je podano, otrzymasz powiadomienie pocztą e-mail o wszystkich zmianach stanu zamówienia. Więcej informacji na temat [konfigurowania wiadomości e-mail z powiadomieniami](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>PYTANIE: Jak mogę zwrócić dyski? 
A.  Firma Microsoft dołącza etykietę wysyłkową do przesyłki z urządzeniami Data Box Disk. Przyklej etykietę do opakowania wysyłkowego i dostarcz zapieczętowaną paczkę do firmy przewozowej. Jeśli etykieta została uszkodzona lub zgubiona, wybierz pozycję **Przegląd > Pobierz etykietę wysyłkową** i pobierz nową zwrotną etykietę wysyłkową.

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>Czy mogę wybrać moją Data Box Disk zamówienie samodzielnie? Czy mogę przywrócić dyski za pośrednictwem wybranego przez siebie operatora?
A. Tak. Firma Microsoft oferuje również samozarządzaną wysyłkę wyłącznie w US Gov regionie. Podczas umieszczania kolejności Data Box Disk można wybrać opcję samozarządzanej wysyłki. Aby wybrać zamówienie Data Box Disk, wykonaj następujące czynności:
    
1. Po umieszczeniu zamówienia zamówienie jest przetwarzane i dyski są przygotowywane. Otrzymasz powiadomienie za pośrednictwem wiadomości e-mail, że zamówienie jest gotowe do odbioru. 
2. Gdy zamówienie będzie gotowe do pobrania, przejdź do zamówienia w Azure Portal i przejdź do bloku **Przegląd** . 
3. Zobaczysz powiadomienie z kodem w Azure Portal. Wyślij wiadomość e-mail do [zespołu operacji Azure Data Box](mailto:adbops@microsoft.com) i podaj kod. Zespół dostarczy lokalizację i zaplanował datę i godzinę pobrania. Po otrzymaniu powiadomienia e-mail należy zadzwonić do zespołu w ciągu 5 dni roboczych.

Po zakończeniu kopiowania i weryfikacji danych wykonaj następujące kroki, aby zwrócić dysk:

1. Po zakończeniu sprawdzania poprawności danych odłącz dyski. Odłącz kable połączeniowe.
2. Zawiń wszystkie dyski i kable łączące za pomocą zawijania bąbelków i umieść je w polu wysyłka. W przypadku braku akcesoriów mogą zostać naliczone opłaty.

    - Ponownie użyj opakowania z pierwotnej wysyłki. Zalecamy zabezpieczenie dysków przy użyciu folii bąbelkowej.
    - Upewnij się, że wszystko jest dopasowane i żaden element nie przemieszcza się w opakowaniu.
3. Przejdź do **bloku przegląd** dla zamówienia w Azure Portal. Powinno zostać wyświetlone powiadomienie z kodem.
4. Użyj tego kodu i Wyślij wiadomość e-mail do [zespołu operacji Azure Data Box](mailto:adbops@microsoft.com) i podaj kod. Dostarczą informacje o tym, gdzie i kiedy należy porzucić dyski.


## <a name="migrate-data"></a>Migrowanie danych

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>PYTANIE: Jaki jest maksymalny rozmiar danych w przypadku urządzeń Data Box Disk?  
A.  Rozwiązanie Data Box Disk może zapewnić do 5 dysków o maksymalnej pojemności do wykorzystania wynoszącej 35 TB. Same dyski mają rozmiar 8 TB (przy czym rozmiar do wykorzystania to 7 TB).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>PYTANIE: Jakie są maksymalne rozmiary blokowych obiektów blob i stronicowych obiektów blob obsługiwane przez urządzenia Data Box Disk ? 
A.  Maksymalne rozmiary podlegają limitom usługi Azure Storage. Maksymalny rozmiar blokowego obiektu blob to około 4,768 TiB, a maksymalny rozmiar stronicowego obiektu blob to 8 TiB. Aby uzyskać więcej informacji, zobacz [elementy docelowe skalowalności i wydajności dla usługi BLOB Storage](../storage/blobs/scalability-targets.md).

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>PYTANIE: Jaka jest szybkość transferu danych w przypadku urządzeń Data Box Disk?
A. Podczas testowania dysków podłączonych za pomocą portu USB 3.0 ich wydajność doszła do poziomu 430 MB/s. Rzeczywista wartość różni się w zależności od rozmiaru pliku. Przy mniejszych plikach wydajność może być niższa.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>PYTANIE: Skąd mam wiedzieć, że moje dane są bezpieczne podczas transportu? 
A.  Urządzenia Data Box Disk są zaszyfrowane za pomocą funkcji BitLocker z użyciem 128-bitowego algorytmu AES, a klucz dostępu jest dostępny tylko w witrynie Azure Portal. Zaloguj się do witryny Azure Portal przy użyciu poświadczeń konta, aby uzyskać klucz dostępu. Podaj ten klucz dostępu podczas uruchamiania narzędzia do odblokowywania urządzenia Data Box Disk.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>PYTANIE: Jak mogę skopiować dane na urządzenia Data Box Disk? 
A.  Użyj narzędzia do kopiowania SMB, takiego jak `Robocopy` , `Diskboss` , lub nawet przeciągnij i upuść w Eksploratorze plików systemu Windows, aby skopiować dane na dyski.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>PYTANIE: Czy istnieją jakieś porady dotyczące przyspieszania kopiowania danych?
A.  Aby przyspieszyć proces kopiowania:

- Użyj wielu strumieni kopiowania danych. Na przykład w programie `Robocopy` należy użyć opcji wielowątkowej. Aby uzyskać więcej informacji na temat konkretnego polecenia, zobacz [Tutorial: Copy data to Azure Data Box Disk and verify (Samouczek: kopiowanie danych na urządzenie Data Box Disk i ich weryfikacja)](data-box-disk-deploy-copy-data.md#copy-data-to-disks).
- Użyj wielu sesji.
- Zamiast kopiować za pośrednictwem udziału sieciowego (gdzie szybkość sieci może powodować ograniczenia), upewnij się, że dane znajdują się lokalnie na komputerze, do którego podłączono dyski.
- Upewnij się, że korzystasz z portu USB 3,0 lub nowszego w trakcie procesu kopiowania. Pobieranie i używanie [ `USBView` Narzędzia](/windows-hardware/drivers/debugger/usbview) do identyfikowania kontrolerów USB i urządzeń USB podłączonych do komputera.
- Wykonaj test porównawczy wydajności komputera używanego do kopiowania danych. Pobierz [ `Bluestop` `FIO` Narzędzie](https://ci.appveyor.com/project/axboe/fio) i użyj go do oceny wydajności sprzętu serwera. Wybierz najnowszą kompilację x86 lub x64, wybierz kartę **artefakty** i Pobierz plik msi.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>PYTANIE: Jak przyspieszyć kopiowanie danych, jeśli dane źródłowe składają się z małych plików (o rozmiarze wyrażonym w kilobajtach lub równym kilka megabajtów)?
A.  Aby przyspieszyć proces kopiowania:

- Utwórz lokalny dysk VHDX w szybkim magazynie lub utwórz pusty dysk VHD na dysku twardym lub SSD (jest to wolniejsze).
- Zainstaluj go na maszynie wirtualnej.
- Skopiuj pliki na dysk maszyny wirtualnej.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>PYTANIE: Czy mogę używać wielu kont magazynu z urządzeniami Data Box Disk?
A.  Nie. Obecnie na potrzeby urządzeń Data Box Disk jest obsługiwane tylko jedno konto magazynu, ogólne lub klasyczne. Obsługiwane są obiekty blob zarówno w warstwie Gorąca, jak i Chłodna.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>PYTANIE: Jaki zestaw narzędzi jest dostępny dla moich danych na urządzeniach Data Box Disk?
A. Zestaw narzędzi dostępny na urządzeniu Data Box Disk zawiera trzy narzędzia:
 - **Narzędzie Data Box Disk odblokowywania**: Użyj tego narzędzia do odblokowania zaszyfrowanych dysków dostarczanych przez firmę Microsoft. W przypadku odblokowywania dysków za pomocą narzędzia musisz podać klucz dostępu udostępniony w zamówieniu urządzenia Data Box Disk w witrynie Azure Portal. 
 - **Data Box Disk narzędzia sprawdzania poprawności**: Użyj tego narzędzia do sprawdzania poprawności rozmiaru, formatu i nazw obiektów BLOB zgodnie z konwencjami nazewnictwa platformy Azure. Generuje również sumy kontrolne dla skopiowanych danych, które są następnie używane do weryfikowania danych przekazywanych do platformy Azure.
 - **Narzędzie Data Box Disk Split Copy**: Użyj tego narzędzia, jeśli używasz wielu dysków i masz duży zestaw danych, który musi być podzielony i kopiowany na wszystkich dyskach. To narzędzie jest obecnie dostępne dla systemu Windows. To narzędzie nie jest obsługiwane w przypadku dysków zarządzanych. To narzędzie sprawdza poprawność danych w miarę ich kopiowania, aby można było pominąć etap walidacji podczas korzystania z tego narzędzia.

Zestaw narzędzi jest dostępny dla systemów Windows i Linux. Zestaw narzędzi można pobrać w tym miejscu:
- [Pobierz zestaw narzędzi dla dysków Data Box Disk dla systemu Windows](https://aka.ms/databoxdisktoolswin) 
- [Pobierz zestaw narzędzi dla dysków Data Box Disk dla systemu Linux](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>PYTANIE: Czy można używać urządzenia Data Box Disk do przesyłania danych do usługi Azure Files, a następnie używać danych w usłudze Azure File Sync? 
A. Azure Files są obsługiwane przez Data Box Disk, ale nie będą dobrze działały w Azure File Sync. Metadane nie są zachowywane, jeśli dane plików są używane z Azure File Sync.


## <a name="verify-and-upload"></a>Weryfikowanie i przekazywanie

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>PYTANIE: Jak szybko mogę uzyskać dostęp do danych na platformie Azure po przełączeniu dysków do tyłu? 
A.  Dostęp do danych powinien być możliwy natychmiast po zmianie stanu zamówienia dla pozycji Kopiowanie danych na Ukończono.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>PYTANIE: Gdzie znajdują się moje dane na platformie Azure po przekazaniu?
A.  Jeśli skopiowano dane do folderów *BlockBlob* i *PageBlob* na dysku, w ramach konta usługi Azure Storage jest tworzony kontener dla każdego podfolderu w folderze *BlockBlob* i *PageBlob*. Jeśli pliki zostały skopiowane bezpośrednio z folderów *BlockBlob* i *PageBlob* , to pliki znajdują się w domyślnym kontenerze *$root* w ramach konta usługi Azure Storage. Po skopiowaniu danych do folderu w folderze *AzureFile* zostaje utworzony udział plików.

### <a name="q-i-just-noticed-that-i-didnt-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>PYTANIE: Zauważyliśmy, że nie obserwuję wymagań dotyczących nazewnictwa platformy Azure dla moich kontenerów. Czy przekazywanie moich danych na platformę Azure nie powiedzie się?
A. Wszystkie wielkie litery w nazwach kontenerów są automatycznie konwertowane na małe litery. Jeśli nazwy nie są zgodne na inne sposoby — na przykład zawierają znaki specjalne lub inne języki — przekazywanie zakończy się niepowodzeniem. Aby uzyskać więcej informacji, zobacz [Konwencje nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>PYTANIE: Jak mogę zweryfikować dane skopiowane na wiele urządzeń Data Box Disk?
A.  Po zakończeniu kopiowania danych możesz uruchomić plik `DataBoxDiskValidation.cmd` znajdujący się w folderze *DataBoxDiskImport* w celu wygenerowania sum kontrolnych na potrzeby walidacji. W przypadku wielu dysków musisz otworzyć okno polecenia i uruchomić to polecenie dla każdego z nich. Pamiętaj, że ta operacja może zająć dużo czasu (kilka godzin) w zależności od rozmiaru danych.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>PYTANIE: Co się stanie z moimi danymi po zwróceniu dysków?
A.  Po zakończeniu kopiowania danych na platformę Azure dane z dysków są bezpiecznie wymazywane zgodnie z wytycznymi NIST SP 800-88 Revision 1.  

### <a name="q-how-is-my-data-protected-during-transit"></a>PYTANIE: Jak moje dane są chronione w transporcie? 
A.  Dyski urządzenie Data Box są szyfrowane przy użyciu szyfrowania AES-128 funkcji BitLocker, a jeden klucz dostępu jest wymagany do odblokowania wszystkich dysków i dostępu do danych.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>PYTANIE: Czy muszę ponownie uruchomić walidację sumy kontrolnej, jeśli dodam więcej danych do urządzenia Data Box Disk?
A. Tak. Jeśli zdecydujesz się na walidację danych (zdecydowanie to zalecamy!), musisz ponownie uruchomić walidację po dodaniu danych do dysków.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>PYTANIE: Transferowane dane zajęły miejsce na wszystkich dyskach i muszę zamówić więcej dysków. Czy można szybko złożyć zamówienie?
A. W takim przypadku możesz sklonować poprzednie zamówienie. Klonowanie tworzy takie samo zamówienie i możliwe jest edytowanie jego szczegółów bez konieczności wpisywania adresu, danych kontaktowych i danych dotyczących powiadomień.

### <a name="q-i-copied-data-to-the-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-how-can-i-locate-it"></a>PYTANIE: Dane zostały skopiowane do folderu ManagedDisk Nie widzę żadnych dysków zarządzanych z grupą zasobów określoną dla dysków zarządzanych. Czy moje dane zostały przekazane na platformę Azure? Jak można go zlokalizować?
A. Tak. Dane zostały przekazane do platformy Azure, ale jeśli nie widzisz żadnych dysków zarządzanych z określonymi grupami zasobów, prawdopodobnie dane są nieprawidłowe. Jeśli stronicowe obiekty blob, blokowe obiekty blob, Azure Files lub dyski zarządzane są nieprawidłowe, zostaną umieszczone w następujących folderach:
 - Stronicowe obiekty blob będą przechodzić do kontenera blokowych obiektów blob, rozpoczynając od *databoxdisk-nieprawidłowy-PB-*.
 - Azure Files przejdzie do kontenera blokowych obiektów blob, rozpoczynając od *databoxdisk-nieprawidłowy-AF-*.
 - Dyski zarządzane przechodzą do kontenera blokowych obiektów blob, rozpoczynając od *databoxdisk-nieprawidłowy-MD-*.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [wymaganiami systemowymi Data Box Disk](data-box-disk-system-requirements.md).
- Zapoznaj się z [limitami usługi Data Box Disk](data-box-disk-limits.md).
- Szybko wdróż usługę [Azure Data Box Disk](data-box-disk-quickstart-portal.md) w witrynie Azure Portal.