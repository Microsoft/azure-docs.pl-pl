---
title: Łączenie danych dziennika systemowego z platformą Azure Microsoft Docs
description: Połącz wszystkie maszyny lub urządzenia, które obsługują dziennik systemowy na platformie Azure, przy użyciu agenta na komputerze z systemem Linux między urządzeniem a platformą Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: d35a97b0008a7ce3069185dd557a60221776b0ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595461"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Zbieranie danych z źródeł opartych na systemie Linux przy użyciu dziennika systemowego

Zdarzenia z maszyn lub urządzeń obsługujących system Linux można przesyłać strumieniowo do usługi Azure wskaźnikowej przy użyciu agenta Log Analytics dla systemu Linux (dawniej nazywany agentem pakietu OMS). Można to zrobić na dowolnym komputerze, na którym można zainstalować agenta Log Analytics bezpośrednio na komputerze. Natywny demon dziennika systemu maszyny zbiera zdarzenia lokalne z określonych typów i przekazuje je lokalnie do agenta, co spowoduje przesłanie strumieniowo do obszaru roboczego Log Analytics.

> [!NOTE]
> - Jeśli urządzenie obsługuje program **Common Event format (CEF) za pośrednictwem dziennika** systemowego, zbierany jest bardziej kompletny zestaw danych, a dane są analizowane w kolekcji. Należy wybrać tę opcję i postępować zgodnie z instrukcjami podanymi w temacie [łączenie rozwiązania zewnętrznego przy użyciu CEF](connect-common-event-format.md).
>
> - Log Analytics obsługuje zbieranie komunikatów wysyłanych przez demony **rsyslog** lub **dziennika** systemowego, gdzie rsyslog jest wartością domyślną. Domyślny demon dziennika systemu w wersji 5 Red Hat Enterprise Linux (RHEL), CentOS i wersja Oracle Linux (**sysklog**) nie są obsługiwane w przypadku zbierania zdarzeń dziennika systemu. Aby zebrać dane dziennika systemu z tej wersji dystrybucji, demona rsyslog powinna zostać zainstalowana i skonfigurowana do zastępowania sysklog.

## <a name="how-it-works"></a>Jak to działa

**Dziennik** systemowy to protokół rejestrowania zdarzeń, który jest wspólny dla systemu Linux. Po zainstalowaniu **agenta log Analytics dla systemu Linux** na maszynie wirtualnej lub urządzeniu, procedura instalacji konfiguruje lokalny demon dziennika systemowego, aby przekazywać komunikaty do agenta na porcie TCP 25224. Następnie Agent wysyła komunikat do obszaru roboczego Log Analytics za pośrednictwem protokołu HTTPS, który jest analizowany w pozycji dziennika zdarzeń w tabeli dziennika systemowego w **dziennikach usługi Azure wskaźnik >**.

Aby uzyskać więcej informacji, zobacz [źródła danych dziennika systemowego w Azure monitor](../azure-monitor/agents/data-sources-syslog.md).

## <a name="configure-syslog-collection"></a>Konfigurowanie kolekcji dziennika systemowego

### <a name="configure-your-linux-machine-or-appliance"></a>Skonfiguruj maszynę lub urządzenie z systemem Linux

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie wybierz pozycję Łącznik **dziennika** systemowego.

1. W bloku **Dziennik** systemowy wybierz pozycję **Otwórz stronę łącznika**.

1. Zainstaluj agenta systemu Linux. W obszarze **Wybierz lokalizację instalacji agenta:**
    
    **W przypadku maszyny wirtualnej z systemem Linux na platformie Azure:**
      
    1. Wybierz pozycję **Zainstaluj agenta na maszynie wirtualnej platformy Azure z systemem Linux**.
    
    1. Kliknij link **pobierz & Zainstaluj agenta dla maszyn wirtualnych platformy Azure z systemem Linux >** . 
    
    1. W bloku **maszyny wirtualne** kliknij maszynę wirtualną, w której ma zostać zainstalowany agent, a następnie kliknij przycisk **Połącz**. Powtórz ten krok dla każdej maszyny wirtualnej, którą chcesz połączyć.
    
    **Dla każdej innej maszyny z systemem Linux:**

    1. Wybierz pozycję **Zainstaluj agenta na maszynie spoza systemu Azure** z systemem Linux

    1. Kliknij link **pobierz & zainstalować agenta dla komputerów spoza systemu Linux >** . 

    1. W bloku **Zarządzanie agentami** kliknij kartę **serwery z systemem Linux** , a następnie skopiuj polecenie **Pobierz i Dołącz agenta dla systemu Linux** i uruchom go na komputerze z systemem Linux. 
    
   > [!NOTE]
   > Upewnij się, że skonfigurowano ustawienia zabezpieczeń dla tych komputerów zgodnie z zasadami zabezpieczeń organizacji. Można na przykład skonfigurować ustawienia sieci, aby dostosować je do zasad zabezpieczeń sieci organizacji, i zmienić porty i protokoły w demona, aby dostosować je do wymagań dotyczących zabezpieczeń.

### <a name="configure-the-log-analytics-agent"></a>Konfigurowanie agenta Log Analytics

1. W dolnej części bloku łącznika dziennika systemu kliknij link **otwórz >konfigurację agentów obszaru roboczego** .

1. W bloku **Konfiguracja agentów** wybierz kartę **Dziennik** systemowy. Następnie Dodaj obiekty do zebrania dla łącznika. Wybierz pozycję **Dodaj** funkcję i wybierz pozycję z listy rozwijanej usługi.
    
    - Dodaj obiekty, które urządzenie dziennika systemu zawiera w jego nagłówkach dziennika. 
    
    - Jeśli chcesz użyć nietypowego wykrywania logowania SSH za pomocą zbieranych danych, Dodaj **uwierzytelnianie** i **authpriv**. Aby uzyskać dodatkowe informacje, zobacz [następującą sekcję](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) .

1. Po dodaniu wszystkich obiektów, które mają być monitorowane, sprawdź, czy pola wyboru dla wszystkich żądanych serwerów są oznaczone.

1. Wybierz przycisk **Zastosuj**. 

1. Na maszynie wirtualnej lub urządzeniu upewnij się, że są wysyłane określone obiekty.

1. Aby wykonać zapytanie dotyczące danych dzienników dziennika systemu w **dziennikach**, wpisz `Syslog` w oknie zapytania.

1. Aby przeanalizować komunikaty dziennika systemowego, można użyć parametrów zapytania opisanych w temacie [using Functions in Azure monitor Query log](../azure-monitor/logs/functions.md) . Następnie można zapisać zapytanie jako nową funkcję Log Analytics i użyć go jako nowego typu danych.

> [!NOTE]
> **Używanie tego samego komputera do przesyłania zarówno zwykłego dziennika systemowego *, jak i* komunikatów CEF**
>
> Możesz użyć istniejącego [komputera usługi przesyłania dalej dzienników CEF](connect-cef-agent.md) , aby zbierać i przesyłać dzienniki z dzienników zwykłego dziennika systemu. Należy jednak wykonać następujące czynności, aby uniknąć wysyłania zdarzeń w obu formatach do usługi Azure wskaźnikowej, ponieważ spowoduje to duplikowanie zdarzeń.
>
>    Już skonfigurowano [zbieranie danych ze źródeł CEF](connect-common-event-format.md)i skonfigurowano agenta log Analytics w taki sam sposób:
>
> 1. Na każdym komputerze, który wysyła dzienniki w formacie CEF, należy edytować plik konfiguracji dziennika systemowego w celu usunięcia obiektów używanych do wysyłania komunikatów CEF. W ten sposób obiekty, które są wysyłane w CEF, nie będą również wysyłane w dzienniku systemu. Szczegółowe instrukcje można znaleźć w temacie [Konfigurowanie dziennika systemowego w agencie systemu Linux](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent) .
>
> 1. Na tych maszynach należy uruchomić następujące polecenie, aby wyłączyć synchronizację agenta z konfiguracją dziennika systemowego na platformie Azure. Dzięki temu zmiana konfiguracji wprowadzona w poprzednim kroku nie zostanie zastępować.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Konfigurowanie łącznika dziennika systemowego na potrzeby wykrywania nietypowego logowania SSH

> [!IMPORTANT]
> Nietypowe wykrywanie logowania SSH jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wskaźnik "Azure" może stosować Uczenie maszynowe (ML) do danych dziennika systemowego, aby zidentyfikować nietypowe działanie logowania Secure Shell (SSH). Scenariusze obejmują:

- Niemożliwa podróż — w przypadku dwóch pomyślnych zdarzeń logowania z dwóch lokalizacji, które są niedostępne w ramach przedziału czasu dla dwóch zdarzeń logowania.
- Nieoczekiwana Lokalizacja — Lokalizacja, w której wystąpiło podejrzane zdarzenie logowania. Na przykład lokalizacja nie była ostatnio widoczna.
 
To wykrywanie wymaga określonej konfiguracji łącznika danych dziennika systemowego: 

1. Dla kroku 2 w obszarze [Konfigurowanie agenta log Analytics](#configure-the-log-analytics-agent) powyżej upewnij się, że zarówno **uwierzytelnianie** , jak i **authpriv** są wybrane jako urządzenia do monitorowania, i że są wybrane wszystkie. 

2. Zezwalaj na zbieranie informacji dziennika systemu wystarczająco długo. Następnie przejdź do okna badanie **wskaźnikowe platformy Azure** i skopiuj i wklej następujące zapytanie:
    
    ```kusto
    Syslog
    | where Facility in ("authpriv","auth")
    | extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)
    | where isnotempty(c)
    | count 
    ```
    
    W razie potrzeby zmień **zakres czasu** , a następnie wybierz pozycję **Uruchom**.
    
    Jeśli wynikowa liczba jest równa zero, Potwierdź konfigurację łącznika i że monitorowane komputery mają działanie pomyślnego logowania dla okresu określonego dla zapytania.
    
    Jeśli wynikowa liczba jest większa od zera, dane dziennika systemowego są odpowiednie dla nietypowego wykrywania logowania SSH. To wykrywanie można włączyć z   >   **szablonów reguł** analizy  >  **(wersja zapoznawcza) wykrywanie nietypowego logowania SSH**.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia lokalnych urządzeń dziennika systemu z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.

