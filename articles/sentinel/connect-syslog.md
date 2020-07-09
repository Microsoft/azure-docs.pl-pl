---
title: Łączenie danych dziennika systemowego z platformą Azure Microsoft Docs
description: Podłącz każde urządzenie lokalne obsługujące dziennik systemowy do platformy Azure, korzystając z agenta na komputerze z systemem Linux między urządzeniem a wskaźnikiem kontrolnym. 
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 65c4e5d9e0752379541063c8a80a4316196ad7c3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565370"
---
# <a name="connect-your-external-solution-using-syslog"></a>Łączenie rozwiązania zewnętrznego przy użyciu dziennika systemowego

Można podłączyć urządzenie lokalne obsługujące dziennik systemowy do platformy Azure. Odbywa się to przy użyciu agenta opartego na komputerze z systemem Linux między urządzeniem a platformą Azure. Jeśli maszyna z systemem Linux znajduje się na platformie Azure, możesz przesłać strumieniowo dzienniki z urządzenia lub aplikacji do dedykowanego obszaru roboczego tworzonego na platformie Azure i połączyć go. Jeśli maszyna z systemem Linux nie znajduje się na platformie Azure, możesz przesłać strumieniowo dzienniki z urządzenia do dedykowanej maszyny wirtualnej lub na maszynie, na której jest instalowany Agent programu dla systemu Linux. 

> [!NOTE]
> Jeśli urządzenie obsługuje dziennik systemowy CEF, połączenie jest bardziej kompletne i należy wybrać tę opcję i postępować zgodnie z instrukcjami w temacie [łączenie danych z CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Jak to działa

Dziennik systemowy to protokół rejestrowania zdarzeń, który jest wspólny dla systemu Linux. Aplikacje będą wysyłać komunikaty, które mogą być przechowywane na komputerze lokalnym lub dostarczane do modułu zbierającego dziennik systemowy. Po zainstalowaniu agenta Log Analytics dla systemu Linux program skonfiguruje lokalny demon dziennika systemowego, aby przekazywać komunikaty do agenta. Następnie Agent wysyła komunikat do Azure Monitor, w którym zostanie utworzony odpowiedni rekord.

Aby uzyskać więcej informacji, zobacz [źródła danych dziennika systemowego w Azure monitor](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> - Agent może zbierać dzienniki z wielu źródeł, ale muszą być zainstalowane na dedykowanym komputerze proxy.
> - Aby umożliwić obsługę łączników dla CEF i dziennika systemowego na tej samej maszynie wirtualnej, wykonaj następujące kroki, aby uniknąć duplikowania danych:
>    1. Postępuj zgodnie z instrukcjami, aby [nawiązać połączenie z usługą CEF](connect-common-event-format.md).
>    2. Aby połączyć dane dziennika systemu, przejdź do pozycji **Ustawienia**  >  **obszaru roboczego**ustawienia  >  **Zaawansowane ustawienia**  >  **danych**  >  **dziennika** systemowego i ustaw obiekty i ich priorytety, tak aby nie były to te same funkcje i właściwości, które zostały użyte w konfiguracji CEF. <br></br>W przypadku wybrania opcji **Zastosuj poniższą konfigurację do moich maszyn**te ustawienia są stosowane do wszystkich maszyn wirtualnych połączonych z tym obszarem roboczym.


## <a name="connect-your-syslog-appliance"></a>Połącz urządzenie dziennika systemu

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie wybierz pozycję Łącznik **dziennika** systemowego.

2. W bloku **Dziennik** systemowy wybierz pozycję **Otwórz stronę łącznika**.

3. Zainstaluj agenta systemu Linux:
    
    - Jeśli maszyna wirtualna z systemem Linux znajduje się na platformie Azure, wybierz pozycję **Pobierz i Zainstaluj agenta na maszynie wirtualnej platformy Azure z systemem Linux**. W bloku **maszyny wirtualne** Wybierz Maszyny wirtualne, na których ma zostać zainstalowany agent, a następnie kliknij przycisk **Połącz**.
    - Jeśli maszyna z systemem Linux nie znajduje się na platformie Azure, wybierz pozycję **Pobierz i Zainstaluj agenta na maszynie spoza platformy Azure**. W bloku **agenta bezpośredniego** Skopiuj polecenie pobierania i dołączania **agenta dla systemu Linux** i uruchom je na komputerze. 
    
   > [!NOTE]
   > Upewnij się, że skonfigurowano ustawienia zabezpieczeń dla tych komputerów zgodnie z zasadami zabezpieczeń organizacji. Można na przykład skonfigurować ustawienia sieci, aby dostosować je do zasad zabezpieczeń sieci organizacji, i zmienić porty i protokoły w demona, aby dostosować je do wymagań dotyczących zabezpieczeń.

4. Wybierz pozycję **Otwórz konfigurację ustawień zaawansowanych obszaru roboczego**.

5. W bloku **Ustawienia zaawansowane** wybierz pozycję Dziennik systemowy **danych**  >  **Syslog**. Następnie Dodaj obiekty do zebrania dla łącznika.
    
    Dodaj obiekty, które urządzenie dziennika systemu zawiera w jego nagłówkach dziennika. Tę konfigurację można wyświetlić w urządzeniu dziennika systemowego w folderze **dziennika** `/etc/rsyslog.d/security-config-omsagent.conf` systemowego, a w obszarze **r-dziennik** systemu `/etc/syslog-ng/security-config-omsagent.conf` .
    
    Jeśli chcesz użyć nietypowego wykrywania logowania SSH za pomocą zbieranych danych, Dodaj **uwierzytelnianie** i **authpriv**. Aby uzyskać dodatkowe informacje, zobacz [następującą sekcję](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) .

6. Po dodaniu wszystkich funkcji, które mają być monitorowane, i dostosowaniu wszystkich opcji ważności dla każdej z nich, zaznacz pole wyboru **Zastosuj poniższą konfigurację do moich maszyn**.

7. Wybierz pozycję **Zapisz**. 

8. Upewnij się, że w urządzeniu dziennika systemowego są wysyłane określone obiekty.

9. Aby użyć odpowiedniego schematu w Azure Monitor dla dzienników dziennika systemowego, Wyszukaj **Dziennik**systemowy.

10. Aby przeanalizować komunikaty dziennika systemowego, można użyć funkcji Kusto opisanej w temacie using Functions [in Azure Monitor Log zapytania](../azure-monitor/log-query/functions.md) . Następnie można zapisać je jako nową funkcję Log Analytics, aby użyć jej jako nowego typu danych.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Konfigurowanie łącznika dziennika systemowego na potrzeby wykrywania nietypowego logowania SSH

> [!IMPORTANT]
> Nietypowe wykrywanie logowania SSH jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wskaźnik "Azure" może stosować Uczenie maszynowe (ML) do danych dziennika systemowego, aby zidentyfikować nietypowe działanie logowania Secure Shell (SSH). Scenariusze obejmują:

- Niemożliwa podróż — w przypadku dwóch pomyślnych zdarzeń logowania z dwóch lokalizacji, które są niedostępne w ramach przedziału czasu dla dwóch zdarzeń logowania.
- Nieoczekiwana Lokalizacja — Lokalizacja, w której wystąpiło podejrzane zdarzenie logowania. Na przykład lokalizacja nie była ostatnio widoczna.
 
To wykrywanie wymaga określonej konfiguracji łącznika danych dziennika systemowego: 

1. W przypadku kroku 5 w poprzedniej procedurze upewnij się, że zarówno **uwierzytelnianie** , jak i **authpriv** są wybrane jako urządzenia do monitorowania. Zachowaj ustawienia domyślne opcji ważności, aby były zaznaczone. Przykład:
    
    > [!div class="mx-imgBorder"]
    > ![Urządzenia wymagane do wykrycia nietypowego logowania SSH](./media/connect-syslog/facilities-ssh-detection.png)

2. Zezwalaj na zbieranie informacji dziennika systemu wystarczająco długo. Następnie przejdź do okna badanie **wskaźnikowe platformy Azure**i skopiuj i wklej następujące zapytanie:
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    W razie potrzeby zmień **zakres czasu** , a następnie wybierz pozycję **Uruchom**.
    
    Jeśli wynikowa liczba jest równa zero, Potwierdź konfigurację łącznika i że monitorowane komputery mają działanie pomyślnego logowania dla okresu określonego dla zapytania.
    
    Jeśli wynikowa liczba jest większa od zera, dane dziennika systemowego są odpowiednie dla nietypowego wykrywania logowania SSH. To wykrywanie można włączyć z **Analytics**  >   **szablonów reguł**analizy  >  **(wersja zapoznawcza) wykrywanie nietypowego logowania SSH**.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia lokalnych urządzeń dziennika systemu z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.

