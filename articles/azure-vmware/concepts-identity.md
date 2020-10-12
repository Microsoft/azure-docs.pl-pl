---
title: Pojęcia — tożsamość i dostęp
description: Dowiedz się więcej na temat pojęć dotyczących tożsamości i uzyskiwania dostępu do rozwiązania Azure VMware
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 9d5b7b4c7c2e0d55cffc99a3f371494f40320a16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88750589"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Pojęcia dotyczące tożsamości rozwiązań VMware platformy Azure

Program vCenter Server i Menedżer NSX-T są inicjowane podczas wdrażania chmury prywatnej. Program vCenter służy do zarządzania obciążeniami maszyny wirtualnej i menedżerem NSX-T w celu rozbudowania sieci zdefiniowanej przez oprogramowanie w chmurze prywatnej.

Zarządzanie dostępem i tożsamościami używa uprawnień grupy CloudAdmin dla programu vCenter i ograniczonych uprawnień administratora dla Menedżera NSX-T. Te zasady zapewniają, że Twoja Platforma chmury prywatnej może być uaktualniana automatycznie. Zapewnia to regularne najnowsze funkcje i poprawki. Więcej informacji o uaktualnieniach chmury prywatnej można znaleźć w [artykule dotyczącym uaktualniania chmury prywatnej][concepts-upgrades] .

## <a name="vcenter-access-and-identity"></a>dostęp i tożsamość vCenter

Uprawnienia w programie vCenter są udostępniane za pomocą grupy CloudAdmin. Ta grupa może być zarządzana lokalnie w programie vCenter lub poprzez integrację logowania jednokrotnego usługi vCenter LDAP z Azure Active Directory. Możesz włączyć tę integrację po wdrożeniu chmury prywatnej.

W poniższej tabeli przedstawiono uprawnienia CloudAdmin i CloudGlobalAdmin.

|  Zestaw uprawnień           | CloudAdmin | CloudGlobalAdmin | Komentarz |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Alarmy                  | Użytkownik CloudAdmin ma wszystkie przywileje alarmowe dotyczące alarmów w Compute-ResourcePool i maszynach wirtualnych.     |          --        |  -- |
|  Wdróż ponownie             |  --  |        --        |  Firma Microsoft zarządza zarządzaniem hostami.  |
|  Certyfikaty            |  --  |        --       |  Firma Microsoft zarządza certyfikatami.  |
|  Biblioteka zawartości         | Użytkownik CloudAdmin ma uprawnienia do tworzenia plików i używania ich w bibliotece zawartości.    |         Włączone z logowaniem jednokrotnym.         |  Firma Microsoft będzie dystrybuować pliki w bibliotece zawartości na hosty ESXi.  |
|  Centrum danych              |  --  |        --          |  Firma Microsoft wykonuje wszystkie operacje centrów danych.  |
|  Magazyn danych               | Datastore. AllocateSpace, datastore. Browse, Datastore.Config, datastore. DeleteFile, datastore. usługi filemanagement, datastore. UpdateVirtualMachineMetadata     |    --    |   -- |
|  Menedżer agentów ESX       |  --  |         --       |  Firma Microsoft wykonuje wszystkie operacje.  |
|  Folder                  |  Użytkownik CloudAdmin ma wszystkie uprawnienia do folderów.     |  --  |  --  |
|  Globalnie                  |  Global. CancelTask, Global. GlobalTag, Global. Health, Global. metody LogEvent, Global. ManageCustomFields, Global. ServiceManager, Global. SetCustomField, Global.SystemTag         |                  |    |
|  Host                    |  Host. Hbr. HbrManagement      |        --          |  Firma Microsoft wykonuje wszystkie inne operacje hosta.  |
|  InventoryService        |  InventoryService. tagowanie      |        --          |  --  |
|  Sieć                 |  Network. Assign    |                  |  Firma Microsoft wykonuje wszystkie inne operacje sieciowe.  |
|  Uprawnienia             |  --  |        --       |  Firma Microsoft wykonuje wszystkie operacje związane z uprawnieniami.  |
|  Magazyn oparty na profilach  |  --  |        --       |  Firma Microsoft wykonuje wszystkie operacje profilowania.  |
|  Zasób                |  Użytkownik CloudAdmin ma wszystkie uprawnienia do zasobów.        |      --       | --   |
|  Zaplanowane zadanie          |  Użytkownik CloudAdmin ma wszystkie uprawnienia ScheduleTask —.   |   --   | -- |
|  Sesje                |  Sessions. GlobalMessage, Sessions. ValidateSession      |   --   |  Firma Microsoft wykonuje wszystkie pozostałe operacje.  |
|  Widoki magazynu           |  StorageViews. View   |        --          |  Firma Microsoft wykonuje wszystkie operacje widoku magazynu (Konfiguracja usługi).  |
|  Zadania                   |  --  |  --   |  Firma Microsoft zarządza rozszerzeniami, które zarządzają zadaniami.  |
|  vApp                    |  Użytkownik CloudAdmin ma wszystkie uprawnienia vApp.  |  --  |  --  |
|  Maszyna wirtualna         |  Użytkownik CloudAdmin ma wszystkie uprawnienia VirtualMachine.  |  --  |  --  |
|  vService                |  Użytkownik CloudAdmin ma wszystkie uprawnienia vService.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>Dostęp i tożsamość menedżera NSX-T

Dostęp do Menedżera NSX-T można uzyskać przy użyciu konta "Administrator". To konto ma pełne uprawnienia i umożliwia tworzenie routerów T1, przełączników logicznych i wszystkich usług oraz zarządzanie nimi. Pełne uprawnienia w NSX-T zapewniają również dostęp do routera T0 NSX-T. Zmiana w routerze T0 może spowodować spadek wydajności sieci lub utratę dostępu do chmury prywatnej. Aby spełnić wymagania dotyczące pomocy technicznej, należy otworzyć żądanie pomocy technicznej w Azure Portal, aby zażądać wszelkich zmian w routerze T0 NSX-T.
  
## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest zapoznanie się z [pojęciami dotyczącymi uaktualniania chmury prywatnej][concepts-upgrades].

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md