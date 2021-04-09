---
title: Rozwiązywanie problemów z serwerem przetwarzania Azure Site Recovery
description: W tym artykule opisano sposób rozwiązywania problemów z serwerem przetwarzania Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: ad1bec66edaa3fcc6049f4911684f6e6d6c3e366
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999194"
---
# <a name="troubleshoot-the-process-server"></a>Rozwiązywanie problemów z serwerem przetwarzania

Serwer przetwarzania [Site Recovery](site-recovery-overview.md) jest używany podczas konfigurowania odzyskiwania po awarii na platformie Azure dla lokalnych maszyn wirtualnych VMware i serwerów fizycznych. W tym artykule opisano sposób rozwiązywania problemów z serwerem przetwarzania, w tym problemy z replikacją i łącznością.

[Dowiedz się więcej](vmware-physical-azure-config-process-server-overview.md) o serwerze przetwarzania.

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem rozwiązywania problemów:

1. Upewnij się, że wiesz, jak [monitorować serwery procesów](vmware-physical-azure-monitor-process-server.md).
2. Zapoznaj się z najlepszymi rozwiązaniami poniżej.
3. Upewnij się, że postępuj zgodnie z [zagadnieniami](site-recovery-plan-capacity-vmware.md#capacity-considerations)dotyczącymi pojemności, i Zastosuj wskazówki dotyczące zmiany wielkości [serwera konfiguracji](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) lub [autonomicznych serwerów procesów](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Najlepsze rozwiązania dotyczące wdrażania serwera przetwarzania

W celu uzyskania optymalnej wydajności serwerów przetwarzania podsumowano kilka ogólnych najlepszych rozwiązań.

**Najlepsze rozwiązanie** | **Szczegóły**
--- |---
**Użycie** | Upewnij się, że serwer konfiguracji/autonomiczny serwer procesu są używane tylko na potrzeby zamierzonego celu. Nie uruchamiaj żadnych innych elementów na komputerze.
**Adres IP** | Upewnij się, że serwer przetwarzania ma statyczny adres IPv4 i nie ma skonfigurowanego translatora adresów sieciowych.
**Sterowanie użyciem pamięci/procesora CPU** |Zachowaj użycie procesora i pamięci na 70%.
**Upewnij się, że ilość wolnego miejsca** | Wolne miejsce odnosi się do miejsca na dysku pamięci podręcznej na serwerze przetwarzania. Dane replikacji są przechowywane w pamięci podręcznej przed przekazaniem ich do platformy Azure.<br/><br/> Zachowaj wolne miejsce powyżej 25%. Jeśli spadnie poniżej 20%, replikacja zostanie ograniczona dla replikowanych maszyn skojarzonych z serwerem przetwarzania.

## <a name="check-process-server-health"></a>Sprawdź kondycję serwera przetwarzania

Pierwszym krokiem w rozwiązywaniu problemów jest sprawdzenie kondycji i stanu serwera przetwarzania. Aby to zrobić, przejrzyj wszystkie alerty, sprawdź, czy są uruchomione wymagane usługi, i sprawdź, czy istnieje puls z serwera przetwarzania. Te kroki zostały podsumowane na poniższej ilustracji, a następnie procedury ułatwiające wykonanie tych czynności.

![Rozwiązywanie problemów z kondycją serwera przetwarzania](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Krok 1. Rozwiązywanie problemów z alertami kondycji serwera przetwarzania

Serwer przetwarzania generuje wiele alertów dotyczących kondycji. Te alerty i zalecane akcje zostały podsumowane w poniższej tabeli.

**Typ alertu** | **Błąd** | **Rozwiązywanie problemów**
--- | --- | --- 
![Dobra kondycja][green] | Brak  | Serwer przetwarzania jest podłączony i jest w dobrej kondycji.
![Ostrzeżenie][yellow] | Określone usługi nie są uruchomione. | 1. Sprawdź, czy usługi są uruchomione.<br/> 2. Jeśli usługi działają zgodnie z oczekiwaniami, postępuj zgodnie z poniższymi instrukcjami, aby [rozwiązać problemy z łącznością i replikacją](#check-connectivity-and-replication).
![Ostrzeżenie][yellow]  | Użycie procesora CPU > 80% dla ostatnich 15 minut. | 1. nie dodawaj nowych maszyn.<br/>2. Sprawdź, czy liczba maszyn wirtualnych używających serwera przetwarzania jest wyrównana do [zdefiniowanych limitów](site-recovery-plan-capacity-vmware.md#capacity-considerations), i rozważ skonfigurowanie [dodatkowego serwera przetwarzania](vmware-azure-set-up-process-server-scale.md).<br/>3. Postępuj zgodnie z poniższymi instrukcjami, aby [rozwiązać problemy z łącznością i replikacją](#check-connectivity-and-replication).
![Krytyczne][red] |  Użycie procesora CPU > 95% dla ostatnich 15 minut. | 1. nie dodawaj nowych maszyn.<br/>2. Sprawdź, czy liczba maszyn wirtualnych używających serwera przetwarzania jest wyrównana do [zdefiniowanych limitów](site-recovery-plan-capacity-vmware.md#capacity-considerations), i rozważ skonfigurowanie [dodatkowego serwera przetwarzania](vmware-azure-set-up-process-server-scale.md).<br/>3. Postępuj zgodnie z poniższymi instrukcjami, aby [rozwiązać problemy z łącznością i replikacją](#check-connectivity-and-replication).<br/> 4. Jeśli problem będzie się powtarzać, uruchom [planista wdrażania](./site-recovery-deployment-planner.md) na potrzeby replikacji oprogramowania VMware/serwera fizycznego.
![Ostrzeżenie][yellow] | Użycie pamięci > 80% dla ostatnich 15 minut. |  1. nie dodawaj nowych maszyn.<br/>2. Sprawdź, czy liczba maszyn wirtualnych używających serwera przetwarzania jest wyrównana do [zdefiniowanych limitów](site-recovery-plan-capacity-vmware.md#capacity-considerations), i rozważ skonfigurowanie [dodatkowego serwera przetwarzania](vmware-azure-set-up-process-server-scale.md).<br/>3. Postępuj zgodnie z wszelkimi instrukcjami związanymi z ostrzeżeniem.<br/> 4. Jeśli problem będzie się powtarzać, postępuj zgodnie z poniższymi instrukcjami, aby [rozwiązać problemy z łącznością i replikacją](#check-connectivity-and-replication).
![Krytyczne][red] | Użycie pamięci > 95% dla ostatnich 15 minut. | 1. nie dodawaj nowych maszyn i rozważ skonfigurowanie [dodatkowego serwera przetwarzania](vmware-azure-set-up-process-server-scale.md).<br/> 2. Postępuj zgodnie z wszelkimi instrukcjami związanymi z ostrzeżeniem.<br/> 3.4. Jeśli problem będzie się powtarzał, postępuj zgodnie z poniższymi instrukcjami, aby [rozwiązać problemy z łącznością i replikacją](#check-connectivity-and-replication).<br/> 4. Jeśli problem będzie się powtarzać, uruchom [planista wdrażania](./site-recovery-deployment-planner.md) w przypadku problemów z replikacją oprogramowania VMware/serwera fizycznego.
![Ostrzeżenie][yellow] | Wolne miejsce w folderze pamięci podręcznej < 30% dla ostatnich 15 minut. | 1. nie dodawaj nowych maszyn i rozważ skonfigurowanie [dodatkowego serwera przetwarzania](vmware-azure-set-up-process-server-scale.md).<br/>2. Sprawdź, czy liczba maszyn wirtualnych używających serwera przetwarzania jest wyrównana do [wskazówek](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Postępuj zgodnie z poniższymi instrukcjami, aby [rozwiązać problemy z łącznością i replikacją](#check-connectivity-and-replication).
![Krytyczne][red] |  Wolne miejsce < 25% dla ostatnich 15 minut | 1. Postępuj zgodnie z instrukcjami związanymi z ostrzeżeniem dotyczącym tego problemu.<br/> 2.3. Postępuj zgodnie z poniższymi instrukcjami, aby [rozwiązać problemy z łącznością i replikacją](#check-connectivity-and-replication).<br/> 3. Jeśli problem będzie się powtarzać, uruchom [planista wdrażania](./site-recovery-deployment-planner.md) na potrzeby replikacji oprogramowania VMware/serwera fizycznego.
![Krytyczne][red] | Brak pulsu z serwera przetwarzania przez 15 minut lub dłużej. Usługa tmansvs nie komunikuje się z serwerem konfiguracji. | 1) Sprawdź, czy serwer przetwarzania jest uruchomiony.<br/> 2. Sprawdź, czy tmassvc jest uruchomiona na serwerze przetwarzania.<br/> 3. Postępuj zgodnie z poniższymi instrukcjami, aby [rozwiązać problemy z łącznością i replikacją](#check-connectivity-and-replication).


![Klucz tabeli](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Krok 2. Sprawdzanie usług serwera przetwarzania

Usługi, które powinny być uruchomione na serwerze przetwarzania, zostały podsumowane w poniższej tabeli. Istnieją niewielkie różnice w usługach, w zależności od sposobu wdrożenia serwera przetwarzania. 

Dla wszystkich usług, z wyjątkiem Microsoft Azure Recovery Services Agent (usługą obengine), sprawdź, czy dla elementu StartType ustawiono wartość **Automatyczne** lub **Automatyczne (opóźnione uruchomienie)**.
 
**Wdrożenie** | **Uruchomione usługi**
--- | ---
**Serwer przetwarzania na serwerze konfiguracji** | ProcessServer; ProcessServerMonitor; cxprocessserver InMage PushInstall; Usługa przekazywania dzienników (LogUpload); Usługa aplikacji InMage Scout; Agent Microsoft Azure Recovery Services (usługą obengine); Agent InMage Scout VX — wskaźnik kontrolny/wpis (svagents); tmansvc World Wide Web Publishing Service (W3SVC); MySQL Usługa Site Recovery Microsoft Azure (dra)
**Serwer przetwarzania działający jako serwer autonomiczny** | ProcessServer; ProcessServerMonitor; cxprocessserver InMage PushInstall; Usługa przekazywania dzienników (LogUpload); Usługa aplikacji InMage Scout; Agent Microsoft Azure Recovery Services (usługą obengine); Agent InMage Scout VX — wskaźnik kontrolny/wpis (svagents); tmansvc.
**Serwer przetwarzania wdrożony na platformie Azure na potrzeby powrotu po awarii** | ProcessServer; ProcessServerMonitor; cxprocessserver InMage PushInstall; Usługa przekazywania dzienników (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Krok 3. Sprawdzanie pulsu serwera przetwarzania

Jeśli nie ma pulsu z serwera przetwarzania (kod błędu 806), wykonaj następujące czynności:

1. Sprawdź, czy maszyna wirtualna serwera przetwarzania jest uruchomiona.
2. Sprawdź dzienniki pod kątem błędów.

    C:\ProgramData\ASR\home\svsystems\eventmanager *. log c\programdata\asr\home\svsystems\ monitor_protection*. log

## <a name="check-connectivity-and-replication"></a>Sprawdź łączność i replikację

 Początkowe i ciągłe błędy replikacji są często spowodowane problemami z łącznością między maszynami źródłowymi i serwerem przetwarzania albo między serwerem przetwarzania i platformą Azure. Te kroki zostały podsumowane na poniższej ilustracji, a następnie procedury ułatwiające wykonanie tych czynności.

![Schemat blokowy przedstawiający kroki rozwiązywania problemów z łącznością i replikacją.](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Krok 4. Weryfikowanie synchronizacji czasu na maszynie źródłowej

Upewnij się, że data/godzina systemowa replikowanej maszyny jest zsynchronizowana. [Dowiedz się więcej](/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Krok 5. sprawdzenie oprogramowania antywirusowego na maszynie źródłowej

Upewnij się, że żadne oprogramowanie antywirusowe na replikowanej maszynie nie blokuje Site Recovery. Jeśli musisz wykluczyć Site Recovery z programów antywirusowych, zapoznaj się z [tym artykułem](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Krok 6. Sprawdzanie łączności z poziomu maszyny źródłowej


1. Jeśli zachodzi taka potrzeba, zainstaluj [klienta programu Telnet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771275(v=ws.10)) na maszynie źródłowej. Nie używaj polecenia ping.
2. Na maszynie źródłowej Wyślij polecenie ping do serwera przetwarzania w porcie HTTPS za pomocą programu Telnet. Domyślnie 9443 jest portem HTTPS dla ruchu związanego z replikacją.

    `telnet <process server IP address> <port>`

3. Sprawdź, czy połączenie zostało nawiązane pomyślnie.


**Połączenia** | **Szczegóły** | **Akcja**
--- | --- | ---
**Wybran** | Program Telnet wyświetla pusty ekran, a serwer przetwarzania jest osiągalny. | Nie są wymagane żadne dalsze działania.
**Niepomyślnych** | Nie można nawiązać połączenia | Upewnij się, że na serwerze przetwarzania jest dozwolony port 9443 dla ruchu przychodzącego. Na przykład jeśli masz sieć obwodową lub podsieć z osłoną. Sprawdź połączenie ponownie.
**Częściowo powodzenie** | Można nawiązać połączenie, ale maszyna źródłowa zgłasza, że serwer przetwarzania nie jest osiągalny. | Kontynuuj pracę z następną procedurą rozwiązywania problemów.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Krok 7. Rozwiązywanie problemów z nieosiągalnym serwerem przetwarzania

Jeśli serwer przetwarzania nie jest dostępny z maszyny źródłowej, zostanie wyświetlony komunikat o błędzie 78186. Jeśli nie rozwiąże to problemu, ten problem będzie prowadzić do tego, że punkty odzyskiwania spójne z aplikacjami i po awarii nie są generowane zgodnie z oczekiwaniami.

Rozwiązywanie problemów, sprawdzając, czy maszyna źródłowa może uzyskać dostęp do adresu IP serwera przetwarzania, i uruchom narzędzie cxpsclient na maszynie źródłowej, aby sprawdzić połączenie typu end-to-end.


### <a name="check-the-ip-connection-on-the-process-server"></a>Sprawdź połączenie IP na serwerze przetwarzania

Jeśli Telnet zakończy się pomyślnie, ale maszyna źródłowa zgłasza, że serwer przetwarzania nie jest osiągalny, sprawdź, czy można uzyskać dostęp do adresu IP serwera przetwarzania.

1. W przeglądarce sieci Web spróbuj skontaktować się z adresem IP https://<PS_IP>: <PS_Data_Port>/.
2. Jeśli ten test pokazuje błąd certyfikatu HTTPS, jest to normalne. Jeśli zignorujesz błąd, powinno być widoczne żądanie 400-złe. Oznacza to, że serwer nie może obsłużyć żądania przeglądarki i że standardowe połączenie HTTPS jest prawidłowo.
3. Jeśli to sprawdzenie nie działa, należy zwrócić uwagę na komunikat o błędzie przeglądarki. Na przykład błąd 407 wskazuje na problem z uwierzytelnianiem serwera proxy.

### <a name="check-the-connection-with-cxpsclient"></a>Sprawdź połączenie z usługą cxpsclient

Ponadto można uruchomić narzędzie cxpsclient, aby sprawdzić kompleksowe połączenie.

1. Uruchom narzędzie w następujący sposób:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. Na serwerze przetwarzania Sprawdź wygenerowane dzienniki w następujących folderach:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Sprawdź dzienniki źródłowej maszyny wirtualnej pod kątem błędów przekazywania (błąd 78028)

Problem z przekazywaniem danych blokowanym z maszyn źródłowych do usługi procesu może spowodować, że nie są generowane punkty odzyskiwania spójne pod względem awarii i aplikacje. 

1. Aby rozwiązać problemy z błędami przekazywania sieci, można wyszukać błędy w tym dzienniku:

    C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\svagents *. log 

2. Pozostałe procedury przedstawione w tym artykule mogą pomóc w rozwiązywaniu problemów z przekazywaniem danych.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Krok 8. Sprawdzanie, czy serwer przetwarzania wypycha dane

Sprawdź, czy serwer przetwarzania aktywnie wypychanie danych do platformy Azure.

  1. Na serwerze przetwarzania Otwórz Menedżera zadań (naciśnij klawisze CTRL + SHIFT + ESC).
  2. Wybierz kartę **wydajność** > **Otwórz Monitor zasobów**.
  3. Na stronie **Monitor zasobów** wybierz kartę **Sieć** . W obszarze **procesy z aktywność sieci** Sprawdź, czy cbengine.exe aktywnie wysyła dużą ilość danych.

       ![Zrzut ekranu przedstawiający dużą liczbę woluminów w ramach procesów z aktywnością sieciową.](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Jeśli cbengine.exe nie wysyła dużej ilości danych, wykonaj kroki opisane w poniższych sekcjach.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Krok 9. sprawdzenie połączenia serwera przetwarzania z usługą Azure Blob Storage

1. W Monitor zasobów wybierz pozycję **cbengine.exe**.
2. W obszarze **połączenia protokołu TCP** Sprawdź, czy istnieje łączność z serwerem przetwarzania do usługi Azure Storage.

  ![Zrzut ekranu przedstawiający łączność między cbengine.exe i adresem URL usługi Azure Blob Storage.](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Sprawdź usługi

Jeśli nie ma łączności z serwerem przetwarzania do adresu URL usługi Azure Blob Storage, sprawdź, czy są uruchomione.

1. W panelu sterowania wybierz pozycję **usługi**.
2. Sprawdź, czy są uruchomione następujące usługi:

    - cxprocessserver
    - Agent InMage Scout VX — wskaźnik kontrolny/wpis
    - Agent Microsoft Azure Recovery Services
    - Usługa Site Recovery Microsoft Azure
    - tmansvc

3. Uruchom lub Uruchom ponownie dowolną usługę, która nie jest uruchomiona.
4. Sprawdź, czy serwer przetwarzania jest połączony i osiągalny. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Krok 10. sprawdzenie połączenia serwera przetwarzania z publicznym adresem IP platformy Azure

1. Na serwerze przetwarzania w **folderze%ProgramFiles%\Microsoft Azure Recovery Services Agent\Temp** Otwórz najnowszy plik CBEngineCurr. errlog.
2. W pliku Wyszukaj wartość **443** lub dla **próby połączenia ciągu nie powiodło się**.

  ![Dzienniki błędów w folderze tymczasowym](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Jeśli zobaczysz problemy, należy zlokalizować publiczny adres IP platformy Azure w pliku CBEngineCurr. currLog przy użyciu portu 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. W wierszu polecenia na serwerze przetwarzania Użyj programu Telnet, aby wysłać polecenie ping do publicznego adresu IP platformy Azure.
6. Jeśli nie możesz się połączyć, postępuj zgodnie z następną procedurą.

## <a name="step-11-check-process-server-firewall-settings"></a>Krok 11. sprawdzenie ustawień zapory serwera przetwarzania. 

Sprawdź, czy zapora oparta na adresie IP na serwerze przetwarzania blokuje dostęp.

1. Reguły zapory oparte na adresach IP:

    a) Pobierz pełną listę [zakresów adresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

    b) Dodaj zakresy adresów IP do konfiguracji zapory, aby upewnić się, że zapora zezwala na komunikację z platformą Azure (i z domyślnym portem HTTPS, 443).

    c) Zezwalaj na zakresy adresów IP w regionie świadczenia usługi Azure subskrypcji oraz w regionie zachodnie stany USA (używanym do kontroli dostępu i zarządzania tożsamościami).

2. W przypadku zapór opartych na adresach URL należy dodać do konfiguracji zapory adresy URL wymienione w poniższej tabeli.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Krok 12. Weryfikowanie ustawień serwera proxy serwera przetwarzania 

1. Jeśli używasz serwera proxy, upewnij się, że nazwa serwera proxy jest rozpoznawana przez serwer DNS. Sprawdź wartość podaną podczas konfigurowania serwera konfiguracji w kluczu rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Upewnij się, że te same ustawienia są używane przez agenta Azure Site Recovery do wysyłania danych.

    a) Wyszukaj **Microsoft Azure Backup**.

    b) Otwórz **Microsoft Azure Backup** i wybierz pozycję **Akcja**  >  **Zmień właściwości**.

    c) na karcie **Konfiguracja serwera proxy** adres serwera proxy powinien być taki sam jak adres serwera proxy, który jest wyświetlany w ustawieniach rejestru. Jeśli nie, Zmień ją na ten sam adres.

## <a name="step-13-check-bandwidth"></a>Krok 13. Sprawdzanie przepustowości

Zwiększ przepustowość między serwerem przetwarzania i platformą Azure, a następnie sprawdź, czy problem nadal występuje.


## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz więcej pomocy, Opublikuj swoje pytanie na [stronie pytania&](/answers/topics/azure-site-recovery.html)pytań i odpowiedzi dla Azure Site Recovery. 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png