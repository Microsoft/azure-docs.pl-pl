---
title: Korzystanie z usługi Azure Automation Update Management z klientami programu Configuration Manager
description: Ten artykuł ma na celu pomóc skonfigurować program Microsoft Endpoint Configuration Manager z tego rozwiązania do wdrażania aktualizacji oprogramowania dla klientów ConfigMgr.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 32a077c476d9669c3f32bd4040fdc8ff90156c19
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678732"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Wdrażanie aktualizacji dla klientów programu Microsoft Endpoint Configuration Manager za pomocą zarządzania aktualizacjami

Klienci, którzy zainwestowali w program Microsoft Endpoint Configuration Manager w celu zarządzania komputerami, serwerami i urządzeniami przenośnymi, również polegają na jego sile i dojrzałości w zarządzaniu aktualizacjami oprogramowania w ramach cyklu zarządzania aktualizacjami oprogramowania (SUM).

Zarządzane serwery systemu Windows można zgłaszać i aktualizować, tworząc i preicyjniejniejniejsną wdrożeń oprogramowania w programie Menedżer konfiguracji, a także uzyskać szczegółowy stan zakończonych wdrożeń aktualizacji przy użyciu [usługi Zarządzanie aktualizacjami](automation-update-management.md). Jeśli program Menedżer konfiguracji jest używany do raportowania zgodności aktualizacji, ale nie do zarządzania wdrożeniami aktualizacji na serwerach systemu Windows, można kontynuować raportowanie do programu Menedżer konfiguracji, podczas gdy aktualizacje zabezpieczeń są zarządzane za pomocą rozwiązania do zarządzania aktualizacjami.

## <a name="prerequisites"></a>Wymagania wstępne

* Do konta automatyzacji musi być dodane [rozwiązanie do zarządzania aktualizacjami.](automation-update-management.md)
* Serwery systemu Windows obecnie zarządzane przez środowisko programu Menedżer konfiguracji muszą również zgłaszać się do obszaru roboczego usługi Log Analytics, w który ma również włączone rozwiązanie Do zarządzania aktualizacjami.
* Ta funkcja jest włączona w programie Configuration Manager bieżącej gałęzi w wersji 1606 lub nowszej. Aby zintegrować centralną lokację administracyjną programu Configuration Manager lub autonomiczną lokację główną z dziennikami i kolekcjami importu usługi Azure Monitor, zapoznaj [się z dziennikami connect configuration manager do dzienników usługi Azure Monitor](../azure-monitor/platform/collect-sccm.md).  
* Agenci dla systemu Windows muszą być skonfigurowani do komunikowania się z serwerem Windows Server Update Services (WSUS) albo mieć dostęp do usługi Microsoft Update, jeśli nie otrzymują aktualizacji zabezpieczeń z programu Configuration Manager.   

To, w jaki sposób zarządzasz klientami hostowanymi w usłudze Azure IaaS za pomocą istniejącego środowiska programu Configuration Manager, zależy głównie od połączenia istniejącego między centrami danych platformy Azure a Twoją infrastrukturą. Połączenie to ma wpływ na wszelkie zmiany projektowe, których wprowadzenie do infrastruktury programu Configuration Manager może być konieczne, oraz wpływa na powiązane koszty obsługi tych niezbędnych zmian. Aby zrozumieć, jakie kwestie planowania należy ocenić przed kontynuowaniem, zobacz [Menedżer konfiguracji Azure — często zadawane pytania](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Konfigurowanie

### <a name="manage-software-updates-from-configuration-manager"></a>Zarządzanie aktualizacjami oprogramowania z programu Configuration Manager 

Jeśli chcesz kontynuować zarządzanie wdrożeniami aktualizacji z programu Configuration Manager, wykonaj następujące kroki. Usługa Azure Automation łączy się z programem Configuration Manager w celu zastosowania aktualizacji do komputerów klienckich połączonych z obszarem roboczym usługi Log Analytics. Zawartość aktualizacji jest dostępna w pamięci podręcznej komputera klienckiego tak, jakby wdrożenie było zarządzane przez program Configuration Manager.

1. Utwórz wdrożenie aktualizacji oprogramowania z lokacji najwyższego poziomu w hierarchii programu Menedżer konfiguracji przy użyciu procesu opisanego w [programie Wdrażanie aktualizacji oprogramowania](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). Jedynym ustawieniem, które musi być skonfigurowane inaczej niż dla standardowego wdrożenia, jest opcja **Nie instaluj aktualizacji oprogramowania** służąca do sterowania zachowaniem pakietu wdrożeniowego podczas pobierania. To zachowanie jest zarządzane przez rozwiązanie zarządzanie aktualizacjami przez utworzenie zaplanowanego wdrożenia aktualizacji w następnym kroku.

1. W usłudze Azure Automation wybierz pozycję **Zarządzanie aktualizacjami**. Utwórz nowe wdrożenie zgodnie z instrukcjami opisanymi w temacie [Tworzenie wdrożenia aktualizacji](automation-tutorial-update-management.md#schedule-an-update-deployment) i wybierz pozycję **Importowane grupy** na stronie rozwijanej **Typ,** aby wybrać odpowiednią kolekcję programu Menedżer konfiguracji. Należy pamiętać o następujących ważnych punktach: a. Jeśli okno konserwacji jest zdefiniowane w wybranej kolekcji urządzeń programu Menedżer konfiguracji, członkowie kolekcji honorują go zamiast **ustawienia Czas trwania** zdefiniowanego w zaplanowanym wdrożeniu.
    b. Członkowie kolekcji docelowej muszą mieć połączenie z Internetem (bezpośrednio, za pośrednictwem serwera proxy lub za pośrednictwem bramy usługi Log Analytics).

Po zakończeniu wdrażania aktualizacji za pośrednictwem usługi Azure Automation komputery docelowe, które są członkami wybranej grupy komputerów, zainstalują aktualizacje o zaplanowanym czasie z lokalnej pamięci podręcznej klienta. Możesz [wyświetlić stan wdrożenia aktualizacji](automation-tutorial-update-management.md#view-results-of-an-update-deployment), aby monitorować wyniki swojego wdrożenia.

### <a name="manage-software-updates-from-azure-automation"></a>Zarządzanie aktualizacjami oprogramowania z usługi Azure Automation

Do zarządzania aktualizacjami maszyn wirtualnych z systemem Windows Server, które są klientami programu Configuration Manager, należy skonfigurować zasady klienta, aby wyłączyć funkcję zarządzania aktualizacjami oprogramowania dla wszystkich klientów zarządzanych przez to rozwiązanie. Domyślnie ustawienia klienta dotyczą wszystkich urządzeń w hierarchii. Aby uzyskać więcej informacji na temat tego ustawienia zasad i sposobu jego konfigurowania, zapoznaj się z [instrukcjami konfigurowania ustawień klienta w programie Menedżer konfiguracji](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings).

Po wykonaniu tej zmiany konfiguracji należy utworzyć nowe wdrożenie zgodnie z instrukcjami opisanymi w temacie [Tworzenie wdrożenia aktualizacji](automation-tutorial-update-management.md#schedule-an-update-deployment) i wybierz pozycję **Zaimportowane grupy** z listy rozwijanej **Typ,** aby wybrać odpowiednią kolekcję programu Menedżer konfiguracji.

## <a name="next-steps"></a>Następne kroki

