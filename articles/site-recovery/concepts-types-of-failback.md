---
title: Powrót po awarii podczas odzyskiwania po awarii przy użyciu Azure Site Recovery | Microsoft Docs
description: Ten artykuł zawiera omówienie różnych typów powrotu po awarii i przeciwdziałań, które należy wziąć pod uwagę podczas awaryjnego powrotu po awarii w środowisku lokalnym w ramach usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: raynew
ms.openlocfilehash: c0eaf28f9aeb4050fd35a6036a53e3e91d00f3eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84691088"
---
# <a name="failback-of-vmware-vms-after-disaster-recovery-to-azure"></a>Powrót po awarii maszyn wirtualnych VMware po odzyskiwaniu po awarii na platformę Azure

Po przejściu w tryb failover na platformę Azure w ramach procesu odzyskiwania po awarii można wrócić do lokacji lokalnej. Istnieją dwa różne typy powrotu po awarii, które są możliwe z Azure Site Recovery: 

- Powrót po awarii do oryginalnej lokalizacji 
- Powrót po awarii do lokalizacji alternatywnej

Jeśli maszyna wirtualna VMware została przełączona w tryb failover, możesz wrócić do tej samej źródłowej maszyny wirtualnej, jeśli jeszcze nie istnieje. W tym scenariuszu tylko te zmiany są replikowane z powrotem. Ten scenariusz jest znany jako **odzyskiwanie do lokalizacji oryginalnej**. Jeśli lokalna maszyna wirtualna nie istnieje, scenariusz jest **odzyskiwaniem do lokalizacji alternatywnej**.

> [!NOTE]
> Powrót po awarii do oryginalnego serwera vCenter i konfiguracji może zakończyć się niepowodzeniem. Nie można wdrożyć nowego serwera konfiguracji i ponownie go używać. Ponadto nie można dodać nowego programu vCenter do istniejącego serwera konfiguracji i powrotu po awarii do nowego programu vCenter.

## <a name="original-location-recovery-olr"></a>Odzyskiwanie do oryginalnej lokalizacji (OLR)
Jeśli zdecydujesz się na powrót po awarii do oryginalnej maszyny wirtualnej, muszą zostać spełnione następujące warunki:

* Jeśli maszyna wirtualna jest zarządzana przez serwer vCenter, Host ESX głównego celu powinien mieć dostęp do magazynu danych maszyny wirtualnej.
* Jeśli maszyna wirtualna znajduje się na hoście ESX, ale nie jest zarządzana przez program vCenter, dysk twardy maszyny wirtualnej musi znajdować się w magazynie danych, do którego może uzyskać dostęp Host głównego elementu docelowego.
* Jeśli maszyna wirtualna znajduje się na hoście ESX i nie używa programu vCenter, przed ponownym włączeniem ochrony należy ukończyć odnajdywanie hosta ESX głównego obiektu docelowego. Dotyczy to również sytuacji, w której serwery fizyczne są błędne.
* Można wrócić po awarii do wirtualnej sieci SAN (sieci vSAN) lub dysku opartego na mapowaniu RAW (RDM), jeśli dyski już istnieją i są połączone z lokalną maszyną wirtualną.

> [!IMPORTANT]
> Ważne jest włączenie opcji Disk. enableUUID = TRUE, aby podczas powrotu po awarii usługa Azure Site Recovery mogła identyfikować oryginalną maszynę wirtualną w maszynie wirtualnej, do której będą zapisywane oczekujące zmiany. Jeśli ta wartość nie jest ustawiona na wartość TRUE, usługa próbuje zidentyfikować odpowiednie lokalne VMDK na podstawie najlepszego wysiłku. Jeśli nie można znaleźć odpowiedniego dysku VMDK, tworzy dodatkowy dysk, a dane są zapisywane w tym pliku.

## <a name="alternate-location-recovery-alr"></a>Odzyskiwanie do lokalizacji alternatywnej (ALR)
Jeśli lokalna maszyna wirtualna nie istnieje przed ponowną ochroną maszyny wirtualnej, scenariusz jest nazywany odzyskiwaniem do lokalizacji alternatywnej. Przepływ pracy ponownego włączania ochrony ponownie tworzy lokalną maszynę wirtualną. Spowoduje to również pobranie pełnych danych.

* Po powrocie po awarii do lokalizacji alternatywnej maszyna wirtualna jest odzyskiwana do tego samego hosta ESX, na którym wdrożono główny serwer docelowy. Magazyn danych użyty do utworzenia dysku będzie tym samym magazynem danych, który został wybrany podczas ponownej ochrony maszyny wirtualnej.
* Można wrócić do trybu failover tylko w przypadku systemu plików maszyny wirtualnej (VMFS) lub magazynu danych sieci vSAN. Jeśli masz RDM, ponowne Włączanie ochrony i powrót po awarii nie będzie możliwe.
* Ponowne włączanie ochrony obejmuje jeden duży początkowy transfer danych, po którym następuje zmiana. Ten proces istnieje, ponieważ maszyna wirtualna nie istnieje w środowisku lokalnym. Kompletne dane należy zreplikować z powrotem. To ponowne włączenie ochrony zajmie również więcej czasu niż odzyskiwanie oryginalnej lokalizacji.
* Nie można wrócić do trybu powrotu do dysków opartych na RDM. W magazynie danych VMFS/sieci vSAN można tworzyć tylko nowe dyski maszyny wirtualnej (VMDK).

> [!NOTE]
> Maszyna fizyczna, po przełączeniu w tryb failover na platformę Azure, może się nie powiódł tylko jako maszyna wirtualna VMware. Ten sam przepływ pracy jest zgodny z odzyskiwaniem lokalizacji alternatywnej. Upewnij się, że wykryjesz co najmniej jeden główny serwer docelowy i wymagane hosty ESX/ESXi, do których należy powrócić po awarii.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z instrukcjami, aby wykonać [operację powrotu po awarii](vmware-azure-failback.md).

