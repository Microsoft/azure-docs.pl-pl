---
title: Wprowadzenie do usługi Azure Files | Microsoft Docs
description: Omówienie usługi Azure Files umożliwiającej tworzenie i używanie udziałów plików sieciowych w chmurze z wykorzystaniem standardowego protokołu SMB.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 03/10/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: aff6f99c119ba2854fd7923d2a15efb2e1a6b601
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80666795"
---
# <a name="what-is-azure-files"></a>Co to jest usługa Azure Files?
Usługa Azure Files oferuje w pełni zarządzane udziały plików w chmurze, dostępne za pośrednictwem [protokołu Server Message Block (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx), będącego standardem branżowym. Udziały plików platformy Azure można instalować współbieżnie za pośrednictwem chmurowych lub lokalnych wdrożeń systemów Windows, Linux i macOS. Ponadto udziały plików platformy Azure mogą być buforowane w systemach Windows Server za pomocą usługi Azure File Sync w celu zapewnienia szybkiego dostępu blisko miejsca, w którym dane są używane.

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
    Usługi Azure Files można użyć w celu całkowitego zastąpienia lub uzupełnienia tradycyjnych lokalnych serwerów plików lub urządzeń NAS. W popularnych systemach operacyjnych, takich jak Windows, macOS i Linux, udziały plików platformy Azure można zainstalować bezpośrednio bez względu na to, gdzie się one znajdują. Udziały plików platformy Azure można również replikować za pomocą usługi Azure File Sync w systemach Windows Server, lokalnie lub w chmurze, w celu zapewnienia wydajnego i rozproszonego buforowania danych w miejscu ich używania. W najnowszej wersji [Azure Files uwierzytelniania usługi AD](storage-files-active-directory-overview.md)udziały plików platformy Azure mogą nadal współdziałać z usługą AD hostowaną lokalnie na potrzeby kontroli dostępu. 

* **Migrowanie aplikacji metodą „lift and shift”**:  
    Usługa Azure File Storage ułatwia migrowanie do chmury metodą „lift and shift” tych aplikacji, które oczekują udziału plików do przechowywania danych aplikacji lub danych użytkownika. Usługa Azure Files umożliwia zarówno klasyczny scenariusz migracji metodą „lift and shift”, w którym aplikacja i jej dane są przenoszone do platformy Azure, jak i scenariusz hybrydowy migracji „lift and shift”, w którym dane aplikacji są przenoszone do usługi Azure Files, a aplikacja dalej działa lokalnie. 

* **Uproszczenie programowania aplikacji w chmurze**:  
    Usługi Azure Files można także używać na wiele różnych sposobów w celu uproszczenia nowych projektów projektowania aplikacji w chmurze. Przykład:
    * **Współdzielone ustawienia aplikacji**:  
        W typowym wdrożeniu aplikacji rozproszonych pliki konfiguracji znajdują się w centralnej lokalizacji, skąd są dostępne dla wielu wystąpień aplikacji. Wystąpienia aplikacji mogą ładować swoje konfiguracje za pomocą interfejsu API REST plików i ludzie mogą uzyskiwać do nich dostęp w razie potrzeby przez zainstalowanie udziału SMB lokalnie.

    * **Udział diagnostyczny**:  
        Udział plików platformy Azure jest wygodnym miejscem do zapisywania dzienników, metryk i zrzutów awaryjnych aplikacji w chmurze. Dzienniki mogą być zapisywane przez wystąpienia aplikacji za pomocą interfejsu API REST pliku, a deweloperzy mogą uzyskiwać do nich dostęp przez zainstalowanie udziału plików na komputerze lokalnym. Zapewnia to dużą elastyczność, ponieważ deweloperzy mogą obsługiwać opracowywanie rozwiązań dla chmury bez konieczności porzucania istniejących narzędzi, które znają i lubią.

    * Tworzenie **i testowanie/debugowanie**:  
        Deweloperzy i administratorzy często w swojej pracy z maszynami wirtualnymi w chmurze muszą korzystać z zestawu narzędzi i programów narzędziowych. Kopiowanie tych narzędzi do każdej maszyny wirtualnej może być czasochłonne. Zainstalowanie udziału plików platformy Azure lokalnie na maszynach wirtualnych pozwala deweloperowi i administratorowi na szybkie uzyskanie dostępu do swoich narzędzi bez konieczności ich kopiowania.

## <a name="key-benefits"></a>Najważniejsze korzyści
* **Dostęp współdzielony**. Udziały plików platformy Azure obsługują należący do standardów branżowych protokół SMB, co oznacza, że można bezproblemowo zastąpić lokalne udziały plików udziałami plików platformy Azure bez obaw o zgodność aplikacji. Możliwość udostępnienia systemu plików na wielu komputerach, aplikacjach/wystąpieniach jest znaczącą korzyścią usługi Azure Files dla aplikacji wymagających możliwości pracy w środowisku współdzielonym. 
* **Pełne zarządzanie**. Udziały plików platformy Azure można tworzyć bez konieczności zarządzania sprzętem lub systemem operacyjnym. Oznacza to, że nie trzeba stosować poprawek systemu operacyjnego serwera w celu zastosowania krytycznych uaktualnień ochrony ani wymieniać uszkodzonych dysków twardych.
* **Skrypty i narzędzia**. Polecenia cmdlet programu PowerShell oraz interfejs wiersza polecenia platformy Azure umożliwiają tworzenie i instalowanie udziałów plików platformy Azure oraz zarządzanie nimi w ramach administrowania aplikacjami platformy Azure. Za pomocą witryny Azure Portal i programu Eksplorator usługi Azure Storage można tworzyć udziały plików platformy Azure oraz nimi zarządzać. 
* **Odporność**. Usługa Azure Files została zbudowana od podstaw w celu zapewnienia nieprzerwanej dostępności. Dzięki zastąpieniu lokalnych udziałów plików usługą Azure Files już nigdy nie będziesz musieć usuwać lokalnych awarii zasilania ani rozwiązywać problemów z siecią. 
* **Znajomy sposób programowania**. Aplikacje działające na platformie Azure mogą uzyskiwać dostęp do danych udziału za pomocą [interfejsów API We/Wy systemu plików](https://msdn.microsoft.com/library/system.io.file.aspx). Dzięki temu programiści mogą wykorzystać istniejący kod i własne umiejętności, aby zmigrować istniejące aplikacje. Oprócz systemowych interfejsów API We/Wy można używać [bibliotek klienckich usługi Azure Storage](https://msdn.microsoft.com/library/azure/dn261237.aspx) lub [interfejsu API REST usługi Azure Storage](/rest/api/storageservices/file-service-rest-api).

## <a name="next-steps"></a>Następne kroki
* [Tworzenie udziału plików platformy Azure](storage-how-to-create-file-share.md)
* [Connect and Mount on Windows](storage-how-to-use-files-windows.md) (Nawiązywanie połączenia i instalowanie w systemie Windows)
* [Connect and Mount on Linux](storage-how-to-use-files-linux.md) (Nawiązywanie połączenia i instalowanie w systemie Linux)
* [Connect and Mount on macOS](storage-how-to-use-files-mac.md) (Nawiązywanie połączenia i instalowanie w systemie macOS)
