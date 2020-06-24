---
title: Dołączanie Azure Stack maszyn wirtualnych do Azure Security Center
description: Ten przewodnik Szybki Start przedstawia sposób aprowizacji rozszerzenia maszyny wirtualnej Azure Monitor, aktualizacji i zarządzania konfiguracją na maszynach wirtualnych Azure Stack.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: memildin
ms.openlocfilehash: 1f0a11616fe996f02d6464d23724156906176041
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771159"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>Szybki Start: dołączanie Azure Stack maszyn wirtualnych do Security Center
Po dołączeniu subskrypcji platformy Azure można włączyć Security Center, aby chronić maszyny wirtualne działające w Azure Stack przez dodanie rozszerzenia maszyny wirtualnej **Azure monitor, aktualizacji i zarządzania konfiguracją** z witryny Azure Stack Marketplace.

W tym przewodniku szybki start przedstawiono sposób dodawania rozszerzenia maszyny wirtualnej **Azure monitor, aktualizacji i zarządzania konfiguracją** na maszynie wirtualnej (systemy Linux i Windows są obsługiwane) w systemie Azure Stack.

## <a name="prerequisites"></a>Wymagania wstępne
Do rozpoczęcia korzystania z usługi Security Center wymagana jest subskrypcja usługi Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).

Przed rozpoczęciem tego przewodnika Szybki Start musisz mieć subskrypcję platformy Azure w warstwie Standardowa Security Center. Zobacz [Dołączanie subskrypcji platformy Azure do usługi Security Center w warstwie Standardowa](security-center-get-started.md), aby uzyskać instrukcje dotyczące uaktualnienia. Możesz wypróbować Security Center warstwę Standardowa bezpłatnie przez 30 dni. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Wybierz obszar roboczy w Azure Security Center

1. Zaloguj się w witrynie [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. W menu **Microsoft Azure** wybierz pozycję **Security Center**. Zostanie otwarte okno **Security Center — Przegląd**. 

   ![Security Center — Przegląd][2]

3. W menu głównym usługi Security Center wybierz pozycję **Wprowadzenie**.
4. Wybierz kartę **Rozpoczęcie pracy**.

   ![Rozpoczęcie pracy][3]

5. Kliknij przycisk **Konfiguruj** w obszarze **Dodaj nowe komputery spoza platformy Azure**. Zostanie wyświetlona lista obszarów roboczych usługi Log Analytics. Jeśli ma to zastosowanie, lista zawiera domyślny obszar roboczy utworzony przez usługę Security Center po włączeniu automatycznej aprowizacji. Wybierz ten obszar roboczy lub inny obszar roboczy, do którego maszyna wirtualna Azure Stack ma raportować dane zabezpieczeń.

    ![Dodawanie komputera spoza platformy Azure](./media/quick-onboard-windows-computer/non-azure.png)

   Zostanie otwarty blok **agenta bezpośredniego** z linkiem umożliwiającym pobranie agenta i kluczy dla identyfikatora obszaru roboczego do użycia podczas konfigurowania agenta.

   >[!NOTE]
   > NIE musisz pobierać agenta ręcznie. Agent zostanie zainstalowany jako rozszerzenie maszyny wirtualnej w poniższych krokach.

6. Po prawej stronie **identyfikatora obszaru roboczego** wybierz ikonę kopiowania i wklej identyfikator do Notatnika.

7. Po prawej stronie **klucza podstawowego** wybierz ikonę kopiowania i wklej klucz do Notatnika.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Dodaj rozszerzenie maszyny wirtualnej do istniejących Azure Stack maszyn wirtualnych
Należy teraz dodać rozszerzenie maszyny wirtualnej **Azure monitor, aktualizacji i zarządzania konfiguracją** do maszyn wirtualnych działających na Azure Stack.

1. Na nowej karcie przeglądarki Zaloguj się do portalu **Azure Stack** .
2. Przejdź do strony **maszyny wirtualne** wybierz maszynę wirtualną, która ma być chroniona za pomocą Security Center. Aby uzyskać informacje na temat sposobu tworzenia maszyny wirtualnej na Azure Stack, zobacz [ten przewodnik Szybki Start dla maszyn wirtualnych z systemem Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) lub [w tym przewodniku szybki start dla maszyn wirtualnych systemu Linux](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Wybierz pozycję **Rozszerzenia**. Zostanie wyświetlona lista rozszerzeń maszyn wirtualnych zainstalowanych na tej maszynie wirtualnej.
4. Kliknij kartę **Dodaj** . Zostanie otwarty blok menu **nowy zasób** i zostanie wyświetlona lista dostępnych rozszerzeń maszyn wirtualnych. 
5. Wybierz rozszerzenie **Azure monitor, aktualizacji i zarządzania konfiguracją,** a następnie kliknij przycisk **Utwórz**. Zostanie otwarty blok konfiguracja **rozszerzenia instalacji** .

>[!NOTE]
> Jeśli nie widzisz rozszerzenia **Azure monitor, aktualizacji i zarządzania konfiguracją** na liście w portalu Marketplace, skontaktuj się z operatorem Azure Stack, aby go udostępnić.

6. W bloku konfiguracja **rozszerzenia instalacji** wklej **Identyfikator obszaru roboczego** i **klucz obszaru roboczego (klucz podstawowy)** skopiowane do Notatnika w poprzedniej procedurze.
7. Po wprowadzeniu niezbędnych ustawień konfiguracji kliknij przycisk **OK**.
8. Po zakończeniu instalacji rozszerzenia jego stan zostanie wyświetlony po **pomyślnym zainicjowaniu obsługi administracyjnej**. Pojawienie się maszyny wirtualnej w portalu Security Center może potrwać do godziny.

Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta dla systemu Windows, zobacz [łączenie komputerów z systemem Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Rozwiązywanie problemów z agentami w systemie Linux można znaleźć w temacie [Rozwiązywanie problemów z usługą Azure log Analytics Linux Agent](../azure-monitor/platform/agent-linux-troubleshoot.md).

Teraz możesz monitorować maszyny wirtualne platformy Azure oraz komputery spoza platformy Azure w jednym miejscu. W portalu Security Center na platformie Azure w obszarze **Obliczanie**znajduje się przegląd wszystkich maszyn wirtualnych i komputerów wraz z ich zaleceniami. Security Center również wykrycie dla tych komputerów w alertach zabezpieczeń.

  ![Blok Obliczanie][6]

Istnieją dwa typy ikon przedstawianych w bloku **Obliczanie**:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Komputer spoza platformy Azure 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Maszyna wirtualna platformy Azure (Azure Stack maszyny wirtualne będą wyświetlane w tej grupie)

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Gdy nie jest już potrzebne, można usunąć rozszerzenie z maszyny wirtualnej za pośrednictwem portalu Azure Stack.

Aby usunąć rozszerzenie:

1. Otwórz **Portal Azure Stack**.
2. Przejdź do strony **maszyny wirtualne** wybierz maszynę wirtualną, z której chcesz usunąć rozszerzenie.
3. Wybierz pozycję **rozszerzenia**, wybierz rozszerzenie **Microsoft. EnterpriseCloud. Monitoring**.
4. Kliknij przycisk **Odinstaluj**i potwierdź wybór, klikając **przycisk tak**.

## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start zainicjowano Azure Monitor, aktualizację i rozszerzenie zarządzania konfiguracją na maszynie wirtualnej uruchomionej w Azure Stack. Aby dowiedzieć się więcej na temat sposobu korzystania z usługi Security Center, przejdź do samouczka konfigurowania zasad zabezpieczeń i oceniania zabezpieczeń Twoich zasobów.

> [!div class="nextstepaction"]
> [Samouczek: Definiowanie i ocenianie zasad zabezpieczeń](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
