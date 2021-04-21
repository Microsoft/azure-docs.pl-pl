---
title: Wprowadzenie do usługi Azure Files | Microsoft Docs
description: Omówienie usługi Azure Files umożliwiającej tworzenie i używanie udziałów plików sieciowych w chmurze z wykorzystaniem standardowego protokołu SMB.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7279c5578d969db9178ce85734e3a43f9eccdf8d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780051"
---
# <a name="what-is-azure-files"></a>Co to jest usługa Azure Files?
Azure Files udostępnia w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem standardowego w branży protokołu bloku komunikatów serwera [(SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) lub protokołu [NFS (Network File System).](https://en.wikipedia.org/wiki/Network_File_System) Udziały plików platformy Azure mogą być instalowane współbieżnie przez wdrożenia w chmurze lub lokalne. Azure Files plików SMB są dostępne z klientów z systemami Windows, Linux i macOS. Azure Files plików NFS są dostępne z klientów z systemem Linux lub macOS. Ponadto Azure Files plików SMB mogą być buforowane na serwerach z systemem Windows Azure File Sync w celu szybkiego dostępu w pobliżu miejsca, w którym dane są używane.

Oto kilka filmów wideo na temat typowych przypadków użycia Azure Files:
* [Zastępowanie serwera plików bez serwera udziałem plików platformy Azure](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
* [Wprowadzenie do kontenerów profilów FSLogix na Azure Files w Windows Virtual Desktop uwierzytelniania usługi AD](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="why-azure-files-is-useful"></a>Na czym polega przydatność usługi Azure Files
Udziały plików platformy Azure mogą być używane w następujących celach:

* **Zastępowanie lub uzupełnianie lokalnych serwerów plików**:  
    Usługi Azure Files można użyć w celu całkowitego zastąpienia lub uzupełnienia tradycyjnych lokalnych serwerów plików lub urządzeń NAS. W popularnych systemach operacyjnych, takich jak Windows, macOS i Linux, udziały plików platformy Azure można zainstalować bezpośrednio bez względu na to, gdzie się one znajdują. Udziały plików SMB platformy Azure można również replikować za pomocą usługi Azure File Sync do serwerów z systemem Windows, lokalnie lub w chmurze, aby uzyskać wydajność i rozproszone buforowanie danych, w których są używane. Dzięki najnowszej wersji uwierzytelniania [Azure Files AD](storage-files-active-directory-overview.md)udziały plików SMB usługi Azure File mogą nadal współpracować z lokalną usługą AD w celu kontroli dostępu. 

* **Migrowanie aplikacji metodą „lift and shift”**:  
    Usługa Azure File Storage ułatwia migrowanie do chmury metodą „lift and shift” tych aplikacji, które oczekują udziału plików do przechowywania danych aplikacji lub danych użytkownika. Usługa Azure Files umożliwia zarówno klasyczny scenariusz migracji metodą „lift and shift”, w którym aplikacja i jej dane są przenoszone do platformy Azure, jak i scenariusz hybrydowy migracji „lift and shift”, w którym dane aplikacji są przenoszone do usługi Azure Files, a aplikacja dalej działa lokalnie. 

* **Uproszczenie programowania aplikacji w chmurze**:  
    Usługi Azure Files można także używać na wiele różnych sposobów w celu uproszczenia nowych projektów projektowania aplikacji w chmurze. Na przykład:
    * **Współdzielone ustawienia aplikacji**:  
        W typowym wdrożeniu aplikacji rozproszonych pliki konfiguracji znajdują się w centralnej lokalizacji, skąd są dostępne dla wielu wystąpień aplikacji. Wystąpienia aplikacji mogą ładować swoje konfiguracje za pomocą interfejsu API REST plików i ludzie mogą uzyskiwać do nich dostęp w razie potrzeby przez zainstalowanie udziału SMB lokalnie.

    * **Udział diagnostyczny**:  
        Udział plików platformy Azure jest wygodnym miejscem do zapisywania dzienników, metryk i zrzutów awaryjnych aplikacji w chmurze. Dzienniki mogą być zapisywane przez wystąpienia aplikacji za pomocą interfejsu API REST pliku, a deweloperzy mogą uzyskiwać do nich dostęp przez zainstalowanie udziału plików na komputerze lokalnym. Zapewnia to dużą elastyczność, ponieważ deweloperzy mogą obsługiwać opracowywanie rozwiązań dla chmury bez konieczności porzucania istniejących narzędzi, które znają i lubią.

    * **Deweloper/testowanie/debugowanie:**  
        Deweloperzy i administratorzy często w swojej pracy z maszynami wirtualnymi w chmurze muszą korzystać z zestawu narzędzi i programów narzędziowych. Kopiowanie tych narzędzi do każdej maszyny wirtualnej może być czasochłonne. Zainstalowanie udziału plików platformy Azure lokalnie na maszynach wirtualnych pozwala deweloperowi i administratorowi na szybkie uzyskanie dostępu do swoich narzędzi bez konieczności ich kopiowania.
* **Konteneryzacja:**  
    Udziały plików platformy Azure mogą być używane jako trwałe woluminy dla kontenerów stanowych. Kontenery zapewniają możliwości "kompilowania raz i uruchamiania w dowolnym miejscu", które umożliwiają deweloperom przyspieszenie innowacji. W przypadku kontenerów, które na każdym początku mają dostęp do danych pierwotnych, wymagany jest udostępniony system plików, aby umożliwić tym kontenerom dostęp do systemu plików niezależnie od tego, w którym wystąpieniu są uruchamiane.

## <a name="key-benefits"></a>Najważniejsze korzyści
* **Dostęp współdzielony**. Udziały plików platformy Azure obsługują standardowe w branży protokoły SMB i NFS, co oznacza, że możesz bezproblemowo zastąpić lokalne udziały plików udziałami plików platformy Azure bez martwienia się o zgodność aplikacji. Możliwość udostępnienia systemu plików na wielu komputerach, aplikacjach/wystąpieniach jest znaczącą korzyścią usługi Azure Files dla aplikacji wymagających możliwości pracy w środowisku współdzielonym. 
* **W pełni zarządzane .** Udziały plików platformy Azure można tworzyć bez konieczności zarządzania sprzętem lub systemem operacyjnym. Oznacza to, że nie trzeba stosować poprawek systemu operacyjnego serwera w celu zastosowania krytycznych uaktualnień ochrony ani wymieniać uszkodzonych dysków twardych.
* **Skrypty i narzędzia**. Polecenia cmdlet programu PowerShell i interfejs wiersza polecenia platformy Azure mogą służyć do tworzenia i instalacji udziałów plików platformy Azure oraz zarządzania nimi w ramach administrowania aplikacjami platformy Azure. Udziały plików platformy Azure można tworzyć i zarządzać nimi przy użyciu Azure Portal i Eksplorator usługi Azure Storage. 
* **Odporność**. Usługa Azure Files została zbudowana od podstaw w celu zapewnienia nieprzerwanej dostępności. Dzięki zastąpieniu lokalnych udziałów plików usługą Azure Files już nigdy nie będziesz musieć usuwać lokalnych awarii zasilania ani rozwiązywać problemów z siecią. 
* **Znajomy sposób programowania**. Aplikacje działające na platformie Azure mogą uzyskiwać dostęp do danych udziału za pomocą [interfejsów API We/Wy systemu plików](/dotnet/api/system.io.file). Dzięki temu programiści mogą wykorzystać istniejący kod i własne umiejętności, aby zmigrować istniejące aplikacje. Oprócz systemowych interfejsów API We/Wy można używać [bibliotek klienckich usługi Azure Storage](/previous-versions/azure/dn261237(v=azure.100)) lub [interfejsu API REST usługi Azure Storage](/rest/api/storageservices/file-service-rest-api).

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej o dostępnych protokołach udziałów plików](storage-files-compare-protocols.md)
* [Tworzenie udziału plików platformy Azure](storage-how-to-create-file-share.md)
* [Łączenie i montowania udziału SMB w systemie Windows](storage-how-to-use-files-windows.md)
* [Łączenie i montowania udziału SMB w systemie Linux](storage-how-to-use-files-linux.md)
* [Łączenie i montowania udziału SMB w systemie macOS](storage-how-to-use-files-mac.md)
* [Jak utworzyć udział NFS](storage-files-how-to-create-nfs-shares.md)
