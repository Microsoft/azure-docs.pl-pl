---
title: Integracja Update Management Azure Automation z punktem końcowym firmy Microsoft Configuration Manager
description: W tym artykule opisano sposób konfigurowania Configuration Manager punktu końcowego w programie Update Management w celu wdrażania aktualizacji oprogramowania na klientach programu Manager.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: a848c7c15bf786ba26b8a1fdb1dab41b9aa20b8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575771"
---
# <a name="integrate-update-management-with-microsoft-endpoint-configuration-manager"></a>Integracja Update Management z punktem końcowym firmy Microsoft Configuration Manager

Klienci, którzy zainwestowali w program Microsoft Endpoint Configuration Manager w celu zarządzania komputerami, serwerami i urządzeniami przenośnymi, również polegają na jej sile i dojrzałości w zarządzaniu aktualizacjami oprogramowania w ramach cyklu zarządzania aktualizacjami oprogramowania (SUM).

Możesz raportować i aktualizować zarządzane serwery z systemem Windows, tworząc i wstępnie przemieszczając wdrożenia aktualizacji oprogramowania w punkcie Configuration Manager końcowym firmy Microsoft, a następnie pobierzesz szczegółowy stan ukończonych wdrożeń aktualizacji przy użyciu [Update Management](overview.md). W przypadku korzystania z programu Microsoft Endpoint Configuration Manager na potrzeby raportowania zgodności aktualizacji, ale nie do zarządzania wdrożeniami aktualizacji na serwerach z systemem Windows, można kontynuować raportowanie do usługi Microsoft Endpoint Configuration Manager, gdy aktualizacje zabezpieczeń są zarządzane za pomocą Azure Automation Update Management.

>[!NOTE]
>Chociaż Update Management obsługuje ocenę aktualizacji i stosowanie poprawek systemu Windows Server 2008 R2, nie obsługuje klientów zarządzanych przez program Microsoft Endpoint Configuration Manager, na których działa ten system operacyjny.

## <a name="prerequisites"></a>Wymagania wstępne

* Musisz mieć [Azure Automation Update Management](overview.md) dodany do konta usługi Automation.
* Serwery z systemem Windows, które są obecnie zarządzane przez środowisko Microsoft Endpoint Configuration Manager, również muszą zgłosić do obszaru roboczego Log Analytics, dla którego włączono również Update Management.
* Ta funkcja jest włączona w programie Microsoft Endpoint Configuration Manager bieżącej gałęzi w wersji 1606 lub nowszej. Aby zintegrować program Microsoft Endpoint Configuration Manager centralną lokację administracyjną lub autonomiczną lokację główną z dziennikami Azure Monitor i importowania kolekcji, przejrzyj [Configuration Manager połączenia w celu Azure monitor dzienników](../../azure-monitor/logs/collect-sccm.md).  
* Agenci systemu Windows muszą być skonfigurowani do komunikowania się z serwerem Windows Server Update Services (WSUS) lub mieć dostęp do Microsoft Update, jeśli nie otrzymają one aktualizacji zabezpieczeń z usługi Microsoft Endpoint Configuration Manager.

Sposób zarządzania klientami hostowanymi w usłudze Azure IaaS z istniejącym środowiskiem Microsoft Endpoint Configuration Manager zależy głównie od połączenia między centrami danych platformy Azure a infrastrukturą użytkownika. To połączenie ma wpływ na wszelkie zmiany projektowe, które mogą być konieczne w przypadku infrastruktury Microsoft Endpoint Configuration Manager i pokrewnego kosztu do obsługi tych niezbędnych zmian. Aby zrozumieć, jakie kwestie planowania należy ocenić przed kontynuowaniem, zobacz [Menedżer konfiguracji Azure — często zadawane pytania](/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="manage-software-updates-from-microsoft-endpoint-configuration-manager"></a>Zarządzanie aktualizacjami oprogramowania z punktu końcowego firmy Microsoft Configuration Manager

Wykonaj poniższe czynności, jeśli zamierzasz kontynuować Zarządzanie wdrożeniami aktualizacji z punktu końcowego firmy Microsoft Configuration Manager. Azure Automation nawiązuje połączenie z usługą Microsoft Endpoint Configuration Manager w celu zastosowania aktualizacji na komputerach klienckich połączonych z obszarem roboczym Log Analytics. Aktualizacja zawartości jest dostępna z pamięci podręcznej komputera klienckiego, tak jakby wdrożenie było zarządzane przez program Microsoft Endpoint Configuration Manager.

1. Utwórz wdrożenie aktualizacji oprogramowania z lokacji najwyższego poziomu w hierarchii programu Microsoft Endpoint Configuration Manager przy użyciu procesu opisanego w temacie [wdrażanie aktualizacji oprogramowania](/configmgr/sum/deploy-use/deploy-software-updates). Jedynym ustawieniem, które musi być skonfigurowane inaczej niż dla standardowego wdrożenia, jest opcja **Nie instaluj aktualizacji oprogramowania** służąca do sterowania zachowaniem pakietu wdrożeniowego podczas pobierania. To zachowanie jest zarządzane w Update Management przez utworzenie zaplanowanego wdrożenia aktualizacji w następnym kroku.

2. W Azure Automation wybierz pozycję **Update Management**. Utwórz nowe wdrożenie, postępując zgodnie z krokami opisanymi w temacie [Tworzenie wdrożenia aktualizacji](deploy-updates.md#schedule-an-update-deployment) i wybierz pozycję **zaimportowane grupy** na liście rozwijanej **Typ** , aby wybrać odpowiednią kolekcję Configuration Manager punktu końcowego firmy Microsoft. Należy pamiętać o następujących ważnych kwestiach:

    a. Jeśli okno obsługi jest zdefiniowane w wybranej kolekcji urządzeń Microsoft Endpoint Configuration Manager, elementy członkowskie kolekcji uznają je za ustawienie **czasu trwania** zdefiniowane w zaplanowanym wdrożeniu.

    b. Elementy członkowskie kolekcji docelowej muszą mieć połączenie z Internetem (bezpośrednio, za pośrednictwem serwera proxy lub z bramą Log Analytics).

Po zakończeniu wdrażania aktualizacji za pośrednictwem Azure Automation komputery docelowe, które są członkami wybranej grupy komputerów, będą instalować aktualizacje w zaplanowanym czasie z ich lokalnej pamięci podręcznej klienta. Możesz [wyświetlić stan wdrożenia aktualizacji](deploy-updates.md#check-deployment-status), aby monitorować wyniki swojego wdrożenia.

## <a name="manage-software-updates-from-azure-automation"></a>Zarządzaj aktualizacjami oprogramowania z programu Azure Automation

Aby zarządzać aktualizacjami maszyn wirtualnych z systemem Windows Server, które są klientami programu Microsoft Endpoint Configuration Manager, należy skonfigurować zasady klienta, aby wyłączyć funkcję zarządzanie aktualizacjami oprogramowania dla wszystkich klientów zarządzanych przez Update Management. Domyślnie ustawienia klienta dotyczą wszystkich urządzeń w hierarchii. Aby uzyskać więcej informacji na temat tego ustawienia zasad i sposobu jego konfiguracji, zobacz [jak skonfigurować ustawienia klienta w Configuration Manager](/configmgr/core/clients/deploy/configure-client-settings).

Po wykonaniu tej zmiany konfiguracji należy utworzyć nowe wdrożenie, postępując zgodnie z krokami opisanymi w temacie [Tworzenie wdrożenia aktualizacji](deploy-updates.md#schedule-an-update-deployment) i wybieraniem **grup zaimportowanych** na liście rozwijanej **Typ** , aby wybrać odpowiednią kolekcję Configuration Manager punktu końcowego firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować harmonogram integracji, zobacz [Planowanie wdrożenia aktualizacji](deploy-updates.md#schedule-an-update-deployment).
