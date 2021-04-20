---
title: Co nowego w programie Azure NetApp Files | Microsoft Docs
description: Zawiera podsumowanie informacji o najnowszych nowych funkcjach i ulepszeniach Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/19/2021
ms.author: b-juche
ms.openlocfilehash: 3c6da2137f2db43284ce7a533ff763e9ef157f35
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726650"
---
# <a name="whats-new-in-azure-netapp-files"></a>Co nowego w programie Azure NetApp Files

Azure NetApp Files są regularnie aktualizowane. Ten artykuł zawiera podsumowanie najnowszych nowych funkcji i ulepszeń. 

## <a name="april-2021"></a>Kwiecień 2021 r.

* [Szyfrowanie protokołu SMB3](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (wersja zapoznawcza) 

    Teraz można włączyć szyfrowanie protokołu SMB3 na Azure NetApp Files woluminach SMB i dwóch protokołów. Ta funkcja umożliwia szyfrowanie danych SMB3 w locie przy użyciu algorytmu AES-CCM w przypadku szyfrowania [SMB 3.0 i algorytmu AES-GCM w połączeniach SMB 3.1.1.](/windows-server/storage/file-server/file-server-smb-overview#features-added-in-smb-311-with-windows-server-2016-and-windows-10-version-1607) Klienci SMB, którzy nie korzystali z szyfrowania SMB3, nie będą mogli uzyskać dostępu do tego woluminu. Dane w spoczynku są szyfrowane niezależnie od tego ustawienia. Szyfrowanie SMB dodatkowo zwiększa bezpieczeństwo. Może to jednak mieć wpływ na klienta (obciążenie procesora CPU związane z szyfrowaniem i odszyfrowywaniem komunikatów). Może to również mieć wpływ na wykorzystanie zasobów magazynu (zmniejszenie przepływności). Przed wdrożeniem obciążeń w środowisku produkcyjnym należy przetestować wpływ na wydajność szyfrowania aplikacji.

* [Active Directory Domain Services (ADDS) Mapowanie użytkowników LDAP przy użyciu rozszerzonych grup NFS](configure-ldap-extended-groups.md) (wersja zapoznawcza)   

    Domyślnie program Azure NetApp Files maksymalnie 16 identyfikatorów grup podczas obsługi poświadczeń użytkownika systemu plików NFS zgodnie z definicją w dokumencie [RFC 5531.](https://tools.ietf.org/html/rfc5531) Dzięki tej nowej możliwości można teraz zwiększyć maksymalną liczbę do 1024, jeśli użytkownicy są członkami więcej niż domyślna liczba grup. W celu obsługi tej funkcji woluminy NFS można teraz również dodać do protokołu ADDS LDAP, co umożliwia użytkownikom LDAP usługi Active Directory z rozszerzonymi wpisami grup (z maksymalnie 1024 grupami) dostęp do woluminu. 

## <a name="march-2021"></a>Marzec 2021 r.
 
* [Udziały ciągłej dostępności SMB (ca)](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (wersja zapoznawcza)  

    Przezroczysty sposób pracy awaryjnej SMB umożliwia operacje konserwacji w usłudze Azure NetApp Files bez przerywania łączności z aplikacjami serwera przechowującymi dane i uzyskiwaniem do nich dostępu w woluminach SMB. Aby obsługiwać przezroczysty tryb failover SMB, usługa Azure NetApp Files obsługuje teraz opcję udziałów ciągłej dostępności SMB do użycia z aplikacjami SQL Server przez SMB uruchomionymi na maszynach wirtualnych platformy Azure. Ta funkcja jest obecnie obsługiwana w systemie Windows SQL Server. System Linux SQL Server nie jest obecnie obsługiwany. Włączenie tej funkcji zapewnia znaczne SQL Server wydajności oraz skalę i korzyści ekonomiczne dla wdrożeń pojedynczego wystąpienia, Always-On klastra trybu failover i Always-On [grupy dostępności.](azure-netapp-files-solution-architectures.md#sql-server) Zobacz [Korzyści z używania Azure NetApp Files wdrażania SQL Server usługi](solutions-benefits-azure-netapp-files-sql-server.md).

* [Automatyczna zmiana rozmiaru woluminu docelowego replikacji między regionami](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)

    W relacji replikacji między regionami rozmiar woluminu docelowego jest automatycznie zmieniany na podstawie rozmiaru woluminu źródłowego. W związku z tym nie trzeba oddzielnie zmieniać rozmiaru woluminu docelowego. To zachowanie automatycznego zmiany rozmiaru ma zastosowanie, gdy woluminy znajdują się w aktywnej relacji replikacji lub gdy komunikacja równorzędna replikacji zostanie przerwana podczas operacji ponownej synchronizacji. Aby ta funkcja działała, należy zapewnić wystarczającą ilość miejsca w pulach pojemności zarówno dla woluminu źródłowego, jak i docelowego.

## <a name="december-2020"></a>Grudzień 2020 r.

* [Narzędzie migawki spójne z aplikacją platformy Azure](azacsnap-introduction.md) (wersja zapoznawcza)    

    Azure Application Consistent Snapshot Tool (AzAcSnap) to narzędzie wiersza polecenia, które umożliwia uproszczenie ochrony danych baz danych innych firm (SAP HANA) w środowiskach systemu Linux (na przykład SUSE i RHEL).   

    Narzędzie AzAcSnap wykorzystuje funkcje migawki woluminów i replikacji w Azure NetApp Files i dużym wystąpieniu platformy Azure. Zapewnia następujące korzyści:

    * Ochrona danych spójna na aplikacji 
    * Zarządzanie wykazem baz danych 
    * *Ochrona woluminów ad hoc* 
    * Klonowanie woluminów magazynu 
    * Obsługa odzyskiwania po awarii 

## <a name="november-2020"></a>Listopad 2020 r.

* [Przywracanie migawki](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)

    Funkcja przywracania migawek umożliwia szybkie przywrócenie woluminu do stanu, w którym był w momencie utworzenia określonej migawki. W większości przypadków przywracanie woluminu jest znacznie szybsze niż przywracanie poszczególnych plików z migawki do aktywnego systemu plików. Jest to również bardziej wydajne miejsce w porównaniu do przywracania migawki do nowego woluminu.

## <a name="september-2020"></a>Wrzesień 2020

* [Azure NetApp Files replikacji między regionami](cross-region-replication-introduction.md) (wersja zapoznawcza)

  Azure NetApp Files obsługuje teraz replikację między regionami. Ta nowa funkcja odzyskiwania po awarii umożliwia szybkie i ekonomiczne replikowanie woluminów usługi Azure NetApp Files z jednego regionu świadczenia usługi Azure do innego, chroniąc dane przed nieprzewidzianymi awariami regionalnymi. Azure NetApp Files replikacji między regionami korzysta z technologii NetApp SnapMirror® technologii; Tylko zmienione bloki są wysyłane przez sieć w skompresowanym, wydajnym formacie. Ta zastrzeżona technologia minimalizuje ilość danych wymaganych do replikacji między regionami, co pozwala zmniejszyć koszty transferu danych. Skraca również czas replikacji, dzięki czemu można osiągnąć mniejszy cel punktu przywracania (RPO, Restore Point Objective).

* [Ręczna pula pojemności QoS](manual-qos-capacity-pool-introduction.md) (wersja zapoznawcza)  

    W ręcznej puli pojemności QoS można niezależnie przypisać pojemność i przepływność dla woluminu. Łączna przepływność wszystkich woluminów utworzonych za pomocą ręcznej puli pojemności QoS jest ograniczona przez łączną przepływność puli. Zależy to od kombinacji rozmiaru puli i przepływności na poziomie usługi. Alternatywnie typ [QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) puli pojemności może być automatyczny (automatyczny), co jest ustawieniem domyślnym. W puli automatycznej pojemności QoS przepływność jest przypisywana automatycznie do woluminów w puli proporcjonalnie do limitu przydziału rozmiaru przypisanego do woluminów.

* [Podpisywanie LDAP](azure-netapp-files-create-volumes-smb.md) (wersja zapoznawcza)   

    Azure NetApp Files obsługuje teraz podpisywanie LDAP w celu bezpiecznego wyszukiwania LDAP między usługą Azure NetApp Files a kontrolerami domeny określonymi przez Active Directory Domain Services użytkownika. Ta funkcja jest obecnie w wersji zapoznawczej.

* [Szyfrowanie AES dla uwierzytelniania usługi AD](azure-netapp-files-create-volumes-smb.md) (wersja zapoznawcza)

    Azure NetApp Files obsługuje teraz szyfrowanie AES na połączeniu LDAP z kontrolerem domeny, aby włączyć szyfrowanie AES dla woluminu SMB. Ta funkcja jest obecnie w wersji zapoznawczej. 

* Nowe [metryki:](azure-netapp-files-metrics.md)   

    * Nowe metryki woluminu: 
        * *Rozmiar przydzielonego woluminu:* aprowizowany rozmiar woluminu
    * Nowe metryki puli: 
        * *Przydzielony rozmiar puli:* aprowizowany rozmiar puli 
        * *Łączny rozmiar migawki dla puli:* suma rozmiaru migawki ze wszystkich woluminów w puli

## <a name="july-2020"></a>Lipiec 2020 r.

* [Wolumin z podwójnym protokołem (NFSv3 i SMB)](create-volumes-dual-protocol.md)

    Teraz można utworzyć wolumin Azure NetApp Files, który umożliwia równoczesny dostęp za pomocą dwóch protokołów (NFS v3 i SMB) z obsługą mapowania użytkowników LDAP. Ta funkcja umożliwia stosowanie przypadków użycia, w których może wystąpić obciążenie oparte na systemie Linux, które generuje i przechowuje dane w Azure NetApp Files woluminie. W tym samym czasie pracownicy muszą używać klientów systemu Windows i oprogramowania do analizowania nowo wygenerowanych danych z tego samego Azure NetApp Files woluminu. Funkcja jednoczesnego dostępu za pomocą dwóch protokołów usuwa konieczność kopiowania danych generowanych przez obciążenie do oddzielnego woluminu przy użyciu innego protokołu na potrzeby analizy po analizie, oszczędzania kosztów magazynu i czasu działania. Ta funkcja jest bezpłatna (normalny [koszt Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) magazynu nadal obowiązuje) i jest ogólnie dostępna. Dowiedz się więcej z [dokumentacji jednoczesnego dostępu z dwoma protokołami.](create-volumes-dual-protocol.MD)

* [Szyfrowanie Kerberos systemu plików NFS w wersji 4.1 podczas przesyłania](configure-kerberos-encryption.MD)

    Azure NetApp Files obsługuje teraz szyfrowanie klienta NFS w trybach Kerberos (krb5, krb5i i krb5p) z szyfrowaniem AES-256, zapewniając dodatkowe zabezpieczenia danych. Ta funkcja jest bezpłatna (normalny [koszt Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) magazynu nadal obowiązuje) i jest ogólnie dostępna. Dowiedz się więcej z dokumentacji dotyczącej szyfrowania [Kerberos systemu plików NFS w wersji 4.1.](configure-kerberos-encryption.MD)

* [Dynamiczna zmiana poziomu usługi woluminu](dynamic-change-volume-service-level.MD)

    Chmura zapewnia elastyczność wydatków na rozwiązania IT. Teraz możesz zmienić poziom usługi istniejącego woluminu Azure NetApp Files, przenosząc wolumin do innej puli pojemności, która używa poziomu usługi dla woluminu. Ta zmiana poziomu usług w miejscu dla woluminu nie wymaga migracji danych. Nie ma to również wpływu na dostęp płaszczyzny danych do woluminu. Możesz zmienić istniejący wolumin, aby użyć wyższego poziomu usługi w celu lepszej wydajności lub użyć niższego poziomu usługi w celu optymalizacji kosztów. Ta funkcja jest bezpłatna (normalny [Azure NetApp Files magazynu](https://azure.microsoft.com/pricing/details/netapp/) nadal obowiązuje) i jest obecnie dostępna w publicznej wersji zapoznawczej. Możesz zarejestrować się, aby korzystać z wersji zapoznawczej funkcji, korzystając z dokumentacji zmiany dynamicznego [poziomu usług woluminu](dynamic-change-volume-service-level.md).

* [Zasady migawek woluminów](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) (wersja zapoznawcza) 

    Azure NetApp Files umożliwia tworzenie migawek woluminów do punktu w czasie. Teraz możesz utworzyć zasady migawek, aby Azure NetApp Files automatycznie tworzyć migawki woluminów z dostępem do wybranej częstotliwości. Możesz zaplanować tworzenie migawek w cyklach godzinowych, codziennych, tygodniowych lub miesięcznych. Można również określić maksymalną liczbę migawek do utrzymania w ramach zasad migawek. Ta funkcja jest bezpłatna (normalny [koszt magazynu Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) nadal obowiązuje) i jest obecnie dostępna w wersji zapoznawczej. Możesz zarejestrować się, aby korzystać z wersji zapoznawczej funkcji, korzystając z [dokumentacji zasad migawek woluminu.](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)

* [Zasady eksportowania dostępu do głównego systemu plików NFS](azure-netapp-files-configure-export-policy.md)

    Azure NetApp Files teraz pozwala określić, czy konto główne może uzyskać dostęp do woluminu. 

* [Ukryj ścieżkę migawki](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    Azure NetApp Files teraz określić, czy użytkownik może zobaczyć katalog `.snapshot` (klienci NFS) lub folder (klienci SMB) na zainstalowanym woluminie i uzyskać do `~snapshot` niego dostęp.

## <a name="may-2020"></a>Maj 2020 r.

* [Użytkownicy zasad kopii zapasowych](create-active-directory-connections.md) (wersja zapoznawcza)

    Azure NetApp Files można uwzględnić dodatkowe konta, które wymagają podniesionych uprawnień do konta komputera utworzonego do użytku z Azure NetApp Files. Określone konta będą mogły zmieniać uprawnienia systemu plików NTFS na poziomie pliku lub folderu. Można na przykład określić konto usługi bez uprawnień używane do migrowania danych do udziału plików SMB w Azure NetApp Files. Funkcja użytkownicy zasad kopii zapasowych jest obecnie dostępna w wersji zapoznawczej.

## <a name="next-steps"></a>Następne kroki
* [Co to jest usługa Azure NetApp Files](azure-netapp-files-introduction.md)
* [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) 
