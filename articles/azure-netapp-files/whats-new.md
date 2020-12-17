---
title: Co nowego w Azure NetApp Files | Microsoft Docs
description: Zawiera podsumowanie dotyczące najnowszych nowych funkcji i ulepszeń Azure NetApp Files.
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
ms.date: 12/04/2020
ms.author: b-juche
ms.openlocfilehash: bba3dce2a2a18888cb88f4cf8b33cd48d6a4cd69
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631163"
---
# <a name="whats-new-in-azure-netapp-files"></a>Co nowego w Azure NetApp Files

Azure NetApp Files jest regularnie aktualizowana. Ten artykuł zawiera podsumowanie dotyczące najnowszych nowych funkcji i ulepszeń. 

## <a name="december-2020"></a>Grudzień 2020 r.

* [Narzędzie do tworzenia migawek spójnych aplikacji Azure](azacsnap-introduction.md) (publiczna wersja zapoznawcza)    

    Narzędzie do tworzenia migawek spójnych w aplikacji Azure (AzAcSnap) to narzędzie wiersza polecenia, które umożliwia uproszczenie ochrony danych dla baz danych innych firm (SAP HANA) w środowiskach systemu Linux (na przykład SUSE i RHEL).   

    AzAcSnap korzysta z funkcji migawek woluminu i replikacji w Azure NetApp Files i dużym wystąpieniu platformy Azure. Zapewnia następujące korzyści:

    * Ochrona danych spójna na poziomie aplikacji 
    * Zarządzanie katalogiem bazy danych 
    * Ochrona woluminów *ad hoc* 
    * Klonowanie woluminów magazynu 
    * Obsługa odzyskiwania po awarii 

## <a name="november-2020"></a>Listopad 2020 r.

* [Przywracanie migawek](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)

    Funkcja przywracania migawek umożliwia szybkie przywrócenie woluminu do stanu, w którym został utworzony. W większości przypadków przywrócenie woluminu jest znacznie szybsze niż przywrócenie pojedynczych plików z migawki do aktywnego systemu plików. Jest to również bardziej wydajne miejsce w porównaniu do przywracania migawki do nowego woluminu.

## <a name="september-2020"></a>Wrzesień 2020

* [Azure NetApp Files replikacji między regionami](cross-region-replication-introduction.md) (publiczna wersja zapoznawcza)

  Azure NetApp Files obsługuje teraz replikację między regionami. Dzięki tej nowej możliwości odzyskiwania po awarii możesz replikować Azure NetApp Files z jednego regionu platformy Azure do innego w szybki i ekonomiczny sposób, chroniąc dane przed nieprzewidywalnymi awariami regionalnymi. Azure NetApp Files replikacja między regionami korzysta z technologii NetApp SnapMirror® tylko zmienione bloki są wysyłane przez sieć w skompresowanym, wydajnym formacie. Ta technologia własnościowa minimalizuje ilość danych wymaganych do replikacji w regionach, co pozwala zaoszczędzić koszty transferu danych. Skraca również czas replikacji, dzięki czemu można osiągnąć mniejszy cel punktu przywracania (RPO).

* [Ręczna Pula pojemności QoS](manual-qos-capacity-pool-introduction.md) (wersja zapoznawcza)  

    W ręcznej puli pojemności usługi QoS można przypisać pojemność i przepływność dla woluminu niezależnie. Całkowita przepływność wszystkich woluminów utworzonych z ręczną pulą pojemności usługi QoS jest ograniczona przez łączną przepływność puli. Jest ona określana na podstawie kombinacji rozmiaru puli i przepływności na poziomie usług. Alternatywnie [typ QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) puli pojemności może być automatyczny (automatyczny), co jest ustawieniem domyślnym. W puli pojemności automatycznej usługi QoS przepływność jest przypisywana automatycznie do woluminów w puli proporcjonalnie do przydziału rozmiaru przypisanego do woluminów.

* [Podpisywanie LDAP](azure-netapp-files-create-volumes-smb.md) (wersja zapoznawcza)   

    Azure NetApp Files obsługuje teraz podpisywanie LDAP dla bezpiecznych wyszukiwań LDAP między usługą Azure NetApp Files i Active Directory Domain Services kontrolerami domeny określony przez użytkownika. Ta funkcja jest obecnie w wersji zapoznawczej.

* [Szyfrowanie AES dla uwierzytelniania usługi AD](azure-netapp-files-create-volumes-smb.md) (wersja zapoznawcza)

    Azure NetApp Files teraz obsługuje szyfrowanie AES dla połączenia LDAP z kontrolerem domeny w celu włączenia szyfrowania AES dla woluminu SMB. Ta funkcja jest obecnie w wersji zapoznawczej. 

* Nowe [metryki](azure-netapp-files-metrics.md):   

    * Nowe metryki woluminów: 
        * *Przydzielony rozmiar woluminu*: rozmiar zainicjowany woluminu
    * Nowe metryki puli: 
        * *Rozmiar przydzielony puli*: rozmiar zainicjowany puli 
        * *Łączny rozmiar migawki dla puli*: suma rozmiaru migawki ze wszystkich woluminów w puli

## <a name="july-2020"></a>Lipiec 2020 r.

* [Dwuprotokołowy wolumin (NFSv3 i SMB)](create-volumes-dual-protocol.md)

    Teraz można utworzyć wolumin Azure NetApp Files, który umożliwia dostęp do podwójnego protokołu (NFS v3 i SMB) z obsługą mapowania użytkowników LDAP. Ta funkcja umożliwia przypadki użycia, w których może istnieć obciążenie oparte na systemie Linux, które generuje i przechowuje dane w woluminie Azure NetApp Files. W tym samym czasie pracownicy muszą korzystać z klientów i oprogramowania z systemem Windows w celu przeanalizowania nowo wygenerowanych danych z tego samego woluminu Azure NetApp Files. Funkcja równoczesnego dostępu do podwójnego protokołu eliminuje konieczność kopiowania danych generowanych przez obciążenia do oddzielnego woluminu przy użyciu innego protokołu na potrzeby po analizie, oszczędności kosztów magazynu i czasu działania. Ta funkcja jest bezpłatna (normalna Azure NetApp Files nadal dotyczy [kosztów magazynu](https://azure.microsoft.com/pricing/details/netapp/) ) i jest ogólnie dostępna. Dowiedz się więcej z [dwuprotokołowej dokumentacji dostępu](create-volumes-dual-protocol.MD).

* [Szyfrowanie Kerberos systemu plików NFS w trakcie przesyłania](configure-kerberos-encryption.MD)

    Azure NetApp Files obsługuje teraz szyfrowanie klienta NFS w trybach protokołu Kerberos (krb5, krb5i i Krb5p) przy użyciu szyfrowania AES-256, zapewniając dodatkowe bezpieczeństwo danych. Ta funkcja jest bezpłatna (normalna Azure NetApp Files nadal dotyczy [kosztów magazynu](https://azure.microsoft.com/pricing/details/netapp/) ) i jest ogólnie dostępna. Dowiedz się więcej z [dokumentacji szyfrowania Kerberos systemu plików NFS](configure-kerberos-encryption.MD)w wersji 4.1.

* [Zmiana poziomu usługi dynamicznego woluminu](dynamic-change-volume-service-level.MD)

    Usługa Cloud niesie obietnice zwiększenia zapewnia elastyczność w zakresie wydatków IT. Teraz można zmienić poziom usługi istniejącego woluminu Azure NetApp Files, przenosząc wolumin do innej puli pojemności, która używa żądanego poziomu usługi dla woluminu. Ta zmiana poziomu usługi w miejscu dla woluminu nie wymaga migracji danych. Nie ma to również wpływu na dostęp do woluminu płaszczyzny danych. Możesz zmienić istniejący wolumin, aby użyć wyższego poziomu usługi w celu uzyskania lepszej wydajności lub użyć niższego poziomu usługi do optymalizacji kosztu. Ta funkcja jest bezpłatna (normalna Azure NetApp Files nadal dotyczy [kosztów magazynu](https://azure.microsoft.com/pricing/details/netapp/) ) i jest obecnie dostępna w publicznej wersji zapoznawczej. Możesz zarejestrować się w wersji zapoznawczej funkcji, postępując zgodnie z [dokumentacją dotyczącą zmiany poziomu usługi woluminu dynamicznego](dynamic-change-volume-service-level.md).

* [Zasady migawek woluminów](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) (wersja zapoznawcza) 

    Azure NetApp Files umożliwia tworzenie migawek do punktu w czasie dla woluminów. Teraz można utworzyć zasady migawek, aby Azure NetApp Files automatycznie tworzyć migawki woluminów z wybranej częstotliwości. Można zaplanować tworzenie migawek w cyklu co godzinę, codziennie, co tydzień lub co miesiąc. Można również określić maksymalną liczbę migawek do zachowania w ramach zasad migawek. Ta funkcja jest bezpłatna (normalne [Azure NetApp Files koszty magazynu](https://azure.microsoft.com/pricing/details/netapp/) ) i jest obecnie dostępna w wersji zapoznawczej. Możesz zarejestrować się w celu uzyskania podglądu funkcji, postępując zgodnie z [dokumentacją zasad migawek woluminu](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

* [Zasady eksportowania dostępu głównego systemu plików NFS](azure-netapp-files-configure-export-policy.md)

    Azure NetApp Files teraz pozwala określić, czy konto główne ma dostęp do woluminu. 

* [Ukryj ścieżkę migawki](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    Azure NetApp Files teraz pozwala określić, czy użytkownik może zobaczyć i uzyskać dostęp do `.snapshot` katalogu (klientów NFS) lub `~snapshot` folderu (klientów SMB) na woluminie zainstalowanym.

## <a name="may-2020"></a>Maj 2020 r.

* [Użytkownicy zasad tworzenia kopii zapasowych](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection) (wersja zapoznawcza)

    Azure NetApp Files umożliwia uwzględnienie dodatkowych kont, które wymagają podwyższonego poziomu uprawnień do konta komputera utworzonego do użytku z Azure NetApp Files. Określone konta będą mogły zmienić uprawnienia systemu plików NTFS na poziomie pliku lub folderu. Na przykład można określić konto usługi bez uprawnień używane do migrowania danych do udziału plików SMB w Azure NetApp Files. Funkcja użytkownicy zasad kopii zapasowych jest obecnie w wersji zapoznawczej.

## <a name="next-steps"></a>Następne kroki
* [Co to jest usługa Azure NetApp Files](azure-netapp-files-introduction.md)
* [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) 
