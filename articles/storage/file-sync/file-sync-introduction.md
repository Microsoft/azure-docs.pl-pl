---
title: Wprowadzenie do Azure File Sync | Microsoft Docs
description: Omówienie usługi Azure File Sync, która umożliwia tworzenie i używanie udziałów plików sieciowych w chmurze przy użyciu standardowego protokołu SMB w branży.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 04/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: da40783e3d299b0edf27c6d045dbc6dcfc5cf964
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797663"
---
# <a name="what-is-azure-file-sync"></a>Co to jest usługa Azure File Sync?
Azure File Sync umożliwia scentralizowanie udziałów plików organizacji w programie Azure Files przy zachowaniu elastyczności, wydajności i zgodności serwera plików z systemem Windows. Niektórzy użytkownicy mogą zdecydować się na zachowanie pełnej kopii danych lokalnie, ale Azure File Sync dodatkowo ma możliwość przekształcania systemu Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Możesz mieć tyle pamięci podręcznych, ile potrzebujesz na całym świecie.   

## <a name="videos"></a>Filmy wideo
| Wprowadzenie do Azure File Sync | Azure Files z synchronizacją (Ignite 2019)  |
|-|-|
| [![Zrzut ekranu wideo Wprowadzenie Azure File Sync — kliknij, aby odtworzyć!](../files/media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Zrzut ekranu aplikacji Azure Files prezentacją synchronizacji — kliknij, aby odtworzyć!](../files/media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

## <a name="benefits-of-azure-file-sync"></a>Zalety Azure File Sync

### <a name="cloud-tiering"></a>Obsługa warstw w chmurze
Po włączeniu obsługi warstw w chmurze najczęściej używane pliki są buforowane na serwerze lokalnym, a najczęściej używane pliki są warstwowe w chmurze. Możesz kontrolować, ile miejsca na dysku lokalnym jest używane do buforowania. Pliki warstwowe można szybko przywołać na żądanie, dzięki czemu środowisko jest bezproblemowe, a jednocześnie można obniżyć koszty, ponieważ wystarczy przechowywać tylko część danych lokalnie. Aby uzyskać więcej informacji na temat warstw w chmurze, zobacz [Omówienie warstw w chmurze.](file-sync-cloud-tiering-overview.md) 

### <a name="multi-site-access-and-sync"></a>Dostęp do wielu lokacji i synchronizacja
Azure File Sync jest idealnym rozwiązaniem w przypadku scenariuszy dostępu rozproszonego. Dla każdego biura można aprowizować lokalny system Windows Server w ramach Azure File Sync wdrożenia. Zmiany wprowadzone na serwerze w jednym biurze są automatycznie synchronizowane z serwerami we wszystkich innych biurach.

### <a name="business-continuity-and-disaster-recovery"></a>Ciągłość działania i odzyskiwanie po awarii
Azure File Sync jest zapewniana przez Azure Files, która oferuje kilka opcji nadmiarowości dla magazynu o wysokiej dostępnej pojemności. Ponieważ platforma Azure zawiera odporne kopie danych, serwer lokalny staje się jednorazowym urządzeniem do buforowania, a odzyskiwanie po nieudanym serwerze można wykonać przez dodanie nowego serwera do Azure File Sync wdrożenia. Zamiast przywracać z lokalnej kopii zapasowej, należy aprowizować inny system Windows Server, zainstalować na nim agenta Azure File Sync, a następnie dodać go do Azure File Sync wdrożenia. Azure File Sync pliki zostaną pobrane przed pobraniem danych, dzięki czemu serwer może być uruchomiony tak szybko, jak to możliwe. Aby jeszcze szybciej odzyskiwać dane, można mieć w ramach wdrożenia ciepły serwer lub użyć Azure File Sync klastrowania systemu Windows.

## <a name="cloud-side-backup"></a>Kopia zapasowa po stronie chmury
Zmniejsz wydatki na lokalne kopie zapasowe, korzystając ze scentralizowanych kopii zapasowych w chmurze przy użyciu Azure Backup. Azure Files udziały SMB mają natywne możliwości migawek, a proces można zautomatyzować przy użyciu usługi Azure Backup, aby zaplanować tworzenie kopii zapasowych i zarządzać ich przechowywaniem. Azure Backup integruje się również z serwerami lokalnymi, więc po przywróceniu do chmury te zmiany są automatycznie pobierane na serwery z systemem Windows.  

## <a name="next-steps"></a>Następne kroki
* [Planowanie wdrażania usługi Azure File Sync](file-sync-planning.md)
* [Omówienie warstw w chmurze](file-sync-cloud-tiering-overview.md)
* [Monitorowanie usługi Azure File Sync](file-sync-monitoring.md)