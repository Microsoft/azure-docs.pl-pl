---
title: Wprowadzenie do usługi Azure Files | Microsoft Docs
description: Omówienie usługi Azure Files umożliwiającej tworzenie i używanie udziałów plików sieciowych w chmurze z wykorzystaniem standardowego protokołu SMB.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0dcd763240205bd396fc8cd0301c2046098473b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98070137"
---
# <a name="what-is-azure-files"></a>Co to jest usługa Azure Files?
Azure Files oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem [protokołu Server Message Block (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) lub [protokołu sieciowego systemu plików (NFS)](https://en.wikipedia.org/wiki/Network_File_System). Udziały plików platformy Azure można instalować współbieżnie przy użyciu wdrożeń w chmurze lub lokalnych. Azure Files udziały plików SMB są dostępne z klientów systemów Windows, Linux i macOS. Azure Files udziały plików NFS są dostępne z poziomu klientów z systemem Linux lub macOS. Ponadto Azure Files udziały plików SMB mogą być buforowane na serwerach z systemem Windows z Azure File Sync, aby uzyskać szybki dostęp w sąsiedztwie miejsca, w którym są używane dane.

## <a name="videos"></a>Filmy wideo
| Wprowadzenie Azure File Sync | Azure Files z synchronizacją (zapłon 2019)  |
|-|-|
| [![Zrzut ekranu przedstawiający wprowadzenie Azure File Sync wideo — kliknij, aby odtworzyć!](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Zrzut ekranu przedstawiający Azure Files z prezentacją synchronizacji — kliknij, aby odtworzyć!](./media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

Poniżej przedstawiono kilka filmów wideo dotyczących typowych przypadków użycia Azure Files:
* [Zastąp serwer plików nieserwerowym udziałem plików platformy Azure](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
* [Wprowadzenie do kontenerów profilów FSLogix na Azure Files na pulpicie wirtualnym systemu Windows wykorzystującym uwierzytelnianie usługi AD](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="why-azure-files-is-useful"></a>Na czym polega przydatność usługi Azure Files
Udziały plików platformy Azure mogą być używane w następujących celach:

* **Zastępowanie lub uzupełnianie lokalnych serwerów plików**:  
    Usługi Azure Files można użyć w celu całkowitego zastąpienia lub uzupełnienia tradycyjnych lokalnych serwerów plików lub urządzeń NAS. W popularnych systemach operacyjnych, takich jak Windows, macOS i Linux, udziały plików platformy Azure można zainstalować bezpośrednio bez względu na to, gdzie się one znajdują. Udziały plików usługi Azure File SMB mogą być również replikowane przy użyciu Azure File Sync do serwerów z systemem Windows, lokalnie lub w chmurze, w celu zapewnienia wydajności i rozproszonego buforowania danych, w których jest używana. W przypadku korzystania z najnowszej wersji [Azure Files uwierzytelniania](storage-files-active-directory-overview.md)za pomocą usługi Azure File przydziały plików SMB mogą nadal współdziałać z usługą AD hostowaną lokalnie na potrzeby kontroli dostępu. 

* **Migrowanie aplikacji metodą „lift and shift”**:  
    Usługa Azure File Storage ułatwia migrowanie do chmury metodą „lift and shift” tych aplikacji, które oczekują udziału plików do przechowywania danych aplikacji lub danych użytkownika. Usługa Azure Files umożliwia zarówno klasyczny scenariusz migracji metodą „lift and shift”, w którym aplikacja i jej dane są przenoszone do platformy Azure, jak i scenariusz hybrydowy migracji „lift and shift”, w którym dane aplikacji są przenoszone do usługi Azure Files, a aplikacja dalej działa lokalnie. 

* **Uproszczenie programowania aplikacji w chmurze**:  
    Usługi Azure Files można także używać na wiele różnych sposobów w celu uproszczenia nowych projektów projektowania aplikacji w chmurze. Na przykład:
    * **Współdzielone ustawienia aplikacji**:  
        W typowym wdrożeniu aplikacji rozproszonych pliki konfiguracji znajdują się w centralnej lokalizacji, skąd są dostępne dla wielu wystąpień aplikacji. Wystąpienia aplikacji mogą ładować swoje konfiguracje za pomocą interfejsu API REST plików i ludzie mogą uzyskiwać do nich dostęp w razie potrzeby przez zainstalowanie udziału SMB lokalnie.

    * **Udział diagnostyczny**:  
        Udział plików platformy Azure jest wygodnym miejscem do zapisywania dzienników, metryk i zrzutów awaryjnych aplikacji w chmurze. Dzienniki mogą być zapisywane przez wystąpienia aplikacji za pomocą interfejsu API REST pliku, a deweloperzy mogą uzyskiwać do nich dostęp przez zainstalowanie udziału plików na komputerze lokalnym. Zapewnia to dużą elastyczność, ponieważ deweloperzy mogą obsługiwać opracowywanie rozwiązań dla chmury bez konieczności porzucania istniejących narzędzi, które znają i lubią.

    * Tworzenie **i testowanie/debugowanie**:  
        Deweloperzy i administratorzy często w swojej pracy z maszynami wirtualnymi w chmurze muszą korzystać z zestawu narzędzi i programów narzędziowych. Kopiowanie tych narzędzi do każdej maszyny wirtualnej może być czasochłonne. Zainstalowanie udziału plików platformy Azure lokalnie na maszynach wirtualnych pozwala deweloperowi i administratorowi na szybkie uzyskanie dostępu do swoich narzędzi bez konieczności ich kopiowania.
* **Kontenerach**:  
    Udziały plików platformy Azure mogą służyć jako woluminy trwałe dla kontenerów stanowych. Kontenery dostarczają możliwości "Kompiluj raz, uruchamiaj w dowolnym miejscu", które umożliwiają deweloperom przyspieszenie innowacji. W przypadku kontenerów, które uzyskują dostęp do danych pierwotnych przy każdym uruchomieniu, współużytkowany system plików jest wymagany, aby umożliwić tym kontenerom dostęp do systemu plików niezależnie od tego, które wystąpienie jest uruchamiane.

## <a name="key-benefits"></a>Najważniejsze korzyści
* **Dostęp współdzielony**. Udziały plików platformy Azure obsługują standardowe w branży protokoły SMB i NFS, co oznacza, że można bezproblemowo zastąpić lokalne udziały plików z udziałami plików platformy Azure bez obaw o zgodność aplikacji. Możliwość udostępnienia systemu plików na wielu komputerach, aplikacjach/wystąpieniach jest znaczącą korzyścią usługi Azure Files dla aplikacji wymagających możliwości pracy w środowisku współdzielonym. 
* W **pełni zarządzane**. Udziały plików platformy Azure można tworzyć bez konieczności zarządzania sprzętem lub systemem operacyjnym. Oznacza to, że nie trzeba stosować poprawek systemu operacyjnego serwera w celu zastosowania krytycznych uaktualnień ochrony ani wymieniać uszkodzonych dysków twardych.
* **Skrypty i narzędzia**. Za pomocą poleceń cmdlet programu PowerShell i interfejsu wiersza polecenia platformy Azure można tworzyć i instalować udziały plików platformy Azure oraz zarządzać nimi w ramach administrowania aplikacjami platformy Azure. Udziały plików platformy Azure można tworzyć i zarządzać nimi przy użyciu Azure Portal i Eksplorator usługi Azure Storage. 
* **Odporność**. Usługa Azure Files została zbudowana od podstaw w celu zapewnienia nieprzerwanej dostępności. Dzięki zastąpieniu lokalnych udziałów plików usługą Azure Files już nigdy nie będziesz musieć usuwać lokalnych awarii zasilania ani rozwiązywać problemów z siecią. 
* **Znajomy sposób programowania**. Aplikacje działające na platformie Azure mogą uzyskiwać dostęp do danych udziału za pomocą [interfejsów API We/Wy systemu plików](/dotnet/api/system.io.file). Dzięki temu programiści mogą wykorzystać istniejący kod i własne umiejętności, aby zmigrować istniejące aplikacje. Oprócz systemowych interfejsów API We/Wy można używać [bibliotek klienckich usługi Azure Storage](/previous-versions/azure/dn261237(v=azure.100)) lub [interfejsu API REST usługi Azure Storage](/rest/api/storageservices/file-service-rest-api).

## <a name="next-steps"></a>Następne kroki
* [Więcej informacji na temat dostępnych protokołów udziałów plików](storage-files-compare-protocols.md)
* [Tworzenie udziału plików platformy Azure](storage-how-to-create-file-share.md)
* [Łączenie i Instalowanie udziału SMB w systemie Windows](storage-how-to-use-files-windows.md)
* [Łączenie i Instalowanie udziału SMB w systemie Linux](storage-how-to-use-files-linux.md)
* [Łączenie i Instalowanie udziału SMB w systemie macOS](storage-how-to-use-files-mac.md)
* [Jak utworzyć udział NFS](storage-files-how-to-create-nfs-shares.md)
