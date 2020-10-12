---
title: Konfigurowanie ustawień źródłowych odzyskiwania po awarii programu VMware na platformie Azure za pomocą Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania środowiska lokalnego do replikowania maszyn wirtualnych VMware na platformę Azure przy użyciu Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: afd3979690b8952c915a49099ee04b3d416031fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88189737"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Konfigurowanie środowiska źródłowego na potrzeby replikacji oprogramowania VMware do platformy Azure

W tym artykule opisano sposób konfigurowania źródłowego środowiska lokalnego do replikowania maszyn wirtualnych VMware na platformę Azure. Artykuł zawiera kroki umożliwiające wybranie scenariusza replikacji, skonfigurowanie maszyny lokalnej jako serwera konfiguracji Site Recovery i automatyczne odnajdowanie lokalnych maszyn wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne

W artykule założono, że masz już:

- Zaplanowano wdrożenie z pomocą [planista wdrażania usługi Azure Site Recovery](site-recovery-deployment-planner.md). Dzięki temu można przydzielić wystarczającą przepustowość na podstawie dziennego współczynnika zmian danych, aby zaspokoić żądany cel punktu odzyskiwania (RPO).
- [Skonfiguruj zasoby](tutorial-prepare-azure.md) w [Azure Portal](https://portal.azure.com).
- [Konfigurowanie lokalnego programu VMware, w](vmware-azure-tutorial-prepare-on-premises.md)tym dedykowanego konta do automatycznego odnajdywania.

## <a name="choose-your-protection-goals"></a>Wybierz cele ochrony

1. W obszarze **Magazyny usługi Recovery Services** wybierz nazwę magazynu. W tym scenariuszu jest używany magazyn **ContosoVMVault**.
2. W obszarze **Wprowadzenie** wybierz pozycję Site Recovery. Następnie wybierz pozycję **Przygotowywanie infrastruktury**.
3. W obszarze **cel ochrony**, gdzie znajdują się  >  **maszyny**, wybierz pozycję **lokalna**.
4. W obszarze **Gdzie chcesz zreplikować maszyny** wybierz pozycję **Na platformę Azure**.
5. W obszarze **Czy maszyny są zwirtualizowane** wybierz pozycję **Tak, z funkcją VMware vSphere Hypervisor**. Następnie wybierz przycisk **OK**.

## <a name="set-up-the-configuration-server"></a>Konfigurowanie serwera konfiguracji

Serwer konfiguracji można skonfigurować jako lokalną maszynę wirtualną VMware za pomocą szablonu Open Virtualization Application (komórki jajowe). [Dowiedz się więcej](./vmware-azure-architecture.md) o składnikach, które zostaną zainstalowane na maszynie wirtualnej programu VMware.

1. Dowiedz się więcej o [wymaganiach wstępnych](vmware-azure-deploy-configuration-server.md#prerequisites) dotyczących wdrażania serwera konfiguracji.
2. [Sprawdź numery pojemności](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements) dla wdrożenia.
3. [Pobierz](vmware-azure-deploy-configuration-server.md#download-the-template) i [zaimportuj](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) szablon komórki jajowe, aby skonfigurować lokalną maszynę wirtualną VMware, na której jest uruchomiony serwer konfiguracji. Licencja dostarczona z szablonem jest licencją ewaluacyjną i jest ważna przez 180 dni. Opublikuj ten okres, aby klient mógł aktywować system Windows przy użyciu licencji z licencją.
4. Włącz maszynę wirtualną VMware i [zarejestruj ją](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) w magazynie Recovery Services.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Azure Site Recovery wykluczenia folderu z programu antywirusowego

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Jeśli oprogramowanie antywirusowe jest aktywne na maszynie źródłowej

Jeśli maszyna źródłowa ma aktywne oprogramowanie antywirusowe, folder instalacji powinien zostać wykluczony. Dlatego Wyklucz *C:\ProgramData\ASR\agent* folderu w celu zapewnienia płynnej replikacji.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Jeśli oprogramowanie antywirusowe jest aktywne na serwerze konfiguracji

Wyklucz następujące foldery ze oprogramowania antywirusowego w celu zapewnienia płynnej replikacji, aby uniknąć problemów z łącznością

- C:\Program Files\Microsoft Azure Recovery Services Agent.
- Dostawca C:\Program Files\Microsoft Azure Site Recovery
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
- C:\Program Files\Microsoft Azure Site Recovery Narzędzie do zbierania błędów 
  - C:\thirdparty
  - C:\Temp
  - C:\strawberry
  - C:\ProgramData\MySQL
  - C:\Program Files (x86) \MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Katalog instalacji serwera Site Recovery. Na przykład: E:\Program Files (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Jeśli oprogramowanie antywirusowe jest aktywne na serwerze przetwarzania skalowalnego w poziomie/głównym miejscu docelowym

Wyklucz następujące foldery ze oprogramowania antywirusowego

1. C:\Program Files\Microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Azure Site Recovery katalog instalacji serwera przetwarzania z równoważeniem obciążenia, przykład: C:\Program Files (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-the-linux-master-target"></a>Jeśli oprogramowanie antywirusowe jest aktywne w głównym elemencie docelowym systemu Linux

Wyklucz następujące foldery ze oprogramowania antywirusowego

1.  /usr/local/ASR
2.  /usr/local/InMage
3.  /var/log/vxlogs
4.  /var/log
5.  /var/log/ApplicationPolicyLogs
6.  /var/log/ASRsetuptelemetry
7.  /var/log/ASRsetuptelemetry_uploaded


## <a name="next-steps"></a>Następne kroki
[Konfigurowanie środowiska docelowego](./vmware-azure-set-up-target.md) 
