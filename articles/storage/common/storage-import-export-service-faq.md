---
title: Często zadawane pytania dotyczące usługi Azure Import/Export | Microsoft Docs
description: Przeczytaj odpowiedzi na często zadawane pytania dotyczące usługi Azure Import Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 08c105bd8ae9b5e0667ef5279e6c9484c631bd48
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91399044"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Usługa Azure Import/Export: często zadawane pytania

Poniżej znajdują się pytania i odpowiedzi, które mogą wystąpić w przypadku przenoszenia danych do usługi Azure Storage za pomocą usług Azure Import/Export. Pytania i odpowiedzi są podzielone na następujące kategorie:

- Usługa Import/Export — informacje
- Przygotowywanie dysków do importowania/eksportowania
- Zadania importu/eksportu
- Dyski wysyłkowe
- Różne

## <a name="about-importexport-service"></a>Usługa Import/Export — informacje

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Czy mogę skopiować usługę Azure File Storage za pomocą usługi Azure Import/Export?

Tak. Usługa Azure Import/Export obsługuje importowanie do usługi Azure File Storage. W tej chwili nie obsługuje eksportu Azure Files.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Czy usługa Azure Import/Export jest dostępna dla subskrypcji CSP?

Tak. Usługa Azure Import/Export obsługuje subskrypcje dostawców rozwiązań w chmurze (CSP).

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-microsoft-365"></a>Czy mogę użyć usługi Azure Import/Export do kopiowania skrzynek pocztowych PST i danych programu SharePoint do Microsoft 365?

Tak. Aby uzyskać więcej informacji, przejdź do [omówienia importowania plików pst w organizacji](/microsoft-365/compliance/importing-pst-files-to-office-365).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Czy mogę użyć usługi Azure Import/Export do kopiowania kopii zapasowych w tryb offline do usługi Azure Backup?

Tak. Aby uzyskać więcej informacji, przejdź do [przepływu pracy kopii zapasowej offline w Azure Backup](../../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Czy mogę kupić dyski do zadań importu/eksportu z firmy Microsoft?

Nie. Musisz dostarczyć własne dyski do zadań importu i eksportu.

## <a name="preparing-disks-for-importexport"></a>Przygotowywanie dysków do importowania/eksportowania

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Czy mogę pominąć krok przygotowania dysku dla zadania importu? Czy mogę przygotować dysk bez kopiowania?

Nie. Wszystkie dyski używane do importowania danych muszą zostać przygotowane przy użyciu narzędzia Azure WAImportExport. Użyj narzędzia, aby skopiować dane na dysk.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Czy podczas tworzenia zadania eksportu trzeba wykonać dowolne przygotowanie dysku?

Nie. Zalecane jest pewne sprawdzanie. Aby sprawdzić wymaganą liczbę dysków, użyj polecenia PreviewExport narzędzia WAImportExport. Aby uzyskać więcej informacji, zobacz [Wyświetlanie podglądu użycia dysku dla zadania eksportu](https://msdn.microsoft.com/library/azure/dn722414.aspx). Polecenie ułatwia zapodglądanie użycia dysku dla wybranych obiektów BLOB na podstawie rozmiaru dysków, które mają być używane. Sprawdź również, czy można czytać i zapisywać dane z dysku twardego, który jest dostarczany dla zadania eksportu.

## <a name="importexport-jobs"></a>Zadania importu/eksportu

### <a name="can-i-cancel-my-job"></a>Czy mogę anulować moje zadanie?

Tak. Zadanie można anulować, gdy jego stan to **Tworzenie** lub **wysyłka**. Poza tymi etapami nie można anulować zadania i jest ono kontynuowane do momentu zakończenia ostatniego etapu.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Jak długo można wyświetlić stan ukończonych zadań w Azure Portal?

Można wyświetlić stan ukończonych zadań przez maksymalnie 90 dni. Ukończone zadania są usuwane po 90 dniach.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Co mam zrobić, jeśli chcę zaimportować lub wyeksportować więcej niż 10 dysków?

Jedno zadanie importu lub eksportu może odwoływać się tylko do 10 dysków w jednym zadaniu. Aby dostarczyć więcej niż 10 dysków, należy utworzyć wiele zadań. Dyski skojarzone z tym samym zadaniem muszą być dostarczane razem z tym samym pakietem.
Aby uzyskać więcej informacji i wskazówki, gdy pojemność danych obejmuje wiele zadań importowania dysku, skontaktuj się z pomoc techniczna firmy Microsoft.

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>Przekazany obiekt BLOB wskazuje stan "Dzierżawa wygasła". Co mam zrobić?

Możesz zignorować pole "wygasła dzierżawa". Import/Export pobiera dzierżawę obiektu BLOB podczas przekazywania, aby upewnić się, że żaden inny proces nie może zaktualizować obiektu BLOB równolegle. Dzierżawa wygasła, oznacza, że import/eksport nie jest już przekazywany do niego, a obiekt BLOB jest dostępny do użytku.

## <a name="shipping-disks"></a>Dyski wysyłkowe

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Jaka jest maksymalna liczba dysków twardych w jednej wysyłce?

Nie ma żadnego limitu liczby HDD w jednej wysyłce. Jeśli dyski należą do wielu zadań, zalecamy:

- Oznacz dyski odpowiednimi nazwami zadań.
- Zaktualizuj zadania o numerze śledzenia z sufiksem-1,-2 itd.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Czy mogę dołączyć coś innego niż dysk twardy w moim pakiecie?

Wyślij tylko dyski twarde w pakiecie wysyłkowym. Nie dołączaj elementów, takich jak kable zasilacza lub kable USB.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Czy muszę dostarczyć moje dyski przy użyciu FedEx lub DHL?

Możesz dostarczać dyski do centrum danych platformy Azure przy użyciu dowolnego znanego operatora, takiego jak FedEx, DHL, UPS lub US Postal Service. Jednak dla wysyłki zwrotnej dysków z centrum danych należy podać:

- Numer konta FedEx w Stanach Zjednoczonych i UE, lub
- Numer konta DHL w regionach Azji i Australii.

> [!NOTE]
> Centra danych w Indiach wymagają litery deklaracji na nagłówku listowym (Challan dostarczania) w celu zwrócenia dysków. Aby rozmieścić wymagane wejście wejścia, należy również zaksięgować pobranie z wybranym przewoźnikiem i udostępnić szczegóły w centrum danych.

### <a name="are-there-any-restrictions-with-shipping-and-returning-my-drive-internationally"></a>Czy istnieją jakieś ograniczenia związane z wysyłką i zwróceniem mojego dysku na Międzynarodowy?

Należy pamiętać, że wysyłany nośnik fizyczny może wymagać przekroczenia granic międzynarodowych. Użytkownik jest odpowiedzialny za zapewnienie, że nośnik fizyczny i dane zostaną zaimportowane i/lub wyeksportowane zgodnie z obowiązującymi przepisami. Przed wysłaniem nośnika fizycznego należy skontaktować się z doradcami, aby upewnić się, że nośnik i dane mogą zostać legalnie wysłane do określonego centrum danych. Pomoże to zapewnić firmie Microsoft w odpowiednim czasie.

Po zakończeniu przekazywania proces zwracania dysków na adres międzynarodowy może trwać dłużej niż w przypadku typowych 2-3 dni wymaganych do wysyłki lokalnej. Na etapie wymienionym w Azure Portal jako pakowanie, zespół urządzenie Data Box zapewnia, że poprawna dokumentacja zapewnia, że wysyłka spełnia różne międzynarodowe wymagania dotyczące importu i eksportu.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Czy istnieją jakieś specjalne wymagania dotyczące dostarczania dysków do centrum danych?

Wymagania te zależą od konkretnych ograniczeń centrum danych platformy Azure.

- Istnieje kilka witryn, takich jak Australia, Niemcy i Południowe Zjednoczone Królestwo, które wymagają, aby numer przychodzący Microsoft centrum danych został zapisany w parceli ze względów bezpieczeństwa. Przed dostarczeniem dysków lub dysków do centrum danych skontaktuj się z pomocą techniczną Azure DataBox Operations ( adbops@microsoft.com ), aby uzyskać tę liczbę. Bez tej liczby pakiet zostanie odrzucony.
- Centra danych w Indiach wymagają szczegółowych informacji o sterowniku, takich jak karta identyfikatora instytucji rządowych lub numer dowodu. (na przykład PANORAMowanie, AADHAR, DL), imię i nazwisko, kontakt i numer płyty samochodu, aby przejść do wpisu bramy. Aby uniknąć opóźnień dostarczania, należy poinformować przewoźnika o tych wymaganiach.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Podczas tworzenia zadania adres wysyłkowy jest lokalizacją inną niż lokalizacja konta magazynu. Co mam zrobić?

Niektóre lokalizacje kont magazynu są mapowane na alternatywne lokalizacje dostawy. Wcześniej dostępne lokalizacje wysyłkowe mogą być również tymczasowo mapowane na alternatywne lokalizacje. Zawsze sprawdzaj adres wysyłkowy podany podczas tworzenia zadania przed wysłaniem dysków.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Gdy wysyłam mój dysk, zostanie wyświetlony monit dotyczący adresu kontaktowego centrum danych i numeru telefonu. Co mam udostępnić?

Numer telefonu i adres kontrolera domeny są udostępniane w ramach tworzenia zadań.

## <a name="miscellaneous"></a>Różne

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Co się stanie, jeśli przypadkowo wyślę dysk twardy, który nie jest zgodny z obsługiwanymi wymaganiami?

Centrum danych Azure zwróci dysk, który nie jest zgodny z obsługiwanymi wymaganiami. Jeśli tylko niektóre dyski w pakiecie spełniają wymagania dotyczące pomocy technicznej, te dyski zostaną przetworzone, a dyski, które nie spełniają wymagań, zostaną zwrócone do użytkownika.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Czy usługa sformatuje dyski przed ich zwróceniem?

Nie. Wszystkie dyski są szyfrowane za pomocą funkcji BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Jak uzyskać dostęp do danych importowanych przez tę usługę?

Użyj Azure Portal lub [Eksplorator usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) , aby uzyskać dostęp do danych na koncie usługi Azure Storage.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Po zakończeniu importowania jak wyglądają moje dane na koncie magazynu? Czy moja hierarchia katalogów jest zachowywana?

Podczas przygotowywania dysku twardego dla zadania importowania, miejsce docelowe jest określane przez pole DstBlobPathOrPrefix w pliku CSV zestawu danych. Jest to kontener docelowy na koncie magazynu, do którego kopiowane są dane z dysku twardego. W tym kontenerze docelowym katalogi wirtualne są tworzone dla folderów z dysku twardego, a obiekty blob są tworzone dla plików.

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Czy na dysku znajdują się pliki, które już istnieją na moim koncie magazynu, czy usługa zastępuje istniejące obiekty blob lub pliki?

Zależy od. Podczas przygotowywania dysku można określić, czy pliki docelowe mają być zastępowane lub ignorowane przy użyciu pola w pliku CSV zestawu danych o nazwie Dyspozycja: <zmienić nazwę | nie zastępuj | zastąpić>. Domyślnie usługa zmienia nazwy nowych plików zamiast zastępować istniejące obiekty blob lub pliki.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Czy narzędzie WAImportExport jest zgodne z 32-bitowymi systemami operacyjnymi?

Nie. Narzędzie WAImportExport jest zgodne tylko z 64-bitowymi systemami operacyjnymi Windows. Aby uzyskać pełną listę obsługiwanych systemów operacyjnych, przejdź do obszaru [obsługiwane systemy operacyjne](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements).

### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Jaki jest maksymalny rozmiar obiektu BLOB bloku i rozmiaru obiektu BLOB obsługiwanego przez usługę Azure Import/Export?

- Maksymalny rozmiar bloku obiektu BLOB wynosi około 4.768 TB lub 5 000 000 MB.
- Maksymalny rozmiar obiektu BLOB strony to 8TB.

### <a name="does-azure-importexport-support-aes-256-encryption"></a>Czy usługa Azure Import/Export obsługuje szyfrowanie AES-256?

Tak. Usługa Azure Import/Export korzysta z szyfrowania AES-256 funkcji BitLocker.

## <a name="next-steps"></a>Następne kroki

* [Co to jest usługa Azure Import/Export?](storage-import-export-service.md)
