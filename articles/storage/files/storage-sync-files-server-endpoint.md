---
title: Dodawanie/Usuwanie punktu końcowego serwera Azure File Sync | Microsoft Docs
description: Dowiedz się, jak dodać lub usunąć punkt końcowy serwera z Azure File Sync. Punkt końcowy serwera to określona lokalizacja na zarejestrowanym serwerze, taka jak folder na woluminie serwera.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f75f0d1ae12db11590f8ce62f3c7b4c0f3e12817
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541496"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Dodawanie/Usuwanie punktu końcowego serwera Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Robi to poprzez transformowanie serwerów z systemem Windows do szybkiej pamięci podręcznej udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS) i możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

*Punkt końcowy serwera* reprezentuje konkretną lokalizację na *zarejestrowanym serwerze*, na przykład folder na woluminie serwera lub w katalogu głównym woluminu. Wiele punktów końcowych serwera może istnieć na tym samym woluminie, jeśli ich przestrzenie nazw nie nakładają się na siebie (na przykład F:\sync1 i F:\sync2), a każdy punkt końcowy jest synchronizowany z unikatową grupą synchronizacji. Zasady dotyczące warstw chmurowych można skonfigurować osobno dla każdego punktu końcowego serwera. W przypadku dodania lokalizacji serwera z istniejącym zestawem plików jako punktu końcowego serwera do grupy synchronizacji te pliki zostaną scalone z innymi plikami znajdującymi się już w innych punktach końcowych w grupie synchronizacji.

Zapoznaj się z artykułem [jak wdrożyć Azure File Sync](storage-sync-files-deployment-guide.md) , aby uzyskać informacje na temat sposobu wdrażania Azure File Sync na końcu.

## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć punkt końcowy serwera, należy najpierw upewnić się, że spełnione są następujące kryteria: 
- Na serwerze zainstalowano agenta Azure File Sync i został on zarejestrowany. Instrukcje dotyczące instalowania agenta Azure File Sync można znaleźć w artykule [Rejestrowanie/Wyrejestrowywanie serwera z Azure File Sync](storage-sync-files-server-registration.md) artykułem. 
- Upewnij się, że została wdrożona usługa synchronizacji magazynu. Zobacz [jak wdrożyć Azure File Sync](storage-sync-files-deployment-guide.md) , aby uzyskać szczegółowe informacje na temat wdrażania usługi synchronizacji magazynu. 
- Upewnij się, że została wdrożona Grupa synchronizacji. Dowiedz się, jak [utworzyć grupę synchronizacji](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Upewnij się, że serwer jest połączony z Internetem i że platforma Azure jest dostępna. Na potrzeby komunikacji między serwerem a naszą usługą korzystamy z portu 443.

## <a name="add-a-server-endpoint"></a>Dodawanie punktu końcowego serwera
Aby dodać punkt końcowy serwera, przejdź do żądanej grupy synchronizacji, a następnie wybierz pozycję "Dodaj punkt końcowy serwera".

![Dodawanie nowego punktu końcowego serwera w okienku grupy synchronizacji](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

W obszarze **Dodawanie punktu końcowego serwera**wymagane są następujące informacje:

- **Zarejestrowany serwer**: Nazwa serwera lub klastra, na którym ma zostać utworzony punkt końcowy serwera.
- **Ścieżka**: ścieżka w systemie Windows Server, która ma zostać zsynchronizowana w ramach grupy synchronizacji.
- Obsługa **warstw w chmurze**: przełącznik umożliwiający włączenie lub wyłączenie obsługi warstw w chmurze. Gdy ta *Funkcja jest włączona* , Obsługa warstw w chmurze będzie uwzględniać pliki w udziałach plików platformy Azure. Powoduje to przekonwertowanie lokalnych udziałów plików na pamięć podręczną, a nie pełną kopię zestawu danych, co ułatwia zarządzanie wydajnością miejsca na serwerze.
- **Wolne miejsce w woluminie**: ilość wolnego miejsca do zarezerwowania na woluminie, na którym znajduje się punkt końcowy serwera. Na przykład, jeśli ilość wolnego miejsca na woluminie jest ustawiona na 50% na woluminie z jednym punktem końcowym serwera, około połowy ilości danych zostanie przydzielona do Azure Files. Bez względu na to, czy włączono obsługę warstw w chmurze, udział plików platformy Azure zawsze ma kompletną kopię danych w grupie synchronizacji.

Wybierz pozycję **Utwórz** , aby dodać punkt końcowy serwera. Pliki w przestrzeni nazw grupy synchronizacji będą teraz nadal zsynchronizowane. 

## <a name="remove-a-server-endpoint"></a>Usuwanie punktu końcowego serwera
Jeśli chcesz przestać używać Azure File Sync dla danego punktu końcowego serwera, możesz usunąć punkt końcowy serwera. 

> [!Warning]  
> Nie należy próbować rozwiązywać problemów z synchronizacją, warstwami chmury ani jakimkolwiek innym aspektem Azure File Sync przez usunięcie i ponowne utworzenie punktu końcowego serwera, chyba że zostanie on jawnie nawiązany przez inżyniera firmy Microsoft. Usunięcie punktu końcowego serwera jest operacją niszczącą, a pliki warstwowe w ramach punktu końcowego serwera nie będą "ponownie połączone" z ich lokalizacjami w udziale plików platformy Azure po ponownym utworzeniu punktu końcowego serwera, co spowoduje błędy synchronizacji. Należy również pamiętać, że pliki warstwowe istniejące poza przestrzenią nazw punktu końcowego serwera mogą zostać trwale utracone. Pliki warstwowe mogą znajdować się w punkcie końcowym serwera, nawet jeśli Obsługa warstw w chmurze nigdy nie została włączona.

Aby upewnić się, że wszystkie pliki warstwowe są ponownie wywoływane przed usunięciem punktu końcowego serwera, Wyłącz obsługę warstw w chmurze w punkcie końcowym serwera, a następnie wykonaj następujące polecenie cmdlet programu PowerShell, aby odwołać wszystkie pliki warstwowe w przestrzeni nazw punktu końcowego serwera:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
Określenie `-Order CloudTieringPolicy` spowoduje, że najpierw zostanie przywoływany ostatnio zmodyfikowane pliki.
Inne opcjonalne, ale przydatne parametry, które należy wziąć pod uwagę:
* `-ThreadCount` Określa, ile plików można wielokrotnie odwoływać.
* `-PerFileRetryCount`Określa, jak często zostanie podjęta próba odwołania pliku, który jest aktualnie zablokowany.
* `-PerFileRetryDelaySeconds`Określa czas (w sekundach) między ponownymi próbami odwołania i powinna być zawsze używana w połączeniu z poprzednim parametrem.

> [!Note]  
> Jeśli na lokalnym woluminie hostującym serwer nie ma wystarczającej ilości wolnego miejsca, aby odwołać wszystkie dane warstwowe, `Invoke-StorageSyncFileRecall` polecenie cmdlet kończy się niepowodzeniem.  

Aby usunąć punkt końcowy serwera:

1. Przejdź do usługi synchronizacji magazynu, w której zarejestrowano serwer.
2. Przejdź do żądanej grupy synchronizacji.
3. Usuń punkt końcowy serwera, który należy do grupy synchronizacji w usłudze synchronizacji magazynu. Można to zrobić, klikając prawym przyciskiem myszy odpowiedni punkt końcowy serwera w okienku Grupa synchronizacji.

    ![Usuwanie punktu końcowego serwera z grupy synchronizacji](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Następne kroki
- [Zarejestruj/Wyrejestruj serwer z Azure File Sync](storage-sync-files-server-registration.md)
- [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
