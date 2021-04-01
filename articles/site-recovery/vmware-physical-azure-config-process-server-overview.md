---
title: Informacje o konfiguracji Azure Site Recovery/procesie/głównym serwerze docelowym
description: Ten artykuł zawiera omówienie konfiguracji, procesu i głównych serwerów docelowych, które są używane podczas konfigurowania odzyskiwania po awarii lokalnych maszyn wirtualnych VMware na platformie Azure przy użyciu Azure Site Recovery
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80062087"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Informacje o składnikach Site Recovery (Konfiguracja, proces, główny cel)

W tym artykule opisano konfigurację, proces i główne serwery docelowe używane przez usługę [Site Recovery](site-recovery-overview.md) do replikowania maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure.

## <a name="configuration-server"></a>Serwer konfiguracji

W przypadku odzyskiwania po awarii lokalnych maszyn wirtualnych programu VMware i serwerów fizycznych należy wdrożyć Site Recovery lokalny serwer konfiguracji.

**Ustawienie** | **Szczegóły** | **Linki**
--- | --- | ---
**Składniki**  | Na komputerze serwera konfiguracji są uruchamiane wszystkie lokalne składniki Site Recovery, takie jak serwer konfiguracji, serwer przetwarzania oraz główny serwer docelowy.<br/><br/> Po skonfigurowaniu serwera konfiguracji wszystkie składniki są instalowane automatycznie. | [Przeczytaj](vmware-azure-common-questions.md#configuration-server) często zadawane pytania dotyczące serwera konfiguracji.
**Role** | Serwer konfiguracji służy do koordynowania komunikacji między środowiskiem lokalnym i platformą Azure oraz do zarządzania replikacją danych. | Dowiedz się więcej o architekturze odzyskiwania po awarii dla oprogramowania [VMware](vmware-azure-architecture.md) i [serwera fizycznego](physical-azure-architecture.md) na platformie Azure.
**Wymagania dotyczące oprogramowania VMware** | W przypadku odzyskiwania po awarii lokalnych maszyn wirtualnych programu VMware należy zainstalować i uruchomić serwer konfiguracji jako lokalną maszynę wirtualną VMware o wysokiej dostępności. | [Dowiedz się więcej o](vmware-azure-deploy-configuration-server.md#prerequisites) wymaganiach wstępnych.
**Wdrożenie VMware** | Zalecamy wdrożenie serwera konfiguracji przy użyciu pobranego szablonu komórki jajowe. Ta metoda zapewnia prosty sposób konfigurowania serwera konfiguracji, który jest zgodny ze wszystkimi wymaganiami i wymaganiami wstępnymi.<br/><br/> Jeśli z jakiegoś powodu nie można wdrożyć maszyny wirtualnej VMware przy użyciu szablonu komórki jajowe, można ręcznie skonfigurować maszyny z serwerem konfiguracji, zgodnie z poniższym opisem w przypadku odzyskiwania po awarii maszyny fizycznej. | [Wdróż](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) z szablonem komórki jajowe.
**Wymagania dotyczące serwera fizycznego** | W przypadku odzyskiwania po awarii na lokalnych serwerach fizycznych należy ręcznie wdrożyć serwer konfiguracji. | [Dowiedz się więcej o](physical-azure-set-up-source.md#prerequisites) wymaganiach wstępnych.
**Wdrożenie serwera fizycznego** | Jeśli nie można go zainstalować jako maszyny wirtualnej VMware, można ją zainstalować na serwerze fizycznym. | [Wdróż](physical-azure-set-up-source.md#set-up-the-source-environment) ręcznie serwer konfiguracji.

## <a name="process-server"></a>Serwer przetwarzania

Serwer przetwarzania obsługuje dane replikacji podczas pracy w trybie failover i powrotu po awarii, a następnie instaluje usługę mobilności dla lokalnych maszyn wirtualnych VMware i serwerów fizycznych.

**Ustawienie** | **Szczegóły** | **Linki**
--- | --- | ---
**Wdrożenie**  | Domyślnie, gdy serwer konfiguracji jest wdrożony, serwer przetwarzania jest zainstalowany. <br/><br/> Serwer przetwarzania lokalnego jest wymagany do odzyskiwania po awarii i replikacji lokalnych maszyn wirtualnych VMware i serwerów fizycznych. | [Dowiedz się więcej](vmware-azure-architecture.md#architectural-components).
**Rola (lokalna**) | Odbiera dane replikacji z maszyn, na których włączono obsługę replikacji. <br/><br/> Optymalizuje dane replikacji za pomocą buforowania, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage. <br/><br/> Wykonuje instalację wypychaną usługi Site Recovery Mobility na lokalnych maszynach wirtualnych programu VMware i serwerach fizycznych, które mają być replikowane. <br/><br/> Wykonuje automatyczne odnajdowanie maszyn lokalnych. | [Dowiedz się więcej](vmware-azure-enable-replication.md).
**Rola (powrót po awarii z platformy Azure)** | Po przełączeniu w tryb failover z lokacji lokalnej należy skonfigurować serwer przetwarzania na platformie Azure jako maszynę wirtualną platformy Azure, aby obsłużyć powrót po awarii do lokalizacji lokalnej.<br/><br/> Serwer przetwarzania na platformie Azure jest tymczasowy. Maszynę wirtualną platformy Azure można usunąć po powrocie po awarii. | [Dowiedz się więcej](vmware-azure-set-up-process-server-azure.md).
**Skalowanie** | W przypadku większych wdrożeń można skonfigurować dodatkowe serwery przetwarzania skalowalnego w poziomie. Dodatkowe serwery skalowalne w poziomie, obsługujące większą liczbę maszyn replikowanych oraz większe ilości ruchu związanego z replikacją.<br/><br/> Można przenosić maszyny między dwoma serwerami przetwarzania, aby równoważyć obciążenie ruchem związanym z replikacją. | [Dowiedz się więcej](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>Główny serwer docelowy

Główny serwer docelowy służy do obsługi replikacji danych podczas powrotu po awarii z platformy Azure.

- Domyślnie główny serwer docelowy jest zainstalowany na serwerze konfiguracji.
- W przypadku dużych wdrożeń można dodać dodatkowy, oddzielny główny serwer docelowy na potrzeby powrotu po awarii.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [architekturą](vmware-azure-architecture.md) odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych.
- Zapoznaj się z [wymaganiami i wymaganiami wstępnymi](vmware-physical-azure-support-matrix.md) dotyczącymi odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure.
