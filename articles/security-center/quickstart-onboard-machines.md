---
title: Połącz maszyny spoza platformy Azure z usługą Azure Security Center
description: Dowiedz się, jak połączyć maszyny spoza platformy Azure w celu Security Center
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 82b8161f92f337002e9d8bbdc45cd53d5921fc00
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280678"
---
#  <a name="connect-your-non-azure-machines-to-security-center"></a>Połącz maszyny spoza platformy Azure z usługą Security Center

Usługa Security Center może monitorować stan bezpieczeństwa komputerów nienależących do platformy Azure, ale musisz najpierw dołączyć te zasoby. Komputery spoza platformy Azure można dodać na stronie **pierwsze kroki** lub z **spisu** , jak opisano poniżej.

## <a name="add-non-azure-computers"></a>Dodaj komputery spoza platformy Azure 

1. W menu Security Center Otwórz stronę **wprowadzenie** .
1. Wybierz kartę **Rozpoczęcie pracy**.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Karta wprowadzenie na stronie pierwsze kroki" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. Poniżej **Dodaj serwery spoza platformy Azure**, wybierz pozycję **Konfiguruj** .

    > [!TIP]
    > Możesz również otworzyć przycisk Dodaj maszyny ze strony **spisu** **Dodaj serwery spoza platformy Azure** .

    Zostanie wyświetlona lista obszarów roboczych usługi Log Analytics. Jeśli ma to zastosowanie, lista zawiera domyślny obszar roboczy utworzony przez usługę Security Center po włączeniu automatycznej aprowizacji. Wybierz ten obszar roboczy lub inny obszar roboczy, którego chcesz użyć.

    Możesz dodać komputery do istniejącego obszaru roboczego lub utworzyć nowy obszar roboczy. 

1. Opcjonalnie, aby utworzyć nowy obszar roboczy, wybierz opcję  **Utwórz nowy obszar roboczy**.

1. Z listy obszarów roboczych wybierz pozycję **Dodaj serwery** dla odpowiedniego obszaru roboczego.
    Zostanie wyświetlona strona **Zarządzanie agentami** .

    W tym miejscu wybierz odpowiednią procedurę poniżej w zależności od typu maszyn, które są dołączane:

    - [Dołączanie maszyn wirtualnych Azure Stack](#onboard-your-azure-stack-vms)
    - [Dołączanie maszyn z systemem Linux](#onboard-your-linux-machines)
    - [Dołączanie maszyn z systemem Windows](#onboard-your-windows-machines)


### <a name="onboard-your-azure-stack-vms"></a>Dołączanie maszyn wirtualnych Azure Stack
Aby dodać maszyny wirtualne Azure Stack, należy uzyskać informacje na stronie **Zarządzanie agentami** oraz skonfigurować rozszerzenie maszyny wirtualnej do **zarządzania Azure monitor, aktualizacji i konfiguracji** na maszynach wirtualnych uruchomionych w Azure Stack.
1. Na stronie **Zarządzanie agentami** Skopiuj **Identyfikator obszaru roboczego** i **klucz podstawowy** do Notatnika.
1. Zaloguj się do portalu **Azure Stack** i Otwórz stronę **maszyny wirtualne** .
1. Wybierz maszynę wirtualną, która ma być chroniona za pomocą Security Center.
    >[!TIP]
    > Aby uzyskać informacje na temat sposobu tworzenia maszyny wirtualnej na Azure Stack, zobacz [ten przewodnik Szybki Start dla maszyn wirtualnych z systemem Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) lub [w tym przewodniku szybki start dla maszyn wirtualnych systemu Linux](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
1. Wybierz pozycję **Rozszerzenia**. Zostanie wyświetlona lista rozszerzeń maszyn wirtualnych zainstalowanych na tej maszynie wirtualnej.
1. Wybierz kartę **Dodaj** . W menu **nowy zasób** zostanie wyświetlona lista dostępnych rozszerzeń maszyn wirtualnych.
1. Wybierz rozszerzenie **Azure monitor, aktualizacji i zarządzania konfiguracją,** a następnie wybierz pozycję **Utwórz**. Zostanie otwarta strona Konfiguracja **rozszerzenia instalacji** .
    >[!NOTE]
    > Jeśli nie widzisz rozszerzenia **Azure monitor, aktualizacji i zarządzania konfiguracją** na liście w portalu Marketplace, skontaktuj się z operatorem Azure Stack, aby go udostępnić.
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

Po zakończeniu **log Analytics Agent** zostanie wyświetlony w **Panelu sterowania**. Możesz tam przejrzeć konfigurację i sprawdzić, czy agent został połączony.

Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta, zobacz [łączenie komputerów z systemem Windows](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).


## <a name="verifying"></a>Weryfikacj
Gratulacje! Teraz możesz zobaczyć maszyny platformy Azure i inne niż platformy Azure razem w jednym miejscu. Otwórz [stronę spisu](asset-inventory.md) zasobów i odfiltruj do odpowiednich typów zasobów. Te dwie ikony odróżniają typy:

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Maszyna spoza platformy Azure

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Maszyna wirtualna platformy Azure


## <a name="next-steps"></a>Następne kroki

Na tej stronie pokazano, jak dodać maszyny spoza platformy Azure do Azure Security Center. Aby monitorować ich stan, użyj narzędzi spisu, jak wyjaśniono na poniższej stronie:

- [Eksplorowanie zasobów i zarządzanie nimi za pomocą spisu zasobów i narzędzi do zarządzania](asset-inventory.md)