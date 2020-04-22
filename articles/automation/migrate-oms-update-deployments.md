---
title: Migrowanie wdrożeń aktualizacji pakietu OMS na platformę Azure
description: W tym artykule opisano sposób migracji istniejących wdrożeń aktualizacji pakietu OMS na platformę Azure
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 910f284eedbf50be5b58b6c18f02e50adda35e9a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680006"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Migrowanie wdrożeń aktualizacji pakietu OMS na platformę Azure

Portal pakietu zarządzania operacjami (OMS) jest [przestarzały.](../azure-monitor/platform/oms-portal-transition.md) Wszystkie funkcje, które były dostępne w portalu pakietu OMS dla zarządzania aktualizacjami jest dostępna w witrynie Azure portal, za pośrednictwem dzienników usługi Azure Monitor. Ten artykuł zawiera informacje potrzebne do migracji do witryny Azure portal.

## <a name="key-information"></a>Najważniejsze informacje

* Istniejące wdrożenia będą nadal działać. Po ponownym utworzyć wdrożenia na platformie Azure, można usunąć stare wdrożenie z systemu OMS.
* Wszystkie istniejące funkcje w usłudze OMS są dostępne na platformie Azure, aby dowiedzieć się więcej o zarządzania aktualizacjami, zobacz [Omówienie zarządzania aktualizacjami](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Uzyskiwanie dostępu do witryny Azure Portal

W obszarze roboczym usługi OMS kliknij pozycję **Otwórz na platformie Azure**. To zaznaczenie przechodzi do obszaru roboczego usługi Log Analytics, który został używany przez system OMS.

![Otwórz na platformie Azure — portal OMS](media/migrate-oms-update-deployments/link-to-azure-portal.png)

W witrynie Azure portal kliknij pozycję **Konto automatyzacji**

![Dzienniki usługi Azure Monitor](media/migrate-oms-update-deployments/log-analytics.png)

Na koncie automatyzacji kliknij pozycję **Zarządzanie aktualizacjami**.

![Zarządzanie aktualizacjami](media/migrate-oms-update-deployments/azure-automation.png)

W witrynie Azure portal wybierz pozycję **Konta automatyzacji** w obszarze **Wszystkie usługi**. 

W obszarze **Narzędzia zarządzania**wybierz odpowiednie konto automatyzacji i kliknij pozycję **Aktualizuj zarządzanie**.

## <a name="recreate-existing-deployments"></a>Odtworzenie istniejących wdrożeń

Wszystkie wdrożenia aktualizacji utworzone w portalu pakietu OMS mają [zapisane wyszukiwanie](../azure-monitor/platform/computer-groups.md) znane również jako grupa komputerów o takiej samej nazwie jak istniejące wdrożenie aktualizacji. Zapisane wyszukiwanie zawiera listę maszyn, które zostały zaplanowane we wdrożeniu aktualizacji.

![Zarządzanie aktualizacjami](media/migrate-oms-update-deployments/oms-deployment.png)

Aby użyć tego istniejącego zapisanego wyszukiwania, wykonaj następujące czynności:

Aby utworzyć nowe wdrożenie aktualizacji, przejdź do witryny Azure Portal, wybierz używane konto automatyzacji i kliknij pozycję **Zarządzanie aktualizacjami**. Kliknij **pozycję Zaplanuj wdrożenie aktualizacji**.

![Planowanie wdrażania aktualizacji](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Zostanie otwarte okienko Wdrażanie nowej aktualizacji. Wprowadź wartości właściwości opisanych w poniższej tabeli, a następnie kliknij przycisk **Utwórz**:

W przypadku **aktualizacji maszyny**wybierz zapisane wyszukiwanie używane przez istniejące wdrożenie pakietu OMS.

| Właściwość | Opis |
| --- | --- |
|Nazwa |Unikatowa nazwa identyfikującą wdrożenie aktualizacji. |
|System operacyjny| Wybierz **Linux** lub **Windows**.|
|Maszyny do aktualizacji |Wybierz zapisane wyszukiwanie bądź zaimportowaną grupę lub wybierz maszynę z listy rozwijanej, a następnie wybierz poszczególne maszyny. Jeśli wybierzesz pozycję **Maszyny**, gotowość maszyny będzie wyświetlana w kolumnie **AKTUALIZUJ GOTOWOŚĆ AGENTA**.</br> Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach usługi Azure Monitor, zobacz [Computer groups in Azure Monitor logs (Grupy komputerów w dziennikach usługi Azure Monitor)](../azure-monitor/platform/computer-groups.md) |
|Klasyfikacje aktualizacji|Wybierz wszystkie potrzebne klasyfikacje aktualizacji. CentOS nie obsługuje tego po wyjęciu z pudełka.|
|Aktualizacje do wykluczenia|Wprowadź aktualizacje, aby wykluczyć. W systemie Windows wprowadź artykuł bazy wiedzy bez prefiksu **KB.** W przypadku systemu Linux wprowadź nazwę pakietu lub użyj symbolu wieloznacznego.  |
|Ustawienia harmonogramu|Wybierz godzinę rozpoczęcia, a następnie wybierz **raz** lub **cykliczne** dla cyklu. | 
| Okno konserwacji |Liczba minut ustawionych dla aktualizacji. Wartość nie może być mniejsza niż 30 minut lub więcej niż 6 godzin. |
| Kontrola ponownego uruchomienia| Określa, jak należy obsługiwać ponowne uruchomienie.</br>Dostępne opcje:</br>Ponowne uruchomienie, jeśli jest to wymagane (ustawienie domyślne)</br>Zawsze uruchamiaj ponownie</br>Nigdy nie uruchamiaj ponownie</br>Tylko ponowne uruchomienie — aktualizacje nie zostaną zainstalowane|

Kliknij **pozycję Zaplanowane wdrożenia aktualizacji,** aby wyświetlić stan nowo utworzonego wdrożenia aktualizacji.

![nowe wdrożenie aktualizacji](media/migrate-oms-update-deployments/new-update-deployment.png)

Jak wspomniano wcześniej, po skonfigurowaniu nowych wdrożeń za pośrednictwem witryny Azure portal istniejące wdrożenia można usunąć z portalu OMS.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zarządzania aktualizacjami na platformie Azure, zobacz [Zarządzanie aktualizacjami](automation-update-management.md).
