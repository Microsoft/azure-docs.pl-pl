---
title: Dodawanie/usuwanie punktu końcowego Azure File Sync serwera | Microsoft Docs
description: Dowiedz się, jak dodać lub usunąć punkt końcowy serwera za pomocą Azure File Sync. Punkt końcowy serwera to pewna lokalizacja na zarejestrowanym serwerze, na przykład folder na woluminie serwera.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bcb346782b8d158dab5371a583c5a2576f0a09c9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796815"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Dodawanie/usuwanie punktu końcowego Azure File Sync serwera
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. W tym celu można przekształcić serwery z systemem Windows w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS) i możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

Punkt *końcowy serwera* reprezentuje określoną lokalizację na *zarejestrowanym* serwerze, taką jak folder na woluminie serwera lub katalog główny woluminu. Na tym samym woluminie może istnieć wiele punktów końcowych serwera, jeśli ich przestrzenie nazw nie nakładają się (na przykład F:\sync1 i F:\sync2), a każdy punkt końcowy jest synchronizowane z unikatową grupą synchronizacji. Zasady warstw w chmurze można skonfigurować osobno dla każdego punktu końcowego serwera. Jeśli dodasz lokalizację serwera z istniejącym zestawem plików jako punkt końcowy serwera do grupy synchronizacji, te pliki zostaną scalone z innymi plikami już w innych punktach końcowych w grupie synchronizacji.

Zobacz [How to deploy Azure File Sync](file-sync-deployment-guide.md) (Jak wdrożyć Azure File Sync), aby uzyskać informacje na temat Azure File Sync wdrażania aplikacji na wszystkich platformach.

## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć punkt końcowy serwera, należy najpierw upewnić się, że spełnione są następujące kryteria: 
- Na serwerze jest Azure File Sync agent usługi i został zarejestrowany. Instrukcje dotyczące instalowania agenta Azure File Sync można znaleźć w artykule [Register/unregister a server with Azure File Sync](file-sync-server-registration.md) (Rejestrowanie/wyrejestruj serwer za pomocą Azure File Sync rejestracji). 
- Upewnij się, że wdrożono usługę synchronizacji magazynu. Aby uzyskać szczegółowe informacje na temat wdrażania usługi synchronizacji [magazynu,](file-sync-deployment-guide.md) Azure File Sync jak wdrożyć usługę synchronizacji magazynu. 
- Upewnij się, że grupa synchronizacji została wdrożona. Dowiedz się, [jak utworzyć grupę synchronizacji.](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint)
- Upewnij się, że serwer jest połączony z Internetem i że platforma Azure jest dostępna. Do komunikacji między serwerem a naszą usługą używamy portu 443.

## <a name="add-a-server-endpoint"></a>Dodawanie punktu końcowego serwera
Aby dodać punkt końcowy serwera, przejdź do żądanej grupy synchronizacji i wybierz pozycję "Dodaj punkt końcowy serwera".

![Dodawanie nowego punktu końcowego serwera w okienku grupy synchronizacji](media/storage-sync-files-server-endpoint/add-server-endpoint.png)

W obszarze Dodawanie punktu końcowego serwera wymagane **są następujące informacje:**

- **Zarejestrowany serwer:** nazwa serwera lub klastra, na którym ma być utworzyć punkt końcowy serwera.
- **Ścieżka:** ścieżka w systemie Windows Server do zsynchronizowania w ramach grupy synchronizacji.
- **Warstwy w chmurze:** przełącznik umożliwiający włączanie lub wyłączanie obsługi warstw w chmurze. Po włączeniu obsługi warstw w chmurze pliki *będą warstwowe* w udziałach plików platformy Azure. Dzięki temu lokalne udziały plików są konwertowane na pamięć podręczną, a nie pełną kopię zestawu danych, co pomaga zarządzać wydajnością miejsca na serwerze.
- **Wolne miejsce na woluminie:** ilość wolnego miejsca do zarezerwowania na woluminie, na którym znajduje się punkt końcowy serwera. Jeśli na przykład ilość wolnego miejsca na woluminie jest ustawiona na 50% na woluminie z pojedynczym punktem końcowym serwera, mniej więcej połowa ilości danych zostanie przesłoniona Azure Files. Niezależnie od tego, czy włączono obsługę warstw w chmurze, udział plików platformy Azure zawsze zawiera pełną kopię danych w grupie synchronizacji.

Wybierz **pozycję Utwórz,** aby dodać punkt końcowy serwera. Pliki w przestrzeni nazw grupy synchronizacji będą teraz synchronizowane. 

## <a name="remove-a-server-endpoint"></a>Usuwanie punktu końcowego serwera
Jeśli chcesz zaprzestać korzystania z Azure File Sync dla danego punktu końcowego serwera, możesz usunąć punkt końcowy serwera. 

> [!Warning]  
> Nie należy próbować rozwiązywać problemów z synchronizacją, warstwami w chmurze ani żadnym innym aspektem usługi Azure File Sync przez usunięcie i ponowne stwoczenie punktu końcowego serwera, chyba że zostanie jawnie poinstruowany przez inżyniera firmy Microsoft. Usunięcie punktu końcowego serwera jest operacją destruktywną, a pliki warstwowe w punkcie końcowym serwera nie będą "ponownie nawiązywane" z ich lokalizacjami w udziałach plików platformy Azure po ponownym odtworzeniu punktu końcowego serwera, co spowoduje błędy synchronizacji. Należy również zauważyć, że pliki warstwowe, które istnieją poza przestrzenią nazw punktu końcowego serwera, mogą zostać trwale utracone. Pliki warstwowe mogą istnieć w punkcie końcowym serwera, nawet jeśli nie włączono obsługi warstw w chmurze.

Aby upewnić się, że wszystkie pliki warstwowe zostaną odwołane przed usunięciem punktu końcowego serwera, wyłącz obsługę warstw w chmurze w punkcie końcowym serwera, a następnie wykonaj następujące polecenie cmdlet programu PowerShell, aby odwołać wszystkie pliki warstwowe w przestrzeni nazw punktu końcowego serwera:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
Określenie wartości `-Order CloudTieringPolicy` spowoduje najpierw przywołanie ostatnio zmodyfikowanych plików.
Inne opcjonalne, ale przydatne parametry, które należy wziąć pod uwagę:
* `-ThreadCount` Określa, ile plików można odwołać równolegle.
* `-PerFileRetryCount`Określa, jak często będzie podejmowana próba odwołania pliku, który jest obecnie zablokowany.
* `-PerFileRetryDelaySeconds`Określa czas (w sekundach) między ponowieniem próby odwołania i powinien być zawsze używany w połączeniu z poprzednim parametrem.

> [!Note]  
> Jeśli wolumin lokalny hostowany na serwerze nie ma wystarczającej ilości wolnego miejsca, aby odwołać wszystkie dane warstwowe, polecenie `Invoke-StorageSyncFileRecall` cmdlet zakończy się niepowodzeniem.  

Aby usunąć punkt końcowy serwera:

1. Przejdź do usługi synchronizacji magazynu, w której zarejestrowano serwer.
1. Przejdź do żądanej grupy synchronizacji.
1. Usuń punkt końcowy serwera, który ma być w grupie synchronizacji w usłudze synchronizacji magazynu. Można to zrobić, klikając prawym przyciskiem myszy odpowiedni punkt końcowy serwera w okienku grupy synchronizacji.

    ![Usuwanie punktu końcowego serwera z grupy synchronizacji](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Następne kroki
- [Rejestrowanie/wyrejestrowywuje serwer przy użyciu Azure File Sync](file-sync-server-registration.md)
- [Planowanie wdrażania usługi Azure File Sync](file-sync-planning.md)
- [Monitorowanie usługi Azure File Sync](file-sync-monitoring.md)
