---
title: Korzystanie z pulpitu wirtualnego systemu Windows z Azure NetApp Files | Microsoft Docs
description: Zapewnia wskazówki dotyczące najlepszych rozwiązań i przykładowe plany dotyczące wdrażania pulpitu wirtualnego systemu Windows przy użyciu Azure NetApp Files.
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
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: 0cd1f6210fbdb74e3fd511150157dccca3e92dda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91932468"
---
# <a name="benefits-of-using-azure-netapp-files-with-windows-virtual-desktop"></a>Korzyści związane z używaniem usługi Azure NetApp Files w usłudze Windows Virtual Desktop 

Ten artykuł zawiera wskazówki dotyczące najlepszych rozwiązań dotyczących wdrażania pulpitu wirtualnego systemu Windows (WVD) przy użyciu Azure NetApp Files.

Azure NetApp Files to wysoce wydajna usługa magazynu plików z platformy Azure. Może ona zapewnić 450 000 operacji we/wy na sekundę, co pozwala na obsługę bardzo dużej skali wdrożeń pulpitów wirtualnych systemu Windows. Możesz dostosować przepustowość i zmienić poziom usług woluminów Azure NetApp Files na żądanie niemal natychmiast bez wstrzymywania operacji we/wy przy zachowaniu dostępu do płaszczyzny danych. Ta funkcja pozwala łatwo zoptymalizować skalę wdrażania usługi WVD pod kątem kosztów. Istnieje również możliwość tworzenia wydajnych migawek woluminów w danym momencie bez wpływu na wydajność woluminu. Ta funkcja umożliwia wycofanie pojedynczych [kontenerów profili użytkowników FSLogix](../virtual-desktop/store-fslogix-profile.md) za pomocą kopii z `~snapshot` katalogu lub natychmiastowe wycofanie całego woluminu przy użyciu funkcji przywracania woluminu.  W przypadku migawek do 255 (rotacji) w celu ochrony woluminu przed utratą lub uszkodzeniem danych Administratorzy mogą w wielu szansach cofnąć to, co zostało zrobione.

## <a name="sample-blueprints"></a>Przykładowe plany

Następujące przykładowe plany pokazują integrację pulpitu wirtualnego systemu Windows z Azure NetApp Files. W scenariuszu pulpitu w puli użytkownicy są kierowani do najlepszej dostępnej sesji (w [trybie szerokiej](../virtual-desktop/host-pool-load-balancing.md#breadth-first-load-balancing-method)) w puli przy użyciu [maszyn wirtualnych wielosesyjnych](../virtual-desktop/windows-10-multisession-faq.md#what-is-windows-10-enterprise-multi-session). Z drugiej strony pulpity osobiste są zastrzeżone dla scenariuszy, w których każdy użytkownik ma własną maszynę wirtualną.

### <a name="pooled-desktop-scenario"></a>Scenariusz związany z pulpitem w puli

W przypadku scenariusza w puli zespół pulpitów wirtualnych systemu Windows [zaleca](/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations) następujące wskazówki według liczby użytkowników do vCPU. Należy pamiętać, że w tym zaleceniu nie określono rozmiaru maszyny wirtualnej.

|     Typ obciążenia     |     Jasny    |     Śred.    |     Ciężki    |
|-----------------------|--------------|---------------|--------------|
|     Użytkownicy na vCPU    |     6        |     4         |     2        |


To zalecenie zostało potwierdzone przez test LoginVSI użytkownika na 500, co oznacza, że na każdej D16as_V4 maszyny wirtualnej zostanie zarejestrowana około 62 "wiedza/średnia" użytkowników. 

Przykładowo w przypadku 62 użytkowników na D16as_V4 maszynę wirtualną Azure NetApp Files mogą łatwo obsługiwać 60 000 użytkowników na środowisko. Testowanie w celu oceny górnego limitu D32as_v4 maszyny wirtualnej jest w toku. Jeśli zalecenie WVD użytkownika na vCPU ma wartość true dla D32as_v4, więcej niż 120 000 użytkowników będzie zmieścić się na maszynach wirtualnych 1 000 przed Broaching [limitu 1 000 IP VNET](./azure-netapp-files-network-topologies.md), jak pokazano na poniższej ilustracji.  

![Scenariusz z pulpitem w puli pulpitów wirtualnych systemu Windows](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

### <a name="personal-desktop-scenario"></a>Osobisty scenariusz pulpitu 

W scenariuszu Personal Desktop na poniższej ilustracji przedstawiono zalecenie dotyczące architektury ogólnego przeznaczenia. Użytkownicy są zamapowane do określonych zasobników stacjonarnych, a każdy z nich ma tylko maszyny wirtualne 1 000, pozostawiając miejsce na propagowanie adresów IP z sieci wirtualnej zarządzania. Azure NetApp Files może łatwo obsłużyć 900 komputerów osobistych w jednej sieci wirtualnej w puli hostów z dwiema sesjami, a rzeczywista liczba maszyn wirtualnych równa 1 000 pomniejszona o liczbę hostów zarządzania znalezionych w sieci wirtualnej centrum. Jeśli potrzebujesz więcej osobistych pulpitów, można łatwo dodać więcej puli (pul hostów i sieci wirtualnych), jak pokazano na poniższej ilustracji. 

![Klasyczny scenariusz pulpitu wirtualnego systemu Windows](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

W przypadku kompilowania architektury opartej na bazie danych w taki sposób, przypisywanie użytkowników do poprawnego użycia przy logowaniu ma znaczenie dla zapewnienia, że użytkownicy będą zawsze mogli znaleźć swoje profile użytkowników. 

## <a name="next-steps"></a>Następne kroki

- [Architektury rozwiązań używające usługi Azure NetApp Files](azure-netapp-files-solution-architectures.md)