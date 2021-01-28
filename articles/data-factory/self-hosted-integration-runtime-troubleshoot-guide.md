---
title: Rozwiązywanie problemów z własnym hostowanym środowiskiem Integration Runtime w Azure Data Factory
description: Dowiedz się, jak rozwiązywać problemy z nieobsługiwanym środowiskiem Integration Runtime w Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.author: lle
ms.openlocfilehash: 8a722550d12d019e25ff39de27cc0df2c2762a01
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942052"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Rozwiązywanie problemów z własnym hostowanym środowiskiem Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów z własnym środowiskiem Integration Runtime (IR) w Azure Data Factory.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Zbieraj własne dzienniki środowiska IR z Azure Data Factory

W przypadku działań zakończonych niepowodzeniem, które działają na samoobsługowym lub udostępnionym środowisku IR, Azure Data Factory obsługuje wyświetlanie i przekazywanie dzienników błędów. Aby uzyskać identyfikator raportu o błędach, postępuj zgodnie z instrukcjami w tym miejscu, a następnie wprowadź identyfikator raportu, aby wyszukać powiązane znane problemy.

1. W obszarze Data Factory wybierz pozycję **uruchomienia potoku**.

1. W obszarze **uruchomienia działania** w kolumnie **błąd** wybierz wyróżniony przycisk, aby wyświetlić dzienniki aktywności, jak pokazano na poniższym zrzucie ekranu:

    ![Zrzut ekranu przedstawiający sekcję "uruchomienia działania" w okienku "wszystkie uruchomienia potoków".](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

    Dzienniki aktywności są wyświetlane dla uruchomienia działania zakończonego niepowodzeniem.

    ![Zrzut ekranu przedstawiający dzienniki aktywności dla działania zakończonego niepowodzeniem.](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png) 
    
1. Aby uzyskać dalszą pomoc, wybierz pozycję **Wyślij dzienniki**.
 
   Zostanie otwarte okno **udostępnianie dzienników środowiska Integration Runtime (IR) w programie Microsoft** .

    ![Zrzut ekranu przedstawiający okno "udostępnianie dzienników środowiska Integration Runtime (IR) w systemie Microsoft".](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Wybierz dzienniki, które chcesz wysłać. 
    * W przypadku *własnego środowiska IR* można przekazać dzienniki związane z działaniem zakończonym niepowodzeniem lub wszystkie dzienniki w węźle samodzielnego środowiska IR. 
    * W przypadku *udostępnionego środowiska IR* można przekazać tylko dzienniki powiązane z działaniem zakończonym niepowodzeniem.

1. Po przekazaniu dzienników należy zachować rekord identyfikatora raportu do późniejszego użycia, jeśli potrzebna jest dalsza pomoc w rozwiązaniu problemu.

    ![Zrzut ekranu przedstawiający wyświetlany identyfikator raportu w oknie postęp przekazywania dla dzienników IR.](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Przeglądanie dzienników i przekazywanie żądań są wykonywane we wszystkich nieobsługiwanych przez Internet wystąpieniach środowiska IR. W przypadku braku dzienników upewnij się, że wszystkie własne wystąpienia środowiska IR są w trybie online. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Ogólny błąd lub awaria własnego środowiska IR

### <a name="out-of-memory-issue"></a>Problem z brakiem pamięci

#### <a name="symptoms"></a>Objawy

Wystąpił błąd OutOfMemoryException (OOM) podczas próby uruchomienia działania wyszukiwania za pomocą połączonego środowiska IR lub samodzielnego środowiska IR.

#### <a name="cause"></a>Przyczyna

Nowe działanie może zgłosić błąd OOM, jeśli maszyna podczerwieni jest w stanie bardzo duże użycie pamięci. Problem może być spowodowany dużą ilością współbieżnych działań i błędem jest projekt.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź użycie zasobów i współbieżne wykonywanie działań w węźle IR. Dostosuj czas wewnętrzny i wyzwalanie przebiegów działania, aby uniknąć zbyt wielu operacji na pojedynczym węźle IR w tym samym czasie.

### <a name="concurrent-jobs-limit-issue"></a>Problem z limitem zadań współbieżnych

#### <a name="symptoms"></a>Objawy

Przy próbie zwiększenia limitu współbieżnych zadań z interfejsu Azure Data Factory, proces zawiesza się podczas *aktualizowania* stanu.

Przykładowy scenariusz: wartość maksymalna liczba współbieżnych zadań jest obecnie ustawiona na 24 i chcesz zwiększyć liczbę, aby zadania mogły działać szybciej. Minimalna wartość, którą można wprowadzić to 3, a wartość maksymalna to 32. Zwiększ wartość z 24 do 32, a następnie wybierz przycisk **Aktualizuj** . Proces jest zablokowany podczas *aktualizowania* stanu, jak pokazano na poniższym zrzucie ekranu. Odświeżasz stronę, a wartość jest nadal wyświetlana jako 24. Nie została zaktualizowana do 32 zgodnie z oczekiwaniami.

![Zrzut ekranu przedstawiający okienko węzły środowiska Integration Runtime, który wyświetla proces zablokowany w stanie "Aktualizowanie".](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Przyczyna

Limit liczby współbieżnych zadań zależy od rdzenia i pamięci logicznej komputera. Spróbuj dopasować wartość w dół do wartości takiej jak 24, a następnie wyświetlić wynik.

> [!TIP] 
> - Aby dowiedzieć się więcej o liczbie rdzeni logiki i określić liczbę rdzeni logiki komputera, zobacz [cztery sposoby znajdowania liczby rdzeni w procesorze CPU w systemie Windows 10](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> - Aby dowiedzieć się, jak obliczyć Math. log, przejdź do [kalkulatora logarytmu](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-high-availability-ha-ssl-certificate-issue"></a>Samoobsługowy problem z certyfikatem SSL wysokiej dostępności (HA) dla środowiska IR

#### <a name="symptoms"></a>Objawy

Węzeł pracy samodzielnego środowiska IR zgłosił następujący błąd:

"Nie można ściągnąć udostępnionych Stanów z węzła podstawowego net. TCP://abc.cloud.corp.Microsoft.com: 8060/ExternalService. svc/. Identyfikator działania: XXXXX certyfikat X. 509 CN = ABC. Cloud. Corp. Microsoft. com, OU = Test, O = kompilacja łańcucha firmy Microsoft nie powiodła się. Używany certyfikat ma łańcuch zaufania, którego nie można zweryfikować. Zastąp certyfikat lub Zmień certificateValidationMode. Funkcja odwoływania nie mogła sprawdzić odwołania, ponieważ serwer odwołań był w trybie offline.

#### <a name="cause"></a>Przyczyna

W przypadku obsługi przypadków związanych z uzgadnianiem SSL/TLS mogą wystąpić problemy związane z weryfikacją łańcucha certyfikatów. 

#### <a name="resolution"></a>Rozwiązanie

- Oto szybki i intuicyjny sposób rozwiązywania problemów z kompilacją łańcucha certyfikatów X. 509:
 
    1. Wyeksportuj certyfikat, który ma zostać zweryfikowany. Aby to zrobić, wykonaj następujące czynności:
    
       a. W systemie Windows wybierz pozycję **Start**, zacznij pisać **Certyfikaty**, a następnie wybierz pozycję **Zarządzaj certyfikatami komputerów**.
       
       b. W Eksploratorze plików w okienku po lewej stronie Wyszukaj certyfikat do sprawdzenia, kliknij go prawym przyciskiem myszy, a następnie wybierz pozycję **wszystkie zadania**  >  **Eksportuj**.
    
        ![Zrzut ekranu przedstawiający kontrolkę "wszystkie zadania" > "Eksportuj" dla certyfikatu w okienku "Zarządzanie certyfikatami komputera".](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Skopiuj wyeksportowany certyfikat do komputera klienckiego. 
    3. Na stronie klienta, w oknie wiersza polecenia, uruchom następujące polecenie. Pamiętaj, aby zamienić *\<certificate path>* i na *\<output txt file path>* rzeczywiste ścieżki.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Na przykład:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Sprawdź, czy w wyjściowym pliku TXT są błędy. Podsumowanie błędu można znaleźć na końcu pliku TXT.

        Na przykład: 

        ![Zrzut ekranu przedstawiający podsumowanie błędu na końcu pliku TXT.](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Jeśli na końcu pliku dziennika nie widzisz błędu, jak pokazano na poniższym zrzucie ekranu, można rozważyć, że łańcuch certyfikatów został pomyślnie skompilowany na komputerze klienckim.
        
        ![Zrzut ekranu przedstawiający plik dziennika nie zawiera błędów.](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Jeśli w pliku certyfikatu jest skonfigurowane rozszerzenie AIA (dostęp do informacji o urzędach), CDP (punkt dystrybucji listy CRL) lub protokołu OCSP (online stanu certyfikatu), można go sprawdzić w bardziej intuicyjny sposób:
 
    1. Pobierz te informacje, sprawdzając szczegóły certyfikatu, jak pokazano na poniższym zrzucie ekranu:
    
        ![Zrzut ekranu przedstawiający szczegóły certyfikatu.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    
    1. Uruchom następujące polecenie. Pamiętaj, aby zastąpić *\<certificate path>* bieżącą ścieżką do certyfikatu.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    
        Zostanie otwarte narzędzie pobierania adresu URL. 
        
    1. Aby sprawdzić certyfikaty z rozszerzeniem nazwy pliku AIA, CDP i OCSP, wybierz pozycję **Pobierz**.

        ![Zrzut ekranu przedstawiający narzędzie pobierania adresu URL i przycisk Pobierz.](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        Łańcuch certyfikatów został pomyślnie skonstruowany, jeśli stan certyfikatu z AIA zostanie *zweryfikowany* , a stan certyfikatu z listy CDP lub OCSP zostanie *zweryfikowany*.

        Jeśli podczas próby pobrania AIA lub CDP wystąpi błąd, skontaktuj się z zespołem sieci w celu przygotowania komputera klienckiego do połączenia z docelowym adresem URL. Wystarczy, że można zweryfikować ścieżkę HTTP lub ścieżkę protokołu LDAP (Lightweight Directory Access Protocol).

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>Własne środowisko IR nie może załadować pliku lub zestawu

#### <a name="symptoms"></a>Objawy

Zostanie wyświetlony następujący komunikat o błędzie:

"Nie można załadować pliku lub zestawu" XXXXXXXXXXXXXXXX, Version = 4.0.2.0, Culture = neutral, PublicKeyToken = XXXXXXXXX "lub jednej z jego zależności. W systemie nie można odnaleźć określonego pliku. Identyfikator działania: 92693b45-b4bf-4FC8-89da-2d3dc56f27c3 "
 
Oto bardziej szczegółowy komunikat o błędzie: 

"Nie można załadować pliku lub zestawu" System. ValueTuple, Version = 4.0.2.0, Culture = neutral, PublicKeyToken = XXXXXXXXX "lub jednej z jego zależności. W systemie nie można odnaleźć określonego pliku. Identyfikator działania: 92693b45-b4bf-4FC8-89da-2d3dc56f27c3 "

#### <a name="cause"></a>Przyczyna

W monitorze procesów można wyświetlić następujący wynik:

[![Zrzut ekranu przedstawiający listę ścieżek w monitorze procesu.](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> W monitorze procesów można ustawić filtry, jak pokazano na poniższym zrzucie ekranu.
>
> Poprzedni komunikat o błędzie wskazuje, że biblioteka DLL system. ValueTuple nie znajduje się w folderze  w folderze *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* lub folderze *c:\Program Files\Microsoft Integration Runtime\4.0\Shared* .
>
> W zasadzie proces ładuje najpierw bibliotekę DLL z folderu *GAC* , następnie z folderu *udostępnionego* , a wreszcie z folderu *Gateway* . W związku z tym można załadować bibliotekę DLL z dowolnej ścieżki, która jest przydatna.

<br>

![Zrzut ekranu przedstawiający stronę "filtr monitora procesów" z listą filtrów dla biblioteki DLL.](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Rozwiązanie

Plik *System.ValueTuple.dll* znajduje się w folderze *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* . Aby rozwiązać ten problem, skopiuj plik *System.ValueTuple.dll* do folderu *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* .

Tej samej metody można użyć do rozwiązywania innych problemów braku pliku lub zestawu.

#### <a name="more-information-about-this-issue"></a>Więcej informacji o tym problemie

Powód, dla którego widzisz *System.ValueTuple.dll* w obszarze *%windir%\Microsoft.NET\assembly* i *%windir%\assembly* , jest to zachowanie platformy .NET. 

W poniższym błędzie można jasno zobaczyć, że brakuje zestawu *System. ValueTuple* . Ten problem występuje, gdy aplikacja próbuje sprawdzić zestaw *System.ValueTuple.dll* .
 
" \<LogProperties> \<ErrorInfo> [{" Code ": 0," Message ":" Inicjator typów dla elementu "Npgsql. poolmanager" zgłosił wyjątek. "," EventType ": 0," Category ": 5," Data ": {} ," MsgId ": null," ExceptionType ":" System. TypeInitializationException "," source ":" Npgsql "," ślad stosu ":" "," InnerEventInfos ": [{" Code ": 0," Message ":" nie można załadować pliku lub zestawu "System. ValueTuple, Version = 4.0.2.0, Culture = neutral, PublicKeyToken = XXXXXXXXX" lub jedną z jej zależności. System nie może odnaleźć określonego pliku. "," EventType ": 0," Category ": 5," Data ": {} ," MsgId":null,"ExceptionType":"System. IO. FileNotFoundException "," source ":" Npgsql "," ślad stosu ":" "," InnerEventInfos ": []}]}] \</ErrorInfo> \</LogProperties> "
 
Aby uzyskać więcej informacji na temat GAC, zobacz [Global Assembly Cache](/dotnet/framework/app-domains/gac).


### <a name="self-hosted-integration-runtime-authentication-key-is-missing"></a>Brak klucza uwierzytelniania środowiska Integration Runtime (samodzielnego)

#### <a name="symptoms"></a>Objawy

Własne środowisko Integration Runtime nagle przejdzie w tryb offline bez klucza uwierzytelniania, a w dzienniku zdarzeń zostanie wyświetlony następujący komunikat o błędzie: 

"Nie przypisano jeszcze klucza uwierzytelniania"

![Zrzut ekranu przedstawiający okienko zdarzenia środowiska Integration Runtime z informacją o tym, że klucz uwierzytelniania nie został jeszcze przypisany.](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Przyczyna

- Samodzielny węzeł IR lub logiczny samoobsługowy środowisko IR w Azure Portal został usunięty.
- Wykonano czystą dezinstalację.

#### <a name="resolution"></a>Rozwiązanie

Jeśli żaden z powyższych przyczyn nie ma zastosowania, możesz przejść do folderu *%ProgramData%\Microsoft\Data Transfer\DataManagementGateway* , aby sprawdzić, czy plik *konfiguracji* został usunięty. Jeśli został usunięty, postępuj zgodnie z instrukcjami w artykule Netwrix, aby [wykryć, kto usunął plik z serwerów plików systemu Windows](https://www.netwrix.com/how_to_detect_who_deleted_file.html).

![Zrzut ekranu przedstawiający okienko szczegółów dziennika zdarzeń służący do sprawdzania pliku konfiguracji.](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cant-use-self-hosted-ir-to-bridge-two-on-premises-datastores"></a>Nie można używać samodzielnego środowiska IR do mostkowania dwóch lokalnych magazynów danych

#### <a name="symptoms"></a>Objawy

Po utworzeniu autonomicznego urzędu skarbowego dla źródłowych i docelowych magazynów danych należy połączyć dwa urzędy skarbowe, aby zakończyć działanie kopiowania. Jeśli magazyny danych są skonfigurowane w różnych sieciach wirtualnych lub magazyny danych nie będą zrozumieć mechanizmu bramy, zostanie wyświetlony jeden z następujących błędów: 

* "Nie można odnaleźć sterownika źródła w docelowym środowisku IR"
* "Nie można uzyskać dostępu do źródła przez docelowy IR"
 
#### <a name="cause"></a>Przyczyna

Własne środowisko IR jest zaprojektowana jako centralny węzeł działania kopiowania, a nie agenta klienta, który należy zainstalować dla każdego magazynu danych.
 
W takim przypadku należy utworzyć połączoną usługę dla każdego magazynu danych za pomocą tego samego środowiska IR, a środowisko IR powinno mieć dostęp do magazynu danych w sieci. Nie ma znaczenia, czy środowisko IR jest zainstalowane w źródłowym magazynie danych, czy w docelowym magazynie danych lub na trzeciej maszynie. Jeśli dwie połączone usługi są tworzone przy użyciu innego urzędu skarbowego, ale są używane w tym samym działaniu kopiowania, używany jest docelowy port IR i należy zainstalować sterowniki dla obu magazynów danych na docelowej maszynie podczerwieni.

#### <a name="resolution"></a>Rozwiązanie

Zainstaluj sterowniki dla źródłowych i docelowych magazynów danych w docelowym środowisku IR i upewnij się, że można uzyskać dostęp do źródłowego magazynu danych.
 
Jeśli ruch nie może przechodzić przez sieć między dwoma magazynami danych (na przykład są one konfigurowane w dwóch sieciach wirtualnych), nie można zakończyć kopiowania w jednym działaniu nawet z zainstalowanym systemem IR. Jeśli nie można zakończyć kopiowania w pojedynczym działaniu, można utworzyć dwa działania kopiowania z dwoma urzędami skarbowymi, każdy w odpowietrzeniu: 
* Kopiuj jedno środowisko IR z magazynu danych 1 do platformy Azure Blob Storage
* Skopiuj inne środowisko IR z usługi Azure Blob Storage do ddatastore 2. 

To rozwiązanie może symulować wymaganie, aby przy użyciu środowiska IR utworzyć mostek łączący dwa rozłączone magazyny danych.


### <a name="credential-sync-issue-causes-credential-loss-from-ha"></a>Problem z synchronizacją poświadczeń powoduje utratę poświadczeń z HA

#### <a name="symptoms"></a>Objawy

Jeśli poświadczenie źródła danych "XXXXXXXXXX" zostanie usunięte z bieżącego węzła Integration Runtime z ładunkiem, zostanie wyświetlony następujący komunikat o błędzie:

"Po usunięciu usługi linku na Azure Portal lub zadaniu z nieprawidłowym ładunkiem należy ponownie utworzyć nową usługę linku przy użyciu poświadczeń".

#### <a name="cause"></a>Przyczyna

Własne środowisko IR jest zbudowane w trybie HA z dwoma węzłami, ale węzły nie są w stanie synchronizacji poświadczeń. Oznacza to, że poświadczenia przechowywane w węźle dyspozytora nie są synchronizowane z innymi węzłami procesu roboczego. Jeśli dojdzie do trybu failover z węzła dyspozytora do węzła procesu roboczego, a poświadczenia istnieją tylko w poprzednim węźle dyspozytora, zadanie zakończy się niepowodzeniem podczas próby uzyskania dostępu do poświadczeń i zostanie wyświetlony poprzedni błąd.

#### <a name="resolution"></a>Rozwiązanie

Jedynym sposobem uniknięcia tego problemu jest upewnienie się, że dwa węzły są w stanie synchronizacji poświadczeń. Jeśli nie są zsynchronizowane, należy ponownie wprowadzić poświadczenia dla nowego dyspozytora.


### <a name="cant-choose-the-certificate-because-the-private-key-is-missing"></a>Nie można wybrać certyfikatu, ponieważ brakuje klucza prywatnego

#### <a name="symptoms"></a>Objawy

* Zaimportowano plik PFX do magazynu certyfikatów.
* Po wybraniu certyfikatu za pomocą interfejsu użytkownika Configuration Manager IR zostanie wyświetlony następujący komunikat o błędzie:

   "Nie można zmienić trybu szyfrowania komunikacji intranetowej. Prawdopodobnie certyfikat " \<*certificate name*> " może nie mieć klucza prywatnego umożliwiającego wymianę klucza lub proces może nie mieć praw dostępu do klucza prywatnego. Aby uzyskać szczegółowe informacje, zobacz wyjątek wewnętrzny.

    ![Zrzut ekranu przedstawiający okienko ustawienia Configuration Manager Integration Runtime, w którym jest wyświetlany komunikat o błędzie "Brak klucza prywatnego".](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Przyczyna

- Konto użytkownika ma niski poziom uprawnień i nie może uzyskać dostępu do klucza prywatnego.
- Certyfikat został wygenerowany jako sygnatura, ale nie jako wymiana klucza.

#### <a name="resolution"></a>Rozwiązanie

* Aby obsługiwać interfejs użytkownika, należy użyć konta z odpowiednimi uprawnieniami do uzyskiwania dostępu do klucza prywatnego.  
* Zaimportuj certyfikat, uruchamiając następujące polecenie:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```

## <a name="self-hosted-ir-setup"></a>Konfiguracja samodzielnego środowiska IR

### <a name="integration-runtime-registration-error"></a>Błąd rejestracji środowiska Integration Runtime 

#### <a name="symptoms"></a>Objawy

Czasami może być konieczne uruchomienie własnego środowiska IR na innym koncie z jednego z następujących powodów:
- Zasady firmowe nie zezwalają na konto usługi.
- Wymagane jest pewne uwierzytelnianie.

Po zmianie konta usługi w okienku usługi może się okazać, że środowisko Integration Runtime przestanie działać i zostanie wyświetlony następujący komunikat o błędzie:

"Węzeł Integration Runtime (Self-hosted) napotkał błąd podczas rejestracji. Nie można nawiązać połączenia z usługą hosta Integration Runtime (Self-hosted) ".

![Zrzut ekranu okna Configuration Manager Integration Runtime, w którym jest wyświetlany błąd rejestracji w podczerwieni.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Przyczyna

Wiele zasobów jest przyznanych tylko do konta usługi. Jeśli zmienisz konto usługi na inne konto, uprawnienia wszystkich zależnych zasobów pozostaną niezmienione.

#### <a name="resolution"></a>Rozwiązanie

Aby sprawdzić błąd, przejdź do dziennika zdarzeń środowiska Integration Runtime.

![Zrzut ekranu przedstawiający dziennik zdarzeń podczerwieni pokazujący, że wystąpił błąd w czasie wykonywania.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

* Jeśli błąd w dzienniku zdarzeń to "UnauthorizedAccessException", wykonaj następujące czynności:

    1. Sprawdź konto usługi logowania *DIAHostService* w panelu usługi systemu Windows.

        ![Zrzut ekranu przedstawiający okienko właściwości konta usługi logowania.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Sprawdź, czy konto usługi logowania ma uprawnienia do odczytu/zapisu dla folderu *%ProgramData%\Microsoft\DataTransfer\DataManagementGateway* .

        - Domyślnie, jeśli konto logowania do usługi nie zostało zmienione, powinno mieć uprawnienia do odczytu/zapisu.

            ![Zrzut ekranu przedstawiający okienko uprawnienia usługi.](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

        - Jeśli zmieniono konto logowania do usługi, należy rozwiązać problem, wykonując następujące czynności:
 
            a. Wykonaj czystą dezinstalację bieżącego samodzielnego środowiska IR.   
            b. Zainstaluj własne bity IR.  
            c. Zmień konto usługi, wykonując następujące czynności:  

             i. Przejdź do folderu instalacji samoobsługowego środowiska IR, a następnie przejdź do folderu *Runtime\4.0\Shared integracji firmy Microsoft* .  
             ii. Otwórz okno wiersza polecenia, używając podniesionych uprawnień. Zastąp *\<user>* *\<password>* wartość i własną nazwą użytkownika i hasłem, a następnie uruchom następujące polecenie:   
                `dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"`  
             iii. Jeśli chcesz zmienić konto LocalSystem, upewnij się, że używasz poprawnego formatu dla tego konta: `dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""`  
                *Nie* używaj tego formatu:`dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""`     
             iv. Opcjonalnie, ponieważ system lokalny ma wyższe uprawnienia niż administrator, możesz również zmienić go bezpośrednio w "usługach".  
             v. Możesz użyć użytkownika lokalnego/domeny dla konta logowania usługi IR.            

            d. Zarejestruj środowisko Integration Runtime.

* Jeśli błąd to "usługa" Integration Runtime usługa "(DIAHostService) nie została uruchomiona. Sprawdź, czy masz wystarczające uprawnienia do uruchamiania usług systemowych, "wykonaj następujące czynności:

    1. Sprawdź konto usługi logowania *DIAHostService* w panelu usługi systemu Windows.
    
        ![Zrzut ekranu przedstawiający okienko "Logowanie" dla konta usługi.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Sprawdź, czy konto usługi logowania ma uprawnienie **Logowanie jako usługa** , aby uruchomić usługę systemu Windows:

        ![Zrzut ekranu przedstawiający okienko właściwości "Logowanie jako usługa".](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Więcej informacji

Jeśli żaden z powyższych dwóch wzorców rozpoznawania nie ma zastosowania w Twoim przypadku, spróbuj zebrać następujące dzienniki zdarzeń systemu Windows: 
- Dzienniki aplikacji i usług > Integration Runtime
- Dzienniki systemu Windows > aplikacji

### <a name="cant-find-the-register-button-to-register-a-self-hosted-ir"></a>Nie można znaleźć przycisku Register w celu zarejestrowania własnego środowiska IR    

#### <a name="symptoms"></a>Objawy

Po zarejestrowaniu samodzielnego środowiska IR przycisk **register** nie jest wyświetlany w okienku Configuration Manager.

![Zrzut ekranu przedstawiający okienko Configuration Manager, w którym jest wyświetlany komunikat informujący o tym, że węzeł Integration Runtime nie jest zarejestrowany.](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Przyczyna

Począwszy od wersji Integration Runtime 3,0, przycisk **register** w istniejących węzłach Integration Runtime został usunięty, aby umożliwić bardziej przejrzyste i bezpieczniejsze środowisko. Jeśli węzeł został zarejestrowany w środowisku Integration Runtime, niezależnie od tego, czy jest on w trybie online, czy nie, zarejestruj go ponownie w innym środowisku Integration Runtime, odinstalując poprzedni węzeł, a następnie zainstaluj i zarejestruj węzeł.

#### <a name="resolution"></a>Rozwiązanie

1. W panelu sterowania Odinstaluj istniejący produkt Integration Runtime.

    > [!IMPORTANT] 
    > W poniższym procesie wybierz pozycję **tak**. Nie należy przechowywać danych podczas procesu dezinstalacji.

    ![Zrzut ekranu przedstawiający przycisk "tak" służący do usuwania wszystkich danych użytkownika z środowiska Integration Runtime.](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Jeśli nie masz pliku MSI instalatora programu Integration Runtime, przejdź do [Centrum pobierania](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) , aby pobrać najnowszą wersję środowiska Integration Runtime.
1. Zainstaluj plik MSI i zarejestruj środowisko Integration Runtime.


### <a name="unable-to-register-the-self-hosted-ir-because-of-localhost"></a>Nie można zarejestrować własnego środowiska IR z powodu hosta lokalnego    

#### <a name="symptoms"></a>Objawy

Nie można zarejestrować własnego środowiska IR na nowym komputerze, gdy używasz get_LoopbackIpOrName.

**Debuguj:** Wystąpił błąd w czasie wykonywania.
Inicjator typów zgłosił wyjątek dla typu „Microsoft.DataTransfer.DIAgentHost.DataSourceCache”.
Wystąpił nieodwracalny błąd podczas wyszukiwania w bazie danych.
 
**Szczegóły wyjątku:** System. TypeInitializationException: inicjator typu dla elementu "Microsoft. DataTransfer. DIAgentHost. DataSourceCache" wywołał wyjątek. ---> system .NET. Sockets. SocketException: Wystąpił nieodwracalny błąd podczas wyszukiwania bazy danych w systemie .NET. DNS. GetAddrInfo (Nazwa ciągu).

#### <a name="cause"></a>Przyczyna

Problem zwykle występuje, gdy jest rozpoznawany localhost.

#### <a name="resolution"></a>Rozwiązanie

Użyj adresu IP hosta lokalnego 127.0.0.1, aby hostować plik i rozwiązać problem.

### <a name="self-hosted-setup-failed"></a>Konfiguracja samodzielna nie powiodła się    

#### <a name="symptoms"></a>Objawy

Nie można odinstalować istniejącego środowiska IR, zainstalować nowego środowiska IR lub uaktualnić istniejącego środowiska IR do nowego środowiska IR.

#### <a name="cause"></a>Przyczyna

Instalacja środowiska Integration Runtime zależy od usługi Instalator Windows. Problemy z instalacją mogą wystąpić z następujących powodów:
- Za mało dostępnego miejsca na dysku.
- Brak uprawnień.
- Usługa systemu Windows NT jest zablokowana.
- Użycie procesora CPU jest zbyt wysokie.
- Plik MSI jest hostowany w wolnej lokalizacji sieciowej.
- Niektóre pliki systemowe lub rejestry zostały przypadkowo naruszone.

### <a name="the-ir-service-account-failed-to-fetch-certificate-access"></a>Nie można pobrać dostępu do certyfikatu dla konta usługi IR

#### <a name="symptoms"></a>Objawy

W przypadku instalowania samodzielnego środowiska IR za pośrednictwem Microsoft Integration Runtime Configuration Manager zostanie wygenerowany certyfikat z zaufanym urzędem certyfikacji. Nie można zastosować certyfikatu do szyfrowania komunikacji między dwoma węzłami i zostanie wyświetlony następujący komunikat o błędzie: 

"Nie można zmienić trybu szyfrowania komunikacji intranetowej: nie powiodło się przyznanie kontu usługi Integration Runtime dostępu do certyfikatu" \<*certificate name*> ". Kod błędu 103 "

![Zrzut ekranu przedstawiający komunikat o błędzie "... Nie można udzielić dostępu do certyfikatu konta usługi Integration Runtime ".](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>Przyczyna

Certyfikat korzysta z magazynu usługi Key Storage Provider (dostawcy magazynu kluczy), który nie jest jeszcze obsługiwany. Na bieżąco środowisko IR obsługiwane przez funkcję samoobsługowego obsługuje tylko magazyn usług kryptograficznych (CSP).

#### <a name="resolution"></a>Rozwiązanie

Zalecamy używanie certyfikatów CSP w tym przypadku.

**Rozwiązanie 1** 

Aby zaimportować certyfikat, uruchom następujące polecenie:

`Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx`

![Zrzut ekranu przedstawiający polecenie certutil do importowania certyfikatu.](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**Rozwiązanie 2** 

Aby przekonwertować certyfikat, uruchom następujące polecenia:

`openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: <EnterPassword>`
`openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx`

Przed i po konwersji:

![Zrzut ekranu przedstawiający wynik przed konwersją certyfikatu.](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![Zrzut ekranu przedstawiający wynik po konwersji certyfikatu.](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>Samodzielne środowisko Integration Runtime w wersji 5. x
W przypadku uaktualnienia do wersji 5. x Azure Data Factory samodzielnego środowiska Integration Runtime wymagana jest **.NET Framework środowiska uruchomieniowego 4.7.2** lub nowszego. Na stronie pobierania znajdziesz linki do pobrania dla najnowszej wersji 4. x i najnowsze dwie wersje 5. x. 

W przypadku klientów z Azure Data Factory v2:
- Jeśli aktualizacja automatyczna jest włączona i uaktualniono już środowisko uruchomieniowe .NET Framework do 4.7.2 lub nowszego, środowisko Integration Runtime (własne) zostanie automatycznie uaktualnione do najnowszej wersji 5. x.
- Jeśli aktualizacja automatyczna jest włączona i nie uaktualniono środowiska uruchomieniowego .NET Framework do 4.7.2 lub nowszego, środowisko Integration Runtime (własne) nie zostanie automatycznie uaktualnione do najnowszej wersji 5. x. Własne środowisko Integration Runtime pozostanie w bieżącej wersji 4. x. Można wyświetlić ostrzeżenie dotyczące uaktualnienia środowiska uruchomieniowego .NET Framework w portalu oraz na własnym kliencie środowiska Integration Runtime.
- Jeśli aktualizacja automatyczna jest wyłączona i uaktualniono już środowisko uruchomieniowe .NET Framework do 4.7.2 lub nowszego, można ręcznie pobrać najnowszą wersję 5. x i zainstalować ją na maszynie.
- Jeśli aktualizacja automatyczna jest wyłączona i nie uaktualniono środowiska uruchomieniowego .NET Framework do 4.7.2 lub nowszego. Gdy próbujesz ręcznie zainstalować środowisko Integration Runtime 5. x i zarejestrować klucz, musisz najpierw uaktualnić wersję środowiska uruchomieniowego .NET Framework.


W przypadku klientów z Azure Data Factory V1:
- Własne środowisko Integration Runtime 5. X nie obsługuje Azure Data Factory v1.
- Własne środowisko Integration Runtime zostanie automatycznie uaktualnione do najnowszej wersji 4. x. I Najnowsza wersja 4. x nie wygaśnie. 
- Jeśli spróbujesz ręcznie zainstalować środowisko Integration Runtime 5. x i zarejestrować klucz, otrzymasz powiadomienie, że własne środowisko Integration Runtime 5. x nie obsługuje Azure Data Factory v1.


## <a name="self-hosted-ir-connectivity-issues"></a>Własne problemy z łącznością IR

### <a name="self-hosted-integration-runtime-cant-connect-to-the-cloud-service"></a>Własne środowisko Integration Runtime nie może nawiązać połączenia z usługą w chmurze

#### <a name="symptoms"></a>Objawy

Podczas próby zarejestrowania własnego środowiska Integration Runtime, Configuration Manager wyświetla następujący komunikat o błędzie:

"Węzeł Integration Runtime (Self-hosted) napotkał błąd podczas rejestracji".

![Zrzut ekranu przedstawiający komunikat "węzeł Integration Runtime (Self-hosted) napotkał błąd podczas rejestracji".](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Przyczyna 

Samoobsługowe środowisko IR nie może połączyć się z zapleczem usługi Azure Data Factory. Ten problem jest zwykle spowodowany przez ustawienia sieci w zaporze.

#### <a name="resolution"></a>Rozwiązanie

1. Sprawdź, czy usługa Integration Runtime jest uruchomiona. Jeśli tak jest, przejdź do kroku 2.
    
   ![Zrzut ekranu przedstawiający, że uruchomiono samoobsługowe usługi IR.](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Jeśli żaden serwer proxy nie jest skonfigurowany na własnym hostowanym środowisku IR, co jest ustawieniem domyślnym, uruchom następujące polecenie programu PowerShell na komputerze, na którym zainstalowano własne środowisko Integration Runtime:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > Adres URL usługi może się różnić w zależności od lokalizacji wystąpienia usługi Data Factory. Aby znaleźć adres URL usługi, wybierz pozycję połączenia **interfejsu użytkownika funkcji ADF**  >    >  **środowisko Integration Runtimes**  >  **Edycja samohostowanych**  >  **węzłów** IR  >  **przeglądanie adresów URL usług**.
            
    Oczekiwana jest następująca odpowiedź:
            
    ![Zrzut ekranu przedstawiający odpowiedź polecenia programu PowerShell.](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Jeśli nie otrzymasz oczekiwanej odpowiedzi, użyj jednej z następujących metod, zgodnie z potrzebami:
            
    * Jeśli zostanie wyświetlony komunikat "nie można rozpoznać nazwy zdalnej", występuje problem z systemem nazw domen (DNS). Skontaktuj się z zespołem sieci, aby rozwiązać ten problem.
    * Jeśli zostanie wyświetlony komunikat "certyfikat SSL/TLS nie jest zaufany", [Sprawdź certyfikat](https://wu2.frontend.clouddatahub.net/) , aby sprawdzić, czy jest on zaufany na maszynie, a następnie Zainstaluj certyfikat publiczny za pomocą Menedżera certyfikatów. Ta akcja powinna wyeliminować problem.
    * Przejdź do pozycji Podgląd zdarzeń **systemu Windows**  >  **(dzienniki)**  >  **Dzienniki aplikacji i usług**  >  **Integration Runtime** i sprawdź, czy wystąpił błąd spowodowany przez system DNS, regułę zapory lub ustawienia sieci firmowej. W przypadku wystąpienia takiego błędu należy wymusić zamknięcie połączenia. Ponieważ każda firma ma własne dostosowane ustawienia sieci, należy skontaktować się z zespołem sieci, aby rozwiązać te problemy.

1. Jeśli "serwer proxy" został skonfigurowany w ramach własnego środowiska Integration Runtime, sprawdź, czy serwer proxy ma dostęp do punktu końcowego usługi. Przykładowe polecenie można znaleźć w temacie [PowerShell, żądania sieci Web i serwery proxy](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

Oczekiwana jest następująca odpowiedź:
            
![Zrzut ekranu przedstawiający oczekiwaną odpowiedź polecenia programu PowerShell.](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Zagadnienia dotyczące serwerów proxy:
> * Sprawdź, czy serwer proxy należy umieścić na liście bezpiecznych adresatów. Jeśli tak, upewnij się, że [te domeny](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) znajdują się na liście bezpiecznych adresatów.
> * Sprawdź, czy certyfikat SSL/TLS "wu2.frontend.clouddatahub.net/" jest zaufany na serwerze proxy.
> * Jeśli używasz uwierzytelniania Active Directory na serwerze proxy, Zmień konto usługi na konto użytkownika, które ma dostęp do serwera proxy jako "usługa Integration Runtime".

### <a name="error-message-self-hosted-integration-runtime-nodelogical-self-hosted-ir-is-in-inactive-running-limited-state"></a>Komunikat o błędzie: samohostowany węzeł Integration Runtime lub logiczny samoobsługowy środowisko IR jest w stanie nieaktywnym/"uruchomionym (ograniczonym)"

#### <a name="cause"></a>Przyczyna 

Samoobsługowy, zintegrowany węzeł środowiska uruchomieniowego może mieć stan **nieaktywny**, jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający własny węzeł zintegrowanego środowiska uruchomieniowego z nieaktywnym stanem](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

To zachowanie występuje, gdy węzły nie mogą komunikować się ze sobą.

#### <a name="resolution"></a>Rozwiązanie

1. Zaloguj się do maszyny wirtualnej hostowanej w węźle. W obszarze **Dzienniki aplikacji i usług**  >  **Integration Runtime** Otwórz Podgląd zdarzeń i przefiltruj dzienniki błędów.

1. Sprawdź, czy dziennik błędów zawiera następujący błąd: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Jeśli ten błąd jest wyświetlany, uruchom następujące polecenie w oknie wiersza polecenia: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Jeśli zostanie wyświetlony komunikat o błędzie "nie można otworzyć połączenia z hostem", który jest wyświetlany na poniższym zrzucie ekranu, skontaktuj się z działem IT w celu uzyskania pomocy w celu rozwiązania tego problemu. Po pomyślnym zalogowaniu się w programie Telnet skontaktuj się z firmą pomoc techniczna firmy Microsoft, jeśli nadal występują problemy ze stanem węzła Integration Runtime.
        
   ![Zrzut ekranu przedstawiający błąd wiersza polecenia "nie można otworzyć połączenia z hostem".](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Sprawdź, czy dziennik błędów zawiera następujący wpis:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Aby rozwiązać ten problem, wypróbuj jedną lub obie następujące metody:
    - Umieść wszystkie węzły w tej samej domenie.
    - Dodaj adres IP do mapowania hosta we wszystkich plikach hosta hostowanej maszyny wirtualnej.

### <a name="connectivity-issue-between-the-self-hosted-ir-and-your-data-factory-instance-or-the-self-hosted-ir-and-the-data-source-or-sink"></a>Problem z łącznością między własnym hostowanym środowiskiem IR a wystąpieniem fabryki danych lub własnym środowiskiem IR oraz źródłem danych lub obiektem sink

Aby rozwiązać problem z łącznością sieciową, należy wiedzieć, jak zbierać dane śledzenia sieci, zrozumieć, jak z niej korzystać i [analizować śledzenie Microsoft Network Monitor (netmon)](#analyze-the-netmon-trace) przed zastosowaniem narzędzi netmon w rzeczywistych przypadkach z własnego środowiska IR.

#### <a name="symptoms"></a>Objawy

Czasami może być konieczne rozwiązywanie problemów z łącznością między własnym hostowanym środowiskiem IR a wystąpieniem usługi Data Factory, jak pokazano na poniższym zrzucie ekranu lub między własnym hostowanym środowiskiem IR a źródłem danych lub ujściam. 

![Zrzut ekranu przedstawiający komunikat "przetworzone żądanie HTTP nie powiodło się"](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

W każdym z tych wystąpień mogą wystąpić następujące błędy:

* "Kopiowanie nie powiodło się z powodu błędu: Type = Microsoft. DataTransfer. Common. Shared. HybridDeliveryException, Message = nie można nawiązać połączenia z SQL Server:" adres IP ""

* "Wystąpił co najmniej jeden błąd. Wystąpił błąd podczas wysyłania żądania. Połączenie podstawowe zostało zamknięte: Wystąpił nieoczekiwany błąd podczas odbierania. Nie można odczytać danych z połączenia transportowego: wykryto, że istniejące połączenie zostało wymuszone przez hosta zdalnego. Istniejące połączenie zostało wymuszenie zamknięte przez identyfikator działania hosta zdalnego ".

#### <a name="resolution"></a>Rozwiązanie

W przypadku wystąpienia powyższych błędów należy rozwiązać problemy, postępując zgodnie z instrukcjami w tej sekcji.

- Zbierz śledzenie netmon na potrzeby analizy: 

    1. Można ustawić filtr, aby wyświetlić Reset z serwera po stronie klienta. Na poniższym przykładowym zrzucie ekranu można zobaczyć, że serwer jest serwerem Data Factory.

        ![Zrzut ekranu przedstawiający serwer fabryki danych.](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

    1. Po otrzymaniu pakietu resetowania można znaleźć konwersację, wykonując następujące Transmission Control Protocol (TCP).

        ![Zrzut ekranu przedstawiający konwersację TCP.](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

    1. Aby uzyskać konwersację między klientem a serwerem Data Factory poniżej, należy usunąć filtr.

        ![Zrzut ekranu przedstawiający szczegóły konwersacji.](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)

- Analiza zebranych śladów netmon pokazuje, że czas wygaśnięcia (TTL)) łącznie wynosi 64. Zgodnie z wartościami wymienionymi w artykule [czas wygaśnięcia IP (TTL) i limit przeskoków](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) , wyodrębnionych z poniższej listy, można zobaczyć, że jest to system Linux, który resetuje pakiet i powoduje odłączenie.

    Domyślne wartości czasu wygaśnięcia i przeskoków różnią się między różnymi systemami operacyjnymi, jak pokazano poniżej:
    - Jądro systemu Linux 2,4 (około 2001): 255 dla protokołów TCP, User Datagram Protocol (UDP) i ICMP (Internet Control Message Protocol)
    - Jądro systemu Linux 4,10 (2015): 64 dla protokołów TCP, UDP i ICMP
    - Windows XP (2001): 128 dla protokołów TCP, UDP i ICMP
    - Windows 10 (2015): 128 dla protokołów TCP, UDP i ICMP
    - Windows Server 2008:128 dla protokołów TCP, UDP i ICMP
    - Windows Server 2019 (2018): 128 dla protokołów TCP, UDP i ICMP
    - macOS (2001): 64 dla protokołów TCP, UDP i ICMP

    ![Zrzut ekranu przedstawiający wartość czasu wygaśnięcia 61.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    W poprzednim przykładzie czas TTL jest pokazywany jako 61 zamiast 64, ponieważ gdy pakiet sieciowy osiągnie miejsce docelowe, musi przekroczyć różne przeskoki, takie jak routery lub urządzenia sieciowe. Liczba routerów lub urządzeń sieciowych jest potrącana w celu utworzenia końcowego czasu wygaśnięcia.
    
    W takim przypadku można zobaczyć, że Resetowanie może być wysyłane z systemu Linux z wartością TTL 64.

-  Aby upewnić się, że urządzenie reset może pochodzić z programu, sprawdź czwarty przeskok z samoobsługowego środowiska IR.
 
    *Pakiet sieciowy z systemu Linux system A z parametrem TTL 64-> B TTL 64 minus 1 = 63-> C TTL 63 minus 1 = 62-> TTL 62 minus 1 = 61 własne środowisko IR*

- W idealnej sytuacji liczba przeskoków czasu wygaśnięcia wynosi 128, co oznacza, że system operacyjny Windows uruchamia wystąpienie fabryki danych. Jak pokazano w poniższym przykładzie, *128 minus 107 = 21 przeskoków*, co oznacza, że 21 przeskoków dla pakietu zostały wysłane z wystąpienia fabryki danych do samodzielnego środowiska IR podczas uzgadniania TCP 3.
 
    ![Zrzut ekranu przedstawiający wartość czasu wygaśnięcia 107.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    W związku z tym należy skontaktować się z zespołem sieci, aby sprawdzić, co czwarty przeskok pochodzi z samodzielnego środowiska IR. Jeśli jest to zapora, tak jak w systemie Linux, sprawdź wszystkie dzienniki, aby zobaczyć, dlaczego urządzenie resetuje pakiet po uzgodnieniu protokołu TCP 3. 
    
    Jeśli nie masz pewności, gdzie należy zbadać, spróbuj uzyskać ślad netmon z własnego środowiska IR i zapory w trakcie problematycznego czasu. Takie podejście pomoże Ci ustalić, które urządzenie mogło zresetować pakiet i spowodowało odłączenie. W takim przypadku należy również skontaktować się z zespołem sieci, aby przejść do przodu.

### <a name="analyze-the-netmon-trace"></a>Analizowanie śladu netmon

> [!NOTE] 
> Poniższe instrukcje dotyczą śledzenia netmon. Ponieważ śledzenie netmon jest obecnie poza wsparciem, możesz użyć programu Wireshark do tego celu.

Podczas próby wykonania w programie Telnet **8.8.8.8 888** z zebranym śledzeniem netmon powinien zostać wyświetlony następujący ślad dotyczący następujących zrzutów ekranu:

![Zrzut ekranu przedstawiający komunikat o błędzie "nie można otworzyć połączenia z hostem na porcie 888".](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Zrzut ekranu przedstawiający opis śladu netmon.](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Powyższe obrazy pokazują, że nie udało się nawiązać połączenia TCP z serwerem **8.8.8.8** na porcie **888**, dlatego w tym miejscu widoczne są dwa **SynReTransmit** dodatkowe pakiety. Ze względu na to, że źródło **host2** nie może nawiązać połączenia z **8.8.8.8** z pierwszym pakietem, podejmie próbę nawiązania połączenia.

> [!TIP]
> Aby nawiązać to połączenie, wypróbuj następujące rozwiązanie:
> 1. Wybierz pozycję Filtr **obciążenia** filtr  >  **Standardowy**  >  **adresy**  >  **IPv4**.
> 1. Aby zastosować filtr, wprowadź wartość **IPv4. Address = = 8.8.8.8**, a następnie wybierz pozycję **Zastosuj**. Następnie powinna zostać wyświetlona komunikacja z komputera lokalnego do lokalizacji docelowej **8.8.8.8**.

![Zrzut ekranu przedstawiający adresy filtrów.](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![Zrzut ekranu przedstawiający więcej adresów filtrów.](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Scenariusze zakończone powodzeniem przedstawiono w następujących przykładach: 

- Jeśli możesz Telnet **8.8.8.8 53** bez żadnych problemów, istnieje pomyślne uzgadnianie TCP 3, a sesja kończy się z uzgadnianiem TCP 4.

    ![Zrzut ekranu przedstawiający scenariusz pomyślnego nawiązania połączenia.](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![Zrzut ekranu przedstawiający szczegółowe informacje o pomyślnym scenariuszu połączenia.](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- Powyższe uzgadnianie TCP 3 generuje następujący przepływ pracy:

    ![Diagram przepływu pracy uzgadniania TCP 3.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- Uzgadnianie protokołu TCP 4 do zakończenia sesji jest zilustrowane przez następujące przepływy pracy:

    ![Zrzut ekranu przedstawiający Szczegóły uzgadniania TCP 4.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Diagram przepływu pracy uzgadniania TCP 4.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 

### <a name="microsoft-email-notification-about-updating-your-network-configuration"></a>Powiadomienia e-mail firmy Microsoft dotyczące aktualizowania konfiguracji sieci

Może zostać wyświetlone następujące powiadomienie e-mail, które zaleca zaktualizowanie konfiguracji sieci w celu umożliwienia komunikacji z nowymi adresami IP dla Azure Data Factory do 8 listopada 2020:

   ![Zrzut ekranu przedstawiający aktualizację konfiguracji sieci z powiadomieniem e-mail firmy Microsoft.](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="determine-whether-this-notification-affects-you"></a>Określanie, czy to powiadomienie dotyczy

To powiadomienie ma zastosowanie do następujących scenariuszy:

##### <a name="scenario-1-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-premises-behind-a-corporate-firewall"></a>Scenariusz 1: komunikacja wychodząca z samodzielnego środowiska Integration Runtime, która jest uruchamiana lokalnie za zaporą firmową

Jak określić, czy ma to wpływ:

- *Nie* ma to żadnego oddziaływania, jeśli definiujesz reguły zapory na podstawie w pełni kwalifikowanych nazw domen (FQDN) korzystających z metody opisanej w temacie [Konfigurowanie konfiguracji zapory i listy dozwolonych adresów IP](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway).

- Ma *to zastosowanie* , jeśli jawnie włączysz listę dozwolonych wychodzących adresów IP w zaporze firmowej.

   Jeśli ma to wpływ, wykonaj następujące czynności: do 8 listopada 2020, powiadom zespół infrastruktury sieciowej o zaktualizowaniu konfiguracji sieci tak, aby korzystała z najnowszych adresów IP fabryki danych. Aby pobrać najnowsze adresy IP, przejdź do obszaru [odnajdywanie tagów usługi przy użyciu plików JSON do pobrania](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-2-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-an-azure-vm-inside-a-customer-managed-azure-virtual-network"></a>Scenariusz 2: komunikacja wychodząca z własnego środowiska Integration Runtime uruchomionego na maszynie wirtualnej platformy Azure wewnątrz sieci wirtualnej platformy Azure zarządzanej przez klienta

Jak określić, czy ma to wpływ:

- Sprawdź, czy w sieci prywatnej zawierającej środowisko Integration Runtime są dostępne reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) dla ruchu wychodzącego. W przypadku braku ograniczeń wychodzących nie ma to wpływu.

- Jeśli masz ograniczenia reguły ruchu wychodzącego, sprawdź, czy używasz tagów usługi. Jeśli używasz tagów usługi, nie ma to żadnego oddziaływania. Nie trzeba zmieniać ani dodawać żadnych elementów, ponieważ nowy zakres adresów IP znajduje się w istniejących tagach usługi. 

  ![Zrzut ekranu przedstawiający Sprawdzanie lokalizacji docelowej pokazujący fabrykę jako lokalizację docelową.](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)

- Ma *to zastosowanie* , jeśli jawnie włączysz listę dozwolonych adresów IP w ustawieniach reguł sieciowej grupy zabezpieczeń w sieci wirtualnej platformy Azure.

   Jeśli ma to wpływ, wykonaj następujące czynności: do 8 listopada 2020, powiadom zespół infrastruktury sieciowej o zaktualizowaniu reguł sieciowej grupy zabezpieczeń w konfiguracji sieci wirtualnej platformy Azure, aby użyć najnowszych adresów IP fabryki danych. Aby pobrać najnowsze adresy IP, przejdź do obszaru [odnajdywanie tagów usługi przy użyciu plików JSON do pobrania](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-a-customer-managed-azure-virtual-network"></a>Scenariusz 3: komunikacja wychodząca z usług SSIS Integration Runtime w sieci wirtualnej platformy Azure zarządzanej przez klienta

Jak określić, czy ma to wpływ:

- Sprawdź, czy masz reguły sieciowej grupy zabezpieczeń ruchu wychodzącego w sieci prywatnej, która zawiera Integration Runtime SQL Server Integration Services (SSIS). W przypadku braku ograniczeń wychodzących nie ma to wpływu.

- Jeśli masz ograniczenia reguły ruchu wychodzącego, sprawdź, czy używasz tagów usługi. Jeśli używasz tagów usługi, nie ma to żadnego oddziaływania. Nie ma potrzeby zmiany ani dodawania wszystkiego, ponieważ nowy zakres adresów IP znajduje się w istniejących tagach usługi.

- Ma *to zastosowanie* , jeśli jawnie włączysz listę dozwolonych adresów IP w ustawieniach reguł sieciowej grupy zabezpieczeń w sieci wirtualnej platformy Azure.

  Jeśli ma to wpływ, wykonaj następujące czynności: do 8 listopada 2020, powiadom zespół infrastruktury sieciowej o zaktualizowaniu reguł sieciowej grupy zabezpieczeń w konfiguracji sieci wirtualnej platformy Azure, aby użyć najnowszych adresów IP fabryki danych. Aby pobrać najnowsze adresy IP, przejdź do obszaru [odnajdywanie tagów usługi przy użyciu plików JSON do pobrania](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

### <a name="couldnt-establish-a-trust-relationship-for-the-ssltls-secure-channel"></a>Nie można ustanowić relacji zaufania dla bezpiecznego kanału SSL/TLS 

#### <a name="symptoms"></a>Objawy

Samoobsługowe środowisko IR nie mogło nawiązać połączenia z usługą Azure Data Factory.

Podczas sprawdzania własnego dziennika zdarzeń IR lub dzienników powiadomień klienta w tabeli CustomLogEvent znajdziesz następujący komunikat o błędzie:

"Połączenie podstawowe zostało zamknięte: nie można ustanowić relacji zaufania dla bezpiecznego kanału SSL/TLS. Certyfikat zdalny jest nieprawidłowy zgodnie z procedurą walidacji ".

Najprostszym sposobem sprawdzenia certyfikatu serwera usługi Data Factory jest otwarcie adresu URL usługi Data Factory w przeglądarce. Na przykład Otwórz [łącze Sprawdź certyfikat serwera](https://eu.frontend.clouddatahub.net/) na komputerze, na którym zainstalowano samoobsługowe środowisko IR, a następnie Wyświetl informacje o certyfikacie serwera.

  ![Zrzut ekranu przedstawiający okienko Sprawdź certyfikat serwera w usłudze Azure Data Factory.](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Zrzut ekranu przedstawiający okno umożliwiające sprawdzenie ścieżki certyfikacji serwera.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Przyczyna

Istnieją dwie możliwe przyczyny tego problemu:

- Przyczyna 1: główny urząd certyfikacji certyfikatu serwera usługi Data Factory nie jest zaufany na komputerze, na którym zainstalowano samoobsługowe środowisko IR. 
- Powód 2: używasz serwera proxy w danym środowisku, certyfikat serwera usługi Data Factory jest zastępowany przez serwer proxy, a zastąpiony certyfikat serwera nie jest zaufany na komputerze, na którym zainstalowano samoobsługowe środowisko IR.

#### <a name="resolution"></a>Rozwiązanie

- Z przyczyn 1: Upewnij się, że certyfikat serwera Data Factory i jego łańcuch certyfikatów są zaufane na komputerze, na którym zainstalowano samoobsługowe środowisko IR.
- W przypadku przyczyny 2: Ufaj zamienionemu głównemu urzędowi certyfikacji na samohostowanej maszynie IR lub skonfiguruj serwer proxy, aby nie zastąpił certyfikat serwera Data Factory.

Aby uzyskać więcej informacji na temat zaufania certyfikatów w systemie Windows, zobacz [Instalowanie zaufanego certyfikatu głównego](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate).

#### <a name="additional-information"></a>Dodatkowe informacje
Wprowadziliśmy nowy certyfikat SSL, który jest podpisany z DigiCert. Sprawdź, czy globalny główny urząd certyfikacji DigiCert ma wartość w obszarze zaufany główny certyfikat.

  ![Zrzut ekranu przedstawiający folder DigiCert Global root w katalogu zaufanych głównych urzędów certyfikacji.](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

Jeśli nie znajduje się w zaufanym głównym urzędzie certyfikacji, [Pobierz ją tutaj](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ). 


## <a name="self-hosted-ir-sharing"></a>Udostępnianie własnego środowiska IR

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Udostępnianie samodzielnego środowiska IR z innej dzierżawy nie jest obsługiwane 

#### <a name="symptoms"></a>Objawy

Możesz zauważyć inne fabryki danych (w różnych dzierżawcach) podczas próby udostępnienia samodzielnego środowiska IR z poziomu interfejsu użytkownika Azure Data Factory, ale nie możesz go udostępnić między fabrykami danych, które znajdują się w różnych dzierżawach.

#### <a name="cause"></a>Przyczyna

Nie można współużytkować samodzielnego środowiska IR w dzierżawach.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pomoc dotyczącą rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&stronie](/answers/topics/azure-data-factory.html)
*  [Forum przepełnienia stosu dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Przewodnik wydajności dotyczący mapowania przepływów danych](concepts-data-flow-performance.md)