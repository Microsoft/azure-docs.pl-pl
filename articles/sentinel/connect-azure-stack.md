---
title: Dołączanie maszyn wirtualnych Azure Stack Hub do centrów danych platformy Azure | Microsoft Docs
description: W tym artykule opisano sposób aprowizacji rozszerzenia maszyny wirtualnej Azure Monitor, aktualizacji i zarządzania konfiguracją na maszynach wirtualnych Azure Stack Hub oraz monitorowania ich przy użyciu centrów na platformie Azure.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5999e8da5dffce85dd12ecd01cd5991ea4abc098
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590246"
---
# <a name="connect-azure-stack-hub-virtual-machines-to-azure-sentinel"></a>Łączenie maszyn wirtualnych Azure Stack Hub z platformą Azure — wskaźnikiem

Za pomocą centrów danych platformy Azure możesz monitorować maszyny wirtualne działające na platformie Azure i w jednym miejscu Azure Stack centrum. Aby dołączyć maszyny Azure Stack do centrów danych platformy Azure, musisz najpierw dodać rozszerzenie maszyny wirtualnej do istniejących maszyn wirtualnych Azure Stack Hub. 

Po nawiązaniu połączenia z maszynami Azure Stack Hub wybierz z galerii pulpitów nawigacyjnych informacje na podstawie danych. Te pulpity nawigacyjne mogą być łatwo dostosowane do Twoich potrzeb.

## <a name="add-the-virtual-machine-extension"></a>Dodaj rozszerzenie maszyny wirtualnej 

Dodaj rozszerzenie maszyny wirtualnej **Azure monitor, aktualizacji i zarządzania konfiguracją** do maszyn wirtualnych działających w centrum Azure Stack. 

1. Na nowej karcie przeglądarki Zaloguj się do [portalu Azure Stack Hub](/azure-stack/user/azure-stack-use-portal#access-the-portal).

1. Przejdź do strony **maszyny wirtualne** , wybierz maszynę wirtualną, która ma być chroniona za pomocą platformy Azure. Aby uzyskać informacje na temat sposobu tworzenia maszyny wirtualnej w centrum Azure Stack, zobacz [Tworzenie maszyny wirtualnej z systemem Windows Server za pomocą centrum Azure Stack Hub](/azure-stack/user/azure-stack-quick-windows-portal) lub [Tworzenie maszyny wirtualnej serwera z systemem Linux przy użyciu portalu Centrum Azure Stack](/azure-stack/user/azure-stack-quick-linux-portal).

1. Wybierz pozycję **Rozszerzenia**. Zostanie wyświetlona lista rozszerzeń maszyn wirtualnych zainstalowanych na tej maszynie wirtualnej.

1. Kliknij kartę **Dodaj** . Zostanie otwarty blok menu **nowy zasób** i zostanie wyświetlona lista dostępnych rozszerzeń maszyn wirtualnych. 

1. Wybierz rozszerzenie **Azure monitor, aktualizacji i zarządzania konfiguracją** , a następnie kliknij przycisk **Utwórz**. Zostanie otwarte okno Konfiguracja **rozszerzenia instalacji** .

   ![Ustawienia Azure Monitor, aktualizacji i zarządzania konfiguracją](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Jeśli nie widzisz rozszerzenia **Azure monitor, aktualizacji i zarządzania konfiguracją** w portalu Marketplace, skontaktuj się z operatorem Azure Stack Hub, aby go udostępnić.

1. W menu wskaźnikowym platformy Azure wybierz opcję **Ustawienia obszaru roboczego** , a następnie **Zaawansowane** i skopiuj **Identyfikator obszaru** roboczego i **klucz obszaru roboczego (klucz podstawowy)**. 

1. W oknie Azure Stack **rozszerzenia instalacji** centrum wklej je w wskazanych polach i kliknij przycisk **OK**.

1. Po zakończeniu instalacji rozszerzenia jego stan wskazuje, że **Inicjowanie obsługi powiodło się**. Aby maszyna wirtualna mogła zostać wyświetlona w portalu wskaźnikowym platformy Azure, może upłynąć do godziny.

Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta dla systemu Windows, zobacz [łączenie komputerów z systemem Windows](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard).

Rozwiązywanie problemów z agentami w systemie Linux można znaleźć w temacie [Rozwiązywanie problemów z usługą Azure log Analytics Linux Agent](../azure-monitor/agents/agent-linux-troubleshoot.md).

W portalu wskaźnikowym platformy Azure na platformie Azure w obszarze **Virtual Machines** masz przegląd wszystkich maszyn wirtualnych i komputerów wraz z ich stanem. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, można usunąć rozszerzenie z maszyny wirtualnej za pośrednictwem portalu Azure Stack Hub.

Aby usunąć rozszerzenie:

1. Otwórz **Portal Azure Stack Hub**.

1. Przejdź do strony **maszyny wirtualne** wybierz maszynę wirtualną, z której chcesz usunąć rozszerzenie.

1. Wybierz pozycję **rozszerzenia**, wybierz rozszerzenie **Microsoft. EnterpriseCloud. Monitoring**.

1. Kliknij przycisk **Odinstaluj** i potwierdź wybór.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- Przesyłaj strumieniowo dane ze [wspólnych urządzeń w formacie zdarzenia](connect-common-event-format.md) do platformy Azure.
