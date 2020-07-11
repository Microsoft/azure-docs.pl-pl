---
title: Integracja Update Management Azure Automation z punktem końcowym systemu Windows Configuration Manager
description: W tym artykule opisano sposób konfigurowania Configuration Manager punktu końcowego w programie Update Management w celu wdrażania aktualizacji oprogramowania na klientach programu Manager.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 06/16/2020
ms.topic: conceptual
ms.openlocfilehash: 164db5d126ab9a22bce527b6197a463943b0fede
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86183192"
---
# <a name="integrate-update-management-with-windows-endpoint-configuration-manager"></a>Integracja Update Management z punktem końcowym systemu Windows Configuration Manager

Klienci, którzy zainwestowali w program Microsoft Endpoint Configuration Manager w celu zarządzania komputerami, serwerami i urządzeniami przenośnymi, również polegają na jej sile i dojrzałości w zarządzaniu aktualizacjami oprogramowania w ramach cyklu zarządzania aktualizacjami oprogramowania (SUM).

Można zgłaszać i aktualizować zarządzane serwery z systemem Windows, tworząc i wstępnie przemieszczając wdrożenia aktualizacji oprogramowania w punkcie końcowym systemu Windows Configuration Manager i uzyskując szczegółowy stan ukończonych wdrożeń aktualizacji przy użyciu [Update Management](automation-update-management.md). W przypadku używania Configuration Manager punktu końcowego systemu Windows do raportowania zgodności aktualizacji, ale nie do zarządzania wdrożeniami aktualizacji z serwerami z systemem Windows, można kontynuować raportowanie do programu Configuration Manager, podczas gdy aktualizacje zabezpieczeń są zarządzane za pomocą Azure Automation Update Management.

>[!NOTE]
>Chociaż Update Management obsługuje ocenę aktualizacji i stosowanie poprawek systemu Windows Server 2008 R2, nie obsługuje klientów zarządzanych przez punkt końcowy Configuration Manager z tym systemem operacyjnym.

## <a name="prerequisites"></a>Wymagania wstępne

* Musisz mieć [Azure Automation Update Management](automation-update-management.md) dodany do konta usługi Automation.
* Serwery z systemem Windows, które są obecnie zarządzane przez środowisko Configuration Manager Endpoint systemu Windows, również muszą zgłosić do obszaru roboczego Log Analytics, w którym włączono również Update Management.
* Ta funkcja jest włączona w punkcie końcowym systemu Windows Configuration Manager bieżącej gałęzi w wersji 1606 lub nowszej. Aby zintegrować system Windows Endpoint Configuration Manager centralną lokację administracyjną lub autonomiczną lokację główną z dziennikami Azure Monitor i zaimportować kolekcje, przejrzyj [Configuration Manager połączenia w celu Azure monitor dzienników](../azure-monitor/platform/collect-sccm.md).  
* Agenci systemu Windows muszą być skonfigurowani do komunikowania się z serwerem Windows Server Update Services (WSUS) lub mieć dostęp do Microsoft Update, jeśli nie otrzymają one aktualizacji zabezpieczeń z punktu końcowego systemu Windows Configuration Manager.

Sposób zarządzania klientami hostowanymi w usłudze Azure IaaS z istniejącym środowiskiem Endpoint Configuration Manager systemu Windows zależy głównie od połączenia między centrami danych platformy Azure a infrastrukturą użytkownika. To połączenie ma wpływ na wszelkie zmiany projektowe, które mogą być konieczne w celu przetworzenia infrastruktury punktu Configuration Manager końcowego systemu Windows i pokrewnego kosztu w celu obsługi tych niezbędnych zmian. Aby zrozumieć, jakie kwestie planowania należy ocenić przed kontynuowaniem, zobacz [Menedżer konfiguracji Azure — często zadawane pytania](/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="manage-software-updates-from-windows-endpoint-configuration-manager"></a>Zarządzanie aktualizacjami oprogramowania z punktu końcowego systemu Windows Configuration Manager

Aby kontynuować Zarządzanie wdrożeniami aktualizacji z punktu Configuration Manager końcowego systemu Windows, wykonaj następujące czynności. Azure Automation nawiązuje połączenie z Configuration Manager punktu końcowego systemu Windows w celu zastosowania aktualizacji na komputerach klienckich połączonych z obszarem roboczym Log Analytics. Aktualizacja zawartości jest dostępna z pamięci podręcznej komputera klienckiego, tak jakby wdrożenie było zarządzane przez program Windows Endpoint Configuration Manager.

1. Utwórz wdrożenie aktualizacji oprogramowania z lokacji najwyższego poziomu w hierarchii Configuration Manager punktu końcowego systemu Windows przy użyciu procesu opisanego w temacie [wdrażanie aktualizacji oprogramowania](/configmgr/sum/deploy-use/deploy-software-updates). Jedynym ustawieniem, które musi być skonfigurowane inaczej niż dla standardowego wdrożenia, jest opcja **Nie instaluj aktualizacji oprogramowania** służąca do sterowania zachowaniem pakietu wdrożeniowego podczas pobierania. To zachowanie jest zarządzane w Update Management przez utworzenie zaplanowanego wdrożenia aktualizacji w następnym kroku.

1. W Azure Automation wybierz pozycję **Update Management**. Utwórz nowe wdrożenie, postępując zgodnie z krokami opisanymi w temacie [Tworzenie wdrożenia aktualizacji](automation-tutorial-update-management.md#schedule-an-update-deployment) i wybierz pozycję **zaimportowane grupy** na liście rozwijanej **Typ** , aby wybrać odpowiednią kolekcję Configuration Manager punktu końcowego systemu Windows. Należy pamiętać o następujących ważnych kwestiach: a. Jeśli okno obsługi jest zdefiniowane w wybranej kolekcji urządzeń z systemem Windows Configuration Manager, elementy członkowskie kolekcji uznają je za ustawienie **czasu trwania** zdefiniowane w zaplanowanym wdrożeniu.
    b. Elementy członkowskie kolekcji docelowej muszą mieć połączenie z Internetem (bezpośrednio, za pośrednictwem serwera proxy lub z bramą Log Analytics).

Po zakończeniu wdrażania aktualizacji za pośrednictwem Azure Automation komputery docelowe, które są członkami wybranej grupy komputerów, będą instalować aktualizacje w zaplanowanym czasie z ich lokalnej pamięci podręcznej klienta. Możesz [wyświetlić stan wdrożenia aktualizacji](automation-tutorial-update-management.md#check-deployment-status), aby monitorować wyniki swojego wdrożenia.

## <a name="manage-software-updates-from-azure-automation"></a>Zarządzaj aktualizacjami oprogramowania z programu Azure Automation

Aby zarządzać aktualizacjami maszyn wirtualnych z systemem Windows Server, które są klientami Configuration Manager punktu końcowego Windows, należy skonfigurować zasady klienta, aby wyłączyć funkcję zarządzanie aktualizacjami oprogramowania dla wszystkich klientów zarządzanych przez Update Management. Domyślnie ustawienia klienta dotyczą wszystkich urządzeń w hierarchii. Aby uzyskać więcej informacji na temat tego ustawienia zasad i sposobu jego konfiguracji, zobacz [jak skonfigurować ustawienia klienta w Configuration Manager](/configmgr/core/clients/deploy/configure-client-settings).

Po wykonaniu tej zmiany konfiguracji należy utworzyć nowe wdrożenie, postępując zgodnie z krokami opisanymi w temacie [Tworzenie wdrożenia aktualizacji](automation-tutorial-update-management.md#schedule-an-update-deployment) i wybieraniem **grup zaimportowanych** na liście rozwijanej **Typ** , aby wybrać odpowiednią kolekcję Configuration Manager punktu końcowego systemu Windows.

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować harmonogram integracji, zobacz [Planowanie wdrożenia aktualizacji](automation-tutorial-update-management.md#schedule-an-update-deployment).
