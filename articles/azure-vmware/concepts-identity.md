---
title: Pojęcia — tożsamość i dostęp
description: Dowiedz się więcej na temat pojęć dotyczących tożsamości i uzyskiwania dostępu do rozwiązania Azure VMware
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 07a7ac8093524ef4240b8f7607d649520b9439e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586254"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Pojęcia dotyczące tożsamości rozwiązań VMware platformy Azure

Chmury prywatne rozwiązań VMware platformy Azure są obsługiwane za pomocą programu vCenter Server i Menedżera NSX-T. Program vCenter służy do zarządzania obciążeniami maszyny wirtualnej. Menedżer NSX-T służy do zarządzania i zwiększania sieci prywatnej chmury.

Dostęp do programu vCenter i zarządzanie tożsamościami odbywa się przy użyciu uprawnień grupy Build CloudAdmin. Menedżer NSX-T używa ograniczonych uprawnień administratora. Ma to charakter usługi zarządzanej i zapewnia, że Twoja Platforma chmury prywatnej jest uaktualniana z najnowszymi funkcjami i poprawkami, zgodnie z oczekiwaniami.  Aby uzyskać więcej informacji, zobacz artykuł dotyczący [pojęć dotyczących uaktualnień w chmurze prywatnej][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>dostęp i tożsamość vCenter

Grupa vCenter CloudAdmin definiuje i udostępnia uprawnienia w programie vCenter. Innym rozwiązaniem jest zapewnienie dostępu i tożsamości poprzez integrację logowania jednokrotnego usługi vCenter LDAP przy użyciu Azure Active Directory. Integrację należy włączyć po wdrożeniu chmury prywatnej. 

W tabeli przedstawiono uprawnienia **CloudAdmin** i **CloudGlobalAdmin** .

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

Użyj konta *administratora* , aby uzyskać dostęp do Menedżera NSX-T. Ma pełne uprawnienia i umożliwia tworzenie bram (T1), segmentów (przełączników logicznych) i wszystkich usług oraz zarządzanie nimi. To konto zapewnia również dostęp do bramy NSX-T warstwy 0 (T0). Pamiętaj o wprowadzeniu takich zmian, ponieważ może to spowodować spadek wydajności sieci lub brak dostępu do chmury prywatnej. Otwórz żądanie obsługi w Azure Portal, aby zażądać wszelkich zmian w bramie "T0" NSX-T.
  
## <a name="next-steps"></a>Następne kroki

Teraz, gdy został objęty usługą Azure VMware dostęp do rozwiązań i pojęcia związane z tożsamościami, warto zapoznać się z tematem:

- [Pojęcia dotyczące uaktualniania chmury prywatnej](concepts-upgrades.md).
- [vSphere kontroli dostępu opartej na rolach dla rozwiązań VMware platformy Azure](concepts-role-based-access-control.md).
- [Jak włączyć zasób rozwiązania VMware dla platformy Azure](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
