---
title: Połącz maszyny spoza platformy Azure z usługą Azure Security Center
description: Dowiedz się, jak połączyć maszyny spoza platformy Azure w celu Security Center
author: memildin
ms.author: memildin
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
zone_pivot_groups: non-azure-machines
ms.openlocfilehash: 68fcf8a8feb046fca2c26041d92264dd8b3a638e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103465501"
---
# <a name="connect-your-non-azure-machines-to-security-center"></a>Połącz maszyny spoza platformy Azure z usługą Security Center

Security Center może monitorować stan zabezpieczeń na komputerach spoza platformy Azure, ale najpierw należy połączyć je z platformą Azure.

Komputery spoza platformy Azure można połączyć w dowolny z następujących sposobów:

- Korzystanie z serwerów z obsługą usługi Azure ARC (**zalecane**)
- Ze stron Security Center w Azure Portal (**wprowadzenie** i **spis**)

Każdy z tych elementów został opisany na tej stronie.

::: zone pivot="azure-arc"

## <a name="add-non-azure-machines-with-azure-arc"></a>Dodawanie maszyn spoza platformy Azure przy użyciu usługi Azure Arc

Serwery z obsługą usługi Azure Arc są preferowanym sposobem dodawania maszyn nienależących do platformy Azure do Azure Security Center.

Komputer z serwerami z obsługą usługi Azure Arc jest zasobem platformy Azure i pojawia się w Security Center z zaleceniami, takimi jak inne zasoby platformy Azure.

Ponadto serwery z obsługą usługi Azure Arc zapewniają rozszerzone możliwości, takie jak opcja włączania zasad konfiguracji gościa na maszynie, wdrożenie agenta Log Analytics jako rozszerzenia, uproszczenie wdrażania z innymi usługami platformy Azure i inne. Aby zapoznać się z omówieniem korzyści, zobacz [obsługiwane scenariusze](../azure-arc/servers/overview.md#supported-scenarios).

Dowiedz się więcej o [serwerach z obsługą usługi Azure Arc](../azure-arc/servers/overview.md).

**Aby wdrożyć usługę Azure ARC:**

- W przypadku jednej maszyny postępuj zgodnie z instrukcjami w [przewodniku szybki start: łączenie maszyn hybrydowych z serwerami z obsługą usługi Azure Arc](../azure-arc/servers/learn/quick-enable-hybrid-vm.md).
- Aby połączyć wiele maszyn w odpowiedniej skali z serwerami z włączoną funkcją ARC, zobacz [łączenie maszyn hybrydowych z platformą Azure na dużą skalę](../azure-arc/servers/onboard-service-principal.md)

> [!TIP]
> W przypadku dołączania maszyn, na których działa usługa AWS, łącznik Security Center dla AWS w niewidoczny sposób obsługuje wdrożenie usługi Azure Arc. Dowiedz się więcej w temacie [Łączenie kont AWS z Azure Security Center](quickstart-onboard-aws.md).

::: zone-end

::: zone pivot="azure-portal"

## <a name="add-non-azure-machines-from-the-azure-portal"></a>Dodaj maszyny spoza platformy Azure z Azure Portal

1. W menu Security Center Otwórz stronę **wprowadzenie** .
1. Wybierz kartę **Rozpoczęcie pracy**.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Karta wprowadzenie na stronie pierwsze kroki" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. Poniżej **Dodaj serwery spoza platformy Azure**, wybierz pozycję **Konfiguruj** .

    > [!TIP]
    > Możesz również otworzyć przycisk Dodaj maszyny ze strony **spisu** **Dodaj serwery spoza platformy Azure** .
    > 
    > :::image type="content" source="./media/security-center-onboarding/onboard-inventory.png" alt-text="Dodawanie maszyn spoza platformy Azure ze strony spisu zasobów":::

    Zostanie wyświetlona lista obszarów roboczych usługi Log Analytics. Jeśli ma to zastosowanie, lista zawiera domyślny obszar roboczy utworzony przez usługę Security Center po włączeniu automatycznej aprowizacji. Wybierz ten obszar roboczy lub inny obszar roboczy, którego chcesz użyć.

    Możesz dodać komputery do istniejącego obszaru roboczego lub utworzyć nowy obszar roboczy.

1. Opcjonalnie, aby utworzyć nowy obszar roboczy, wybierz opcję  **Utwórz nowy obszar roboczy**.

1. Z listy obszarów roboczych wybierz pozycję **Dodaj serwery** dla odpowiedniego obszaru roboczego.
    Zostanie wyświetlona strona **Zarządzanie agentami** .

    W tym miejscu wybierz odpowiednią procedurę poniżej w zależności od typu maszyn, które są dołączane:

    - [Dołączanie maszyn wirtualnych centrów Azure Stack](#onboard-your-azure-stack-hub-vms)
    - [Dołączanie maszyn z systemem Linux](#onboard-your-linux-machines)
    - [Dołączanie maszyn z systemem Windows](#onboard-your-windows-machines)

### <a name="onboard-your-azure-stack-hub-vms"></a>Dołączanie maszyn wirtualnych centrów Azure Stack

Aby dodać maszyny wirtualne Azure Stack Hub, należy uzyskać informacje na stronie **Zarządzanie agentami** oraz skonfigurować rozszerzenie maszyny wirtualnej do **zarządzania Azure monitor, aktualizacji i konfiguracji** na maszynach wirtualnych działających w wystąpieniu centrum Azure Stack.

1. Na stronie **Zarządzanie agentami** Skopiuj **Identyfikator obszaru roboczego** i **klucz podstawowy** do Notatnika.
1. Zaloguj się do portalu **Azure Stack Hub** i Otwórz stronę **maszyny wirtualne** .
1. Wybierz maszynę wirtualną, która ma być chroniona za pomocą Security Center.
    >[!TIP]
    > Aby uzyskać informacje na temat sposobu tworzenia maszyny wirtualnej w centrum Azure Stack, zobacz [ten przewodnik Szybki Start dla maszyn wirtualnych z systemem Windows](/azure-stack/user/azure-stack-quick-windows-portal) lub [tego przewodnika Szybki Start dla maszyn wirtualnych](/azure-stack/user/azure-stack-quick-linux-portal)z systemem Linux.
1. Wybierz pozycję **Rozszerzenia**. Zostanie wyświetlona lista rozszerzeń maszyn wirtualnych zainstalowanych na tej maszynie wirtualnej.
1. Wybierz kartę **Dodaj** . W menu **nowy zasób** zostanie wyświetlona lista dostępnych rozszerzeń maszyn wirtualnych.
1. Wybierz rozszerzenie **Azure monitor, aktualizacji i zarządzania konfiguracją,** a następnie wybierz pozycję **Utwórz**. Zostanie otwarta strona Konfiguracja **rozszerzenia instalacji** .
    >[!NOTE]
    > Jeśli nie widzisz rozszerzenia **Azure monitor, aktualizacji i zarządzania konfiguracją** w portalu Marketplace, skontaktuj się z operatorem Azure Stack Hub, aby go udostępnić.
1. Na stronie Konfiguracja **rozszerzenia instalacji** wklej **Identyfikator obszaru roboczego** i **klucz obszaru roboczego (klucz podstawowy)** skopiowane do Notatnika w poprzednim kroku.
1. Po zakończeniu konfiguracji wybierz **przycisk OK**. Stan rozszerzenia zostanie wyświetlony po **pomyślnym zainicjowaniu obsługi administracyjnej**. Aby maszyna wirtualna była wyświetlana w Security Center, może upłynąć do godziny.

### <a name="onboard-your-linux-machines"></a>Dołączanie maszyn z systemem Linux

Aby dodać maszyny z systemem Linux, musisz wykonać polecenie WGET na stronie **zarządzania agentami** .

1. Na stronie **Zarządzanie agentami** Skopiuj polecenie **Wget** do Notatnika. Zapisz ten plik do lokalizacji dostępnej z komputera z systemem Linux.
1. Na komputerze z systemem Linux Otwórz plik za pomocą polecenia WGET. Zaznacz całą zawartość i skopiuj ją i wklej do konsoli terminala.
1. Po zakończeniu instalacji możesz sprawdzić, czy *omsagent* jest zainstalowany, uruchamiając polecenie *pgrep* . Polecenie zwróci identyfikator PID *omsagent* .
    Dzienniki dla agenta można znaleźć pod adresem: */var/opt/Microsoft/omsagent/ \<workspace id> /log/* nowy Security Center komputer z systemem Linux może potrwać do 30 minut.

### <a name="onboard-your-windows-machines"></a>Dołączanie maszyn z systemem Windows

Aby dodać maszyny z systemem Windows, potrzebne są informacje na stronie **Zarządzanie** agentami oraz pobranie odpowiedniego pliku agenta (32/64-bitowy).
1. Wybierz link **Pobierz agenta systemu Windows** odpowiadający typowi procesora komputera, aby pobrać plik instalacji.
1. Na stronie **Zarządzanie agentami** Skopiuj **Identyfikator obszaru roboczego** i **klucz podstawowy** do Notatnika.
1. Skopiuj pobrany plik instalacyjny na komputer docelowy i uruchom go.
1. Postępuj zgodnie z instrukcjami Kreatora instalacji (**dalej** **zgadzam** **się, dalej,** **dalej**).
    1. Na stronie **log Analytics platformy Azure** wklej **Identyfikator obszaru roboczego** i **klucz obszaru roboczego (klucz podstawowy)** skopiowane do Notatnika.
    1. Jeśli komputer powinien raportować do obszaru roboczego Log Analytics w Azure Government chmurze, wybierz pozycję **Azure USA dla instytucji rządowych** z listy rozwijanej **Azure Cloud** .
    1. Jeśli komputer musi komunikować się z usługą Log Analytics za pośrednictwem serwera proxy, wybierz pozycję **Zaawansowane** i podaj adres URL i numer portu serwera proxy.
    1. Po wprowadzeniu wszystkich ustawień konfiguracji wybierz pozycję **dalej**.
    1. Na stronie **gotowy do instalacji** przejrzyj ustawienia, które mają zostać zastosowane, a następnie wybierz pozycję **Zainstaluj**.
    1. Na stronie **Konfiguracja została ukończona pomyślnie** wybierz pozycję **Zakończ**.

Po zakończeniu tego procesu **program Microsoft Monitoring Agent** zostanie wyświetlony w **Panelu sterowania**. Możesz tam przejrzeć konfigurację i sprawdzić, czy agent został połączony.

Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta, zobacz [łączenie komputerów z systemem Windows](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard).

::: zone-end

## <a name="verifying"></a>Weryfikacj

Gratulacje! Teraz możesz zobaczyć maszyny platformy Azure i inne niż platformy Azure razem w jednym miejscu. Otwórz [stronę spisu](asset-inventory.md) zasobów i odfiltruj do odpowiednich typów zasobów. Te ikony odróżniają typy:

  ![Ikona ASC dla maszyny spoza platformy Azure](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Maszyna spoza platformy Azure

  ![Ikona ASC dla maszyny platformy Azure](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Maszyna wirtualna platformy Azure

  ![Ikona ASC dla serwera usługi Azure Arc](./media/quick-onboard-linux-computer/arc-enabled-machine-icon.png) Serwer z włączonym usługą Azure Arc

## <a name="next-steps"></a>Następne kroki

Na tej stronie pokazano, jak dodać maszyny spoza platformy Azure do Azure Security Center. Aby monitorować ich stan, użyj narzędzi spisu, jak wyjaśniono na poniższej stronie:

- [Eksplorowanie zasobów i zarządzanie nimi za pomocą spisu zasobów](asset-inventory.md)