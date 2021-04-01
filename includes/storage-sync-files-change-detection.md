---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 1387933dc82c07e73b7715d6593238ea8c993e93
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96005337"
---
Zmiany wprowadzone w udziale plików platformy Azure przy użyciu Azure Portal lub protokołu SMB nie są natychmiast wykrywane i replikowane, podobnie jak zmiany w punkcie końcowym serwera. Azure Files nie ma jeszcze powiadomień o zmianach ani rejestrowania, dlatego nie ma możliwości automatycznego inicjowania sesji synchronizacji w przypadku zmiany plików. W systemie Windows Server Azure File Sync używa [rejestrowania numerów USN systemu Windows](/windows/win32/fileio/change-journals) do automatycznego inicjowania sesji synchronizacji, gdy pliki zostaną zmienione.

Aby wykryć zmiany w udziale plików platformy Azure, Azure File Sync ma zaplanowane zadanie o nazwie *zadanie wykrywania zmian*. Zadanie wykrywania zmian wylicza każdy plik w udziale plików, a następnie porównuje go z wersją synchronizacji dla tego pliku. Gdy zadanie wykrywania zmian określi, że pliki uległy zmianie, Azure File Sync inicjuje sesję synchronizacji. Zadanie wykrywania zmian jest inicjowane co 24 godziny. Ponieważ zadanie wykrywania zmian działa przez wyliczenie wszystkich plików w udziale plików platformy Azure, wykrywanie zmian trwa dłużej w większych przestrzeniach nazw niż w mniejszych przestrzeniach nazw. W przypadku dużych przestrzeni nazw może upłynąć więcej niż co 24 godziny, aby określić, które pliki uległy zmianie.

Aby natychmiast synchronizować pliki, które zostały zmienione w udziale plików platformy Azure, można ręcznie zainicjować wykrywanie zmian w udziale plików platformy Azure za pomocą polecenia cmdlet **Invoke-AzStorageSyncChangeDetection** programu PowerShell. To polecenie cmdlet jest przeznaczone dla scenariuszy, w których jakiś typ zautomatyzowany proces wprowadza zmiany w udziale plików platformy Azure lub zmiany są wykonywane przez administratora (na przykład przeniesienie plików i katalogów do udziału). W przypadku zmian wprowadzonych przez użytkownika końcowego zaleca się zainstalowanie agenta Azure File Sync na maszynie wirtualnej IaaS, a użytkownicy końcowi uzyskują dostęp do udziału plików za pomocą maszyny wirtualnej IaaS. W ten sposób wszystkie zmiany będą szybko synchronizowane z innymi agentami bez konieczności używania polecenia cmdlet Invoke-AzStorageSyncChangeDetection. Aby dowiedzieć się więcej, zobacz dokumentację [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) .

>[!NOTE]
>Zmiany wprowadzone w udziale plików platformy Azure przy użyciu REST nie aktualizują czasu ostatniej modyfikacji SMB i nie będą widoczne jako zmiany przez synchronizację.

Eksplorujemy Dodawanie wykrywania zmian dla udziału plików platformy Azure podobnego do numeru USN dla woluminów w systemie Windows Server. Pomóż nam określić priorytet tej funkcji na potrzeby przyszłego rozwoju dzięki głosowaniu na [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).