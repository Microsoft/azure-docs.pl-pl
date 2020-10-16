---
title: Rozwiązywanie problemów z własnym hostowanym środowiskiem Integration Runtime w Azure Data Factory
description: Dowiedz się, jak rozwiązywać problemy z nieobsługiwanym środowiskiem Integration Runtime w Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.author: abnarain
ms.openlocfilehash: f0957b74bf13acfcc80e38cccaec389fbbd19fa0
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131320"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Rozwiązywanie problemów z własnym hostowanym środowiskiem Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów z własnym hostowanym środowiskiem Integration Runtime w Azure Data Factory.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Zbieraj własne dzienniki środowiska IR z Azure Data Factory

W przypadku działań zakończonych niepowodzeniem w przypadku samodzielnego środowiska IR/udostępnionego środowiska IR Azure Data Factory obsługuje wyświetlanie i przekazywanie dzienników błędów. Aby uzyskać identyfikator raportu o błędach, należy wykonać poniższe czynności, a następnie wprowadzić identyfikator raportu w celu zlokalizowania powiązanych znanych problemów.

1. Przejdź do strony **uruchomienia działania** .

1. W kolumnie **błąd** kliknij przycisk poniżej.

    ![Strona uruchomienia działania](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. Zostaną wyświetlone powiązane dzienniki dla uruchomienia działania zakończonego niepowodzeniem. Kliknij przycisk **Wyślij dzienniki** , aby uzyskać dalszą pomoc.

    ![Wysyłanie dzienników](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. Możesz wybrać dzienniki, które chcesz wysłać. W przypadku *samodzielnego środowiska IR*można przekazać dzienniki powiązane z działaniem zakończonym niepowodzeniem lub wszystkie dzienniki w autonomicznym węźle IR. W przypadku *udostępnionego środowiska IR*można przekazać tylko dzienniki powiązane z działaniem zakończonym niepowodzeniem.

    ![Wybieranie dzienników](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Podczas przekazywania dzienników należy zachować rekord identyfikatora raportu, jeśli potrzebna jest dalsza pomoc w rozwiązaniu problemu.

    ![Przekaż dzienniki](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Przeglądanie dzienników i przekazywanie żądań zostanie wykonane we wszystkich wystąpieniach samoobsługowego środowiska IR w trybie online. Upewnij się, że wszystkie własne wystąpienia środowiska IR są w trybie online w przypadku brakujących dzienników. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Ogólny błąd lub awaria własnego środowiska IR

### <a name="tlsssl-certificate-issue"></a>Problem z certyfikatem TLS/SSL:

#### <a name="symptoms"></a>Objawy

Podczas próby włączenia certyfikatu TLS/SSL (zaawansowane) z poziomu ekranu **Menedżer konfiguracji własnego środowiska IR** -> **Dostęp zdalny z intranetu** po wybraniu certyfikatu TLS/SSL jest wyświetlany poniższy komunikat o błędzie:

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

W powyższym przypadku użytkownik korzysta z certyfikatu, w którym ostatni element to „microsoft.com”.

#### <a name="cause"></a>Przyczyna

Jest to znany problem w usłudze WCF: Podczas walidacji certyfikatu TLS/SSL w usłudze WCF sprawdzana jest tylko ostatnia nazwa DNSName w sieci SAN. 

#### <a name="resolution"></a>Rozwiązanie

Własne środowisko IR usługi Azure Data Factory v2 obsługuje certyfikat z symbolami wieloznacznymi. Ten problem występuje zwykle z powodu nieprawidłowego certyfikatu SSL. Ostatnia nazwa DNSName w sieci SAN powinna być prawidłowa. Wykonaj poniższe czynności, aby to sprawdzić. 
1.  Otwórz konsolę zarządzania, dokładnie sprawdź *podmiot* i *alternatywną nazwę podmiotu* w szczegółach certyfikatu. W powyższym przypadku na przykład ostatni element w *alternatywnej nazwie podmiotu*, czyli "DNS name = Microsoft.com.com", nie jest prawidłowy.
2.  Skontaktuj się z firmą wystawienia certyfikatu w celu usunięcia nieprawidłowej nazwy DNS.

### <a name="concurrent-jobs-limit-issue"></a>Problem z limitem zadań współbieżnych

#### <a name="symptoms"></a>Objawy

Przy próbie zwiększenia limitu zadań współbieżnych z poziomu interfejsu użytkownika usługi Azure Data Factory stan zawiesza się na wartości *trwa aktualizacja*.
Maksymalna wartość zadań współbieżnych została ustawiona na 24 i chcesz zwiększyć tę liczbę, aby umożliwić szybsze uruchamianie zadań. Minimalna wartość, którą można wprowadzić, to 3, a maksymalna wartość to 32. Zwiększono wartość z 24 do 32 i trafisz na przycisk *aktualizacji* w interfejsie użytkownika, który został zablokowany podczas *aktualizowania* , jak widać poniżej. Po odświeżeniu klient nadal widzi wartość 24, która nigdy nie została zaktualizowana do 32.

![Aktualizowanie stanu](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Przyczyna

To ustawienie jest ograniczone, ponieważ jego wartość zależy od rdzenia logicznego i pamięci komputera. Można ustawić mniejszą wartość, na przykład 24, i sprawdzić, jaki będzie wynik.

> [!TIP] 
> - Aby uzyskać szczegółowe informacje o tym, co to jest liczba rdzeni dla logiki, i jak znaleźć podstawową liczbę logiki maszyn, zobacz [ten artykuł](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> - Aby uzyskać szczegółowe informacje na temat sposobu obliczania zapisu Math. log, zobacz [ten artykuł](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>Problem z certyfikatem HA SSL własnego środowiska IR

#### <a name="symptoms"></a>Objawy

Węzeł roboczy własnego środowiska IR zgłosił poniższy błąd:

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>Przyczyna

Podczas obsługi przypadków związanych z uzgadnianiem certyfikatów SSL/TLS mogą wystąpić pewne problemy związane z weryfikacją łańcucha certyfikatów. 

#### <a name="resolution"></a>Rozwiązanie

- Oto szybki i intuicyjny sposób rozwiązywania problemów z kompilacją łańcucha certyfikatów X. 509.
 
    1. Wyeksportuj certyfikat, który ma zostać zweryfikowany. Przejdź do zarządzania certyfikatem komputera i znajdź certyfikat, który chcesz sprawdzić, a następnie kliknij prawym przyciskiem myszy pozycję **Wszystkie zadania** -> **Eksportuj**.
    
        ![Eksportuj zadania](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Skopiuj wyeksportowany certyfikat do komputera klienckiego. 
    3. Po stronie klienta uruchom poniższe polecenie w programie CMD. Upewnij się, że zamieniono poniżej *\<certificate path>* i *\<output txt file path>* symbole zastępcze ze ścieżkami pokrewnymi.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Na przykład:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Sprawdź, czy w wyjściowym pliku txt znajduje się jakikolwiek błąd. Podsumowanie błędów można znaleźć na końcu pliku txt.

        Na przykład: 

        ![Podsumowanie błędu](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Jeśli na końcu pliku dziennika nie ma żadnego błędu, jak pokazano poniżej, można rozważyć pomyślne utworzenie łańcucha certyfikatów na komputerze klienckim.
        
        ![Brak błędów w pliku dziennika](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- W przypadku wystąpienia AIA, CDP i OCSP skonfigurowanych w pliku certyfikatu. Możemy ją sprawdzać w bardziej intuicyjny sposób.
 
    1. Te informacje można uzyskać, sprawdzając szczegóły certyfikatu.
    
        ![Szczegóły certyfikatu](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. Uruchom poniższe polecenie. Upewnij się, że zamieniono *\<certificate path>* symbol zastępczy na ścieżkę powiązaną z certyfikatem.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. Następnie zostanie otwarte **narzędzie do pobierania adresu URL**. Aby sprawdzić certyfikaty z protokołów AIA, CDP i OCSP, kliknij przycisk **Pobierz**.

        ![Przycisk pobierania](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        Łańcuch certyfikatów może zostać pomyślnie utworzony, jeśli certyfikat z protokołu AIA ma wartość „Zweryfikowano” i certyfikat z protokołu CDP lub OCSP ma wartość „Zweryfikowano”.

        Jeśli wystąpi błąd podczas pobierania protokołu AIA lub CDP, skontaktuj się z zespołem ds. sieci, aby przygotować komputer kliencki do nawiązania połączenia z docelowym adresem URL. Wystarczy, że będzie można zweryfikować ścieżkę http lub ldap.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>Własne środowisko IR nie może załadować pliku lub zestawu

#### <a name="symptoms"></a>Objawy

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
Na przykład: 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>Przyczyna

Jeśli monitor zostanie przetworzony, zobaczysz następujący wynik:

[![Monitor procesu](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> Można ustawić filtr, jak pokazano na poniższym zrzucie ekranu.
> Informuje nas, że biblioteka DLL **System. ValueTuple** nie znajduje się w folderze związanym z pamięcią GAC lub w folderze *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway*, lub w *katalogu c:\Program Files\Microsoft Integration Runtime\4.0\Shared* .
> Zasadniczo załaduje on bibliotekę dll najpierw z folderu pamięci *GAC*, następnie z folderu *Shared* (Udostępnione), a na końcu z folderu *Gateway* (Brama). W związku z tym możesz umieścić bibliotekę dll w dowolnej ścieżce, która może być przydatna.

![Skonfiguruj filtry](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Rozwiązanie

Można sprawdzić, czy **System.ValueTuple.dll** znajduje się w folderze *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* . Aby rozwiązać ten problem, skopiuj **System.ValueTuple.dll** do folderu *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* .

Tej samej metody można użyć do rozwiązywania problemów z innymi brakującymi plikami lub zestawami.

#### <a name="more-information"></a>Więcej informacji

Powód, dla którego widzisz System.ValueTuple.dll w obszarze *%windir%\Microsoft.NET\assembly* i *%windir%\assembly* , jest to zachowanie platformy .NET. 

Na poniższym błędzie można jasno zobaczyć zestaw *System. ValueTuple* nie istnieje. Taki problem występuje, gdy aplikacja próbuje sprawdzić zestaw *System.ValueTuple.dll*.
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
Aby uzyskać więcej informacji na temat GAC, zobacz [ten artykuł](https://docs.microsoft.com/dotnet/framework/app-domains/gac).


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>Jak przeprowadzić inspekcję braku klucza własnego środowiska IR

#### <a name="symptoms"></a>Objawy

Własne środowisko Integration Runtime nagle przechodzi do trybu offline bez klucza. W dzienniku zdarzeń widoczny jest poniższy błąd: `Authentication Key is not assigned yet`

![Brak klucza uwierzytelniania](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Przyczyna

- Usunięto węzeł własnego środowiska IR lub logiczne własne środowisko IR w portalu.
- Wykonywana jest czysta dezinstalacja.

#### <a name="resolution"></a>Rozwiązanie

Jeśli żaden z powyższych przyczyn nie ma zastosowania, możesz przejść do folderu: *%ProgramData%\Microsoft\Data Transfer\DataManagementGateway*i sprawdzić, czy plik o nazwie **Configurations** został usunięty. Jeśli został on usunięty, postępuj zgodnie z instrukcjami dostępnymi [tutaj](https://www.netwrix.com/how_to_detect_who_deleted_file.html), aby sprawdzić, kto usunął plik.

![Sprawdź plik konfiguracji](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>Nie można użyć własnego środowiska IR w celu połączenia dwóch lokalnych magazynów danych

#### <a name="symptoms"></a>Objawy

Po utworzeniu własnych środowisk IR dla źródłowego i docelowego magazynu danych, chcesz połączyć te dwa środowiska IR, aby zakończyć kopię. Jeśli magazyny danych są skonfigurowane w różnych sieci wirtualnychach lub nie mogą zrozumieć mechanizmu bramy, wystąpią błędy, takie jak: *nie można znaleźć sterownika źródła w docelowym środowisku IR*; *nie można uzyskać dostępu do źródła za pomocą docelowego środowiska IR*.
 
#### <a name="cause"></a>Przyczyna

Własne środowisko IR działa jako centralny węzeł akcji kopiowania, a nie agent klienta, który musi zostać zainstalowany dla każdego magazynu danych.
 
W powyższym przypadku usługa połączona dla każdego magazynu danych powinna zostać utworzona przy użyciu tego samego środowiska IR, a środowisko IR powinno mieć dostęp do obydwu magazynów danych za pośrednictwem sieci. Niezależnie od tego, czy środowisko IR zostało zainstalowane ze źródłowym magazynem danych, docelowym magazynem danych, czy na oddzielnej maszynie, jeśli dwie usługi połączone zostały utworzone za pomocą innych środowisk IR, ale są używane w tej samej akcji kopiowania, zostanie użyte docelowe środowisko IR i na maszynie docelowego środowiska IR należy zainstalować sterowniki dla obydwu magazynów danych.

#### <a name="resolution"></a>Rozwiązanie

Zainstaluj sterowniki dla źródłowego i docelowego magazynu na maszynie docelowego środowiska IR i upewnij się, że ma ona dostęp do źródłowego magazynu danych.
 
Jeśli ruch nie może przejść przez sieć między dwoma magazynami danych (na przykład zostały one skonfigurowane w dwóch sieciach wirtualnych), nie można ukończyć kopiowania w jednej akcji, nawet z zainstalowanym środowiskiem IR. W takim przypadku można utworzyć dwie akcje kopiowania z dwoma środowiskami IR, z których każde znajduje się w sieci wirtualnej: Pierwsze środowisko IR w celu skopiowania z magazynu danych 1 do usługi Azure Blob Storage, drugie w celu skopiowania z usługi Azure Blob Storage do magazynu danych 2. Można w ten sposób symulować wymaganie użycia środowiska IR w celu utworzenia mostu, który łączy dwa odrębne magazyny danych.


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>Problem z synchronizacją poświadczeń powoduje utratę poświadczeń z HA

#### <a name="symptoms"></a>Objawy

Poświadczenia źródła danych „XXXXXXXXXX” zostały usunięte z bieżącego węzła środowiska Integration Runtime z ładunkiem „po usunięciu połączonej usługi w witrynie Azure Portal lub ładunek zadania jest nieprawidłowy. Utwórz nową połączoną usługę przy użyciu swoich poświadczeń”.

#### <a name="cause"></a>Przyczyna

Twoje własne środowisko IR jest zbudowane w trybie HA z dwoma węzłami, ale nie są one w stanie synchronizacji poświadczeń, co oznacza, że poświadczenia przechowywane w węźle dyspozytora nie są synchronizowane z innymi węzłami procesu roboczego. Jeśli dojdzie do przejścia w tryb failover z węzła dyspozytora do węzła procesu roboczego, ale poświadczenia istniały tylko w poprzednim węźle dyspozytora, zadanie zakończy się niepowodzeniem podczas próby uzyskania dostępu do poświadczeń i wystąpi powyższy błąd.

#### <a name="resolution"></a>Rozwiązanie

Jedynym sposobem na uniknięcie tego problemu jest upewnienie się, że obydwa węzły są w stanie synchronizacji poświadczeń. W przeciwnym razie należy wprowadzić poświadczenia dla nowego dyspozytora.


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>Nie można wybrać certyfikatu z powodu braku klucza prywatnego

#### <a name="symptoms"></a>Objawy

1.  Importowanie pliku PFX do magazynu certyfikatów.
2.  Podczas wybierania certyfikatu za pomocą interfejsu użytkownika menedżera konfiguracji środowiska IR występuje następujący błąd:

    ![Brak klucza prywatnego](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Przyczyna

- Konto użytkownika ma niskie uprawnienia i nie może uzyskać dostępu do klucza prywatnego.
- Certyfikat został wygenerowany jako podpis, ale nie jako wymiana klucza.

#### <a name="resolution"></a>Rozwiązanie

1.  Użyj konta z uprawnieniami, które może uzyskać dostęp do klucza prywatnego w celu obsługi interfejsu użytkownika.
2.  Uruchom następujące polecenie, aby zaimportować certyfikat:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>Konfiguracja samodzielnego środowiska IR

### <a name="the-integration-runtime-registration-error"></a>Błąd rejestracji Integration Runtime 

#### <a name="symptoms"></a>Objawy

Czasami chcemy uruchomić własne środowisko IR na innym koncie z przyczyn takich jak poniżej:
- Zasady firmowe nie zezwalają na konto usługi.
- Wymagane jest pewne uwierzytelnianie.

Po zmianie konta usługi w panelu usługi może się okazać, że Integration Runtime przestanie działać.

![Błąd rejestracji w podczerwieni](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Przyczyna

Istnieje wiele zasobów, które są przydzielone tylko do konta usługi. W przypadku zmiany konta usługi na inne konto uprawnienie do wszystkich zasobów zależnych pozostaje takie same.

#### <a name="resolution"></a>Rozwiązanie

Aby sprawdzić błąd, przejdź do dziennika zdarzeń Integration Runtime.

![Dziennik zdarzeń IR](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

Jeśli błąd jest wyświetlany jak powyżej *UnauthorizedAccessException*, postępuj zgodnie z poniższymi instrukcjami:


1. Sprawdź konto usługi logowania *DIAHostService* w panelu usługi systemu Windows.

    ![Konto usługi logowania](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Sprawdź, czy konto usługi logowania ma uprawnienie R/W odniesieniu do folderu: *%ProgramData%\Microsoft\DataTransfer\DataManagementGateway*.

    - Domyślnie, jeśli konto logowania do usługi nie zostało zmienione, powinno mieć uprawnienie R/W.

        ![Uprawnienie usługi](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - Jeśli zmieniono konto logowania do usługi, wykonaj następujące kroki, aby rozwiązać problem:
        1. Wyczyść, aby odinstalować bieżące środowisko IR samoobsługowego.
        1. Zainstaluj własne bity IR.
        1. Postępuj zgodnie z poniższymi instrukcjami, aby zmienić konto usługi: 
            1. Przejdź do folderu instalacji środowiska IR selfhosted, przełącz się do folderu: *Microsoft Integration Runtime\4.0\Shared*.
            1. Uruchom wiersz polecenia przy użyciu podwyższonego poziomu uprawnień. Zamień *\<user>* i *\<password>* przy użyciu własnej nazwy użytkownika i hasła, a następnie uruchom polecenie poniżej:
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. Jeśli chcesz zmienić konto na LocalSystem, upewnij się, że dla tego konta używasz poprawnego formatu. Oto przykład poprawnego formatu:

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                **Nie** używaj formatu, jak pokazano poniżej:

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. W związku z tym, ponieważ system lokalny ma wyższy poziom uprawnień niż administrator, można go również bezpośrednio zmienić w "usługach".
            1. Możesz użyć użytkownika lokalnego/domeny dla konta logowania usługi IR.            
        1. Zarejestruj Integration Runtime.

Jeśli błąd jest wyświetlany jako: *nie można uruchomić usługi Integration Runtime Service "(DIAHostService). Sprawdź, czy masz wystarczające uprawnienia do uruchamiania usług systemowych*, postępując zgodnie z poniższymi instrukcjami:

1. Sprawdź konto usługi logowania *DIAHostService* w panelu usługi systemu Windows.
   
    ![Konto usługi logowania](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Sprawdź, czy konto usługi logowania ma uprawnienie **Logowanie jako usługa** do uruchomienia usługi systemu Windows:

    ![Logowanie jako usługa](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Więcej informacji

Jeśli w przypadku żadnego z powyższych dwóch wzorców nie ma zastosowania, spróbuj zebrać poniższe dzienniki zdarzeń systemu Windows: 
- Dzienniki aplikacji i usług — > Integration Runtime
- Dzienniki systemu Windows — aplikacja >

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>Nie można znaleźć przycisku Register w celu zarejestrowania własnego środowiska IR    

#### <a name="symptoms"></a>Objawy

Nie można znaleźć przycisku **register** w interfejsie użytkownika Configuration Manager podczas rejestrowania własnego środowiska IR.

![Brak przycisku Register](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Przyczyna

Ponieważ wersja *Integration Runtime 3,0*, przycisk **zarejestruj** na istniejącym węźle Integration Runtime został usunięty, aby umożliwić bardziej przejrzyste i bezpieczniejsze środowisko. Jeśli zarejestrowano węzeł w dowolnym środowisku IR (online lub nie), to aby zainstalować go ponownie w innym środowisku IR, należy odinstalować poprzedni węzeł, a następnie zainstalować i zarejestrować nowy.

#### <a name="resolution"></a>Rozwiązanie

1. Przejdź do panelu sterowania, aby odinstalować istniejące Integration Runtime.

    > [!IMPORTANT] 
    > W poniższym procesie wybierz pozycję tak. Nie należy przechowywać danych podczas procesu odinstalowywania.

    ![Usuwanie danych](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Jeśli nie masz Instalatora MSI środowiska Integration Runtime, przejdź do [Centrum pobierania](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) , aby pobrać najnowszą Integration Runtime.
1. Zainstaluj plik MSI i zarejestruj Integration Runtime.


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>Nie można zarejestrować własnego środowiska IR z powodu hosta lokalnego    

#### <a name="symptoms"></a>Objawy

Nie można zarejestrować własnego środowiska IR na nowym komputerze, gdy get_LoopbackIpOrName.

**Debuguj:** Wystąpił błąd w czasie wykonywania.
Inicjator typu dla elementu "Microsoft. DataTransfer. DIAgentHost. DataSourceCache" zgłosił wyjątek.
Wystąpił nieodwracalny błąd podczas wyszukiwania w bazie danych.
 
**Szczegóły wyjątku:** System. TypeInitializationException: inicjator typu dla elementu "Microsoft. DataTransfer. DIAgentHost. DataSourceCache" wywołał wyjątek. ---> system .NET. Sockets. SocketException: Wystąpił nieodwracalny błąd podczas wyszukiwania bazy danych w systemie .NET. DNS. GetAddrInfo (Nazwa ciągu).

#### <a name="cause"></a>Przyczyna

Ten problem występuje zwykle podczas rozpoznawania hosta lokalnego.

#### <a name="resolution"></a>Rozwiązanie

Użyj localhost 127.0.0.1 do pliku hosta i Rozwiąż ten problem.


### <a name="self-hosted-setup-failed"></a>Konfiguracja samodzielna nie powiodła się    

#### <a name="symptoms"></a>Objawy

Nie można odinstalować istniejącego środowiska IR lub zainstalować nowego środowiska IR lub uaktualnić istniejącego środowiska IR do nowego środowiska IR.

#### <a name="cause"></a>Przyczyna

Instalacja zależy od usługi Instalator Windows. Istnieją przyczyny wariantów, które mogą spowodować problem z instalacją:
- Brak wystarczającej ilości miejsca na dysku
- Brak uprawnień
- Usługa NT jest zablokowana z jakiegoś powodu
- Użycie procesora CPU jest zbyt wysokie
- Plik MSI jest hostowany w wolnej lokalizacji sieciowej
- Niektóre pliki systemowe lub rejestry zostały przypadkowo naruszone


## <a name="self-hosted-ir-connectivity-issues"></a>Własne problemy z łącznością IR

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Własne środowisko Integration Runtime nie może nawiązać połączenia z usługą w chmurze

#### <a name="symptoms"></a>Objawy

![Problem z nieobsługiwanym połączeniem IR](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Przyczyna 

Własne środowisko Integration Runtime nie może nawiązać połączenia z usługą Data Factory (zaplecza). Ten problem jest zwykle spowodowany przez ustawienia sieci w zaporze.

#### <a name="resolution"></a>Rozwiązanie

1. Sprawdź, czy usługa Integration Runtime jest uruchomiona.
    
   ![Stan działania usługi IR samoobsługowego](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Jeśli usługa jest uruchomiona, przejdź do kroku 3.

1. Jeśli nie ma skonfigurowanego serwera proxy w ramach własnego środowiska Integration Runtime (jest to ustawienie domyślne), uruchom następujące polecenie programu PowerShell na komputerze, na którym zainstalowano własne środowisko Integration Runtime:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > Adres URL usługi może się różnić w zależności od lokalizacji Data Factory. Adres URL usługi można znaleźć w obszarze **połączenia interfejsu użytkownika funkcji ADF**  >  **Connections**  >  **środowisko Integration Runtimes**  >  **Edycja samohostowanych**  >  **węzłów**IR  >  **przeglądanie adresów URL usług**.
            
    Oczekiwana jest następująca odpowiedź:
            
    ![Odpowiedź polecenia programu PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Jeśli nie otrzymasz oczekiwanej odpowiedzi, użyj jednej z następujących metod odpowiednio do swojej sytuacji:
            
    * Jeśli zostanie wyświetlony komunikat "nie można rozpoznać nazwy zdalnej", występuje problem z systemem nazw domen (DNS). Aby rozwiązać ten problem, skontaktuj się z zespołem sieci.
    * Jeśli zostanie wyświetlony komunikat "certyfikat SSL/TLS nie jest zaufany", sprawdź, czy certyfikat dla programu https://wu2.frontend.clouddatahub.net/ jest zaufany na komputerze, a następnie Zainstaluj certyfikat publiczny za pomocą Menedżera certyfikatów. Ta akcja powinna wyeliminować problem.
    * Przejdź do pozycji Podgląd zdarzeń **systemu Windows**  >  **(dzienniki)**  >  **Dzienniki aplikacji i usług**  >  **Integration Runtime** i sprawdź, czy wystąpił błąd spowodowany przez system DNS, regułę zapory lub ustawienia sieci firmowej. (W przypadku wystąpienia takiego błędu należy wymusić zamknięcie połączenia). Ponieważ każda firma ma dostosowane ustawienia sieci, należy skontaktować się z zespołem sieci, aby rozwiązać te problemy.

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
            
![Odpowiedź polecenia programu PowerShell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Zagadnienia dotyczące serwerów proxy:
> *    Sprawdź, czy serwer proxy należy umieścić na liście bezpiecznych adresatów. Jeśli tak, upewnij się, że [te domeny](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) znajdują się na liście bezpiecznych adresatów.
> *    Sprawdź, czy certyfikat TLS/SSL "wu2.frontend.clouddatahub.net/" jest zaufany na serwerze proxy.
> *    Jeśli używasz uwierzytelniania Active Directory na serwerze proxy, Zmień konto usługi na konto użytkownika, które ma dostęp do serwera proxy jako "usługa Integration Runtime".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Komunikat o błędzie: własny Host Integration Runtime Node/Logical SHIR jest w stanie nieaktywny/"uruchomiony (ograniczony)"

#### <a name="cause"></a>Przyczyna 

Samoobsługowy, zintegrowany węzeł środowiska uruchomieniowego może mieć **nieaktywny** stan, jak pokazano na poniższym zrzucie ekranu:

![Nieaktywny węzeł Self-Hosted IR](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

To zachowanie występuje, gdy węzły nie mogą komunikować się ze sobą.

#### <a name="resolution"></a>Rozwiązanie

1. Zaloguj się do maszyny wirtualnej hostowanej na węźle. W obszarze **Dzienniki aplikacji i usług**  >  **Integration Runtime**Otwórz Podgląd zdarzeń i przefiltruj wszystkie dzienniki błędów.

1. Sprawdź, czy dziennik błędów zawiera następujący błąd: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Jeśli ten błąd jest wyświetlany, uruchom następujące polecenie w wierszu polecenia: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Jeśli wystąpi następujący błąd, skontaktuj się z działem IT w celu uzyskania pomocy w rozwiązaniu tego problemu. Po pomyślnym zalogowaniu się w programie Telnet skontaktuj się z firmą pomoc techniczna firmy Microsoft, jeśli nadal występują problemy ze stanem węzła środowiska uruchomieniowego integracyjne.
        
   ![Błąd wiersza polecenia](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Sprawdź, czy dziennik błędów zawiera następujące elementy:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Aby rozwiązać ten problem, wypróbuj jedną lub obie następujące metody:
    - Umieść wszystkie węzły w tej samej domenie.
    - Dodaj adres IP do mapowania hosta we wszystkich plikach hosta hostowanej maszyny wirtualnej.

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Problem z łącznością między własnym hostowanym środowiskiem IR i Data Factory lub własnym środowiskiem IR i źródłem danych/ujściam

Aby rozwiązać problem z łącznością sieciową, należy wiedzieć, jak zebrać ślad sieci, zrozumieć, jak z niego korzystać i [analizować ślad netmon](#how-to-analyze-netmon-trace) przed zastosowaniem narzędzi netmon w rzeczywistych przypadkach z własnego środowiska IR.

#### <a name="symptoms"></a>Objawy

Czasami w przypadku rozwiązywania problemów z łącznością, takich jak poniżej, między obsługą środowiska IR i Data Factory: 

![Żądanie HTTP nie powiodło się](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Lub jeden między środowiskiem samoobsługowym środowiska IR a źródłem danych/ujściam, wystąpią następujące błędy:

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>Rozwiązanie:

W przypadku wystąpienia powyższych problemów zapoznaj się z poniższymi instrukcjami, aby uzyskać dalsze informacje:

Wykonaj śledzenie netmon i Przeanalizuj je ponownie.
- Po pierwsze, można ustawić filtr, aby wyświetlić wszystkie z serwera po stronie klienta. W poniższym przykładzie można zobaczyć, że po stronie serwera jest Data Factory Server.

    ![Serwer fabryki danych](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- Po otrzymaniu pakietu resetowania można znaleźć konwersację według następującego protokołu TCP.

    ![Znajdowanie konwersacji](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Następnie można uzyskać konwersję między klientem a Data Factory Server poniżej, usuwając filtr.

    ![Pobierz konwersację](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- Na podstawie zebranych śladów netmon możemy stwierdzić, że wartość czasu wygaśnięcia (TimeToLive) wynosi 64. Zgodnie z **domyślnymi wartościami limitu czasu wygaśnięcia i przeskoków** wymienionymi w [tym artykule](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) (jak wyodrębnione poniżej) można zobaczyć, że jest to system Linux, który resetuje pakiet i powoduje odłączenie.

    Domyślne wartości czasu wygaśnięcia i przeskoków różnią się między różnymi systemami operacyjnymi, poniżej przedstawiono ustawienia domyślne dla kilku:
    - Jądro systemu Linux 2,4 (około 2001): 255 dla protokołów TCP, UDP i ICMP
    - Jądro systemu Linux 4,10 (2015): 64 dla protokołów TCP, UDP i ICMP
    - Windows XP (2001): 128 dla protokołów TCP, UDP i ICMP
    - Windows 10 (2015): 128 dla protokołów TCP, UDP i ICMP
    - Windows Server 2008:128 dla protokołów TCP, UDP i ICMP
    - Windows Server 2019 (2018): 128 dla protokołów TCP, UDP i ICMP
    - macOS (2001): 64 dla protokołów TCP, UDP i ICMP

    ![CZAS WYGAŚNIĘCIA 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Jednak jest on wyświetlany jako 61 zamiast 64 w powyższym przykładzie, ponieważ to, kiedy pakiet sieciowy dociera do miejsca docelowego, musi przechodzić przez różne przeskoki, takie jak routery/urządzenia sieciowe. Liczba routerów/urządzeń sieciowych zostanie potrącona w końcowym czasie TTL.
    W takim przypadku można zobaczyć, że Resetowanie może być wysyłane z systemu Linux z wartością TTL 64.

- Musimy sprawdzić czwarty przeskok z własnego środowiska IR, aby upewnić się, że może to być urządzenie resetowania.
 
    *Pakiet sieciowy z systemu Linux system A z parametrem TTL 64-> B TTL 64 minus 1 = 63-> C TTL 63 minus 1 = 62-> TTL 62 minus 1 = 61 własne środowisko IR*

- W idealnej sytuacji czas wygaśnięcia będzie wynosił 128, co oznacza, że system Windows działa w naszym Data Factory. Jak pokazano w poniższym przykładzie, *128 – 107 = 21 przeskoków*, co oznacza, że 21 przeskoków dla pakietu zostało wysłanych z Data Factory do samodzielnego środowiska IR podczas uzgadniania TCP 3.
 
    ![CZAS WYGAŚNIĘCIA 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    W związku z tym należy skontaktować się z zespołem sieci, aby sprawdzić, co czwarty przeskok pochodzi z samodzielnego środowiska IR. Jeśli jest to Zapora jako system Linux, sprawdź wszystkie dzienniki, dlaczego urządzenie resetuje pakiet po uzgodnieniu protokołu TCP 3. Jednakże jeśli nie masz pewności, gdzie należy przeprowadzić badanie, spróbuj uzyskać ślad netmon z własnego środowiska IR i zapory w czasie, aby ustalić, które urządzenie może zresetować ten pakiet i spowodować odłączenie. W takim przypadku należy również skontaktować się z zespołem sieci, aby przejść do przodu.

### <a name="how-to-analyze-netmon-trace"></a>Jak analizować śledzenie netmon

> [!NOTE] 
> Poniższa instrukcja ma zastosowanie do śledzenia netmon. Ponieważ śledzenie netmon jest obecnie poza wsparciem, możesz korzystać z programu Wireshark jako tego samego.

Gdy próbujesz wykonać polecenie telnet **8.8.8.8 888** przy użyciu zebranych śladów netmon, zobaczysz następujący ślad:

![Śledzenie netmon 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Śledzenie netmon 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Oznacza to, że nie można nawiązać połączenia TCP ze stroną serwera **8.8.8.8** w oparciu o port **888**, dlatego w tym miejscu widoczne są dwa **SynReTransmit** dodatkowe pakiety. Ze względu na to, że źródło **host2** nie może nawiązać połączenia z **8.8.8.8em** w pierwszym pakiecie, będzie nadal mieć połączenie.

> [!TIP]
> - Możesz kliknąć pozycję Filtr **obciążenia**filtr  ->  **Standardowy**  ->  **adresy**  ->  **adresy IPv4**.
> - Wprowadź wartość **IPv4. Address = = 8.8.8.8** jako filtr, a następnie kliknij przycisk **Zastosuj**. Następnie zostanie wyświetlona tylko komunikacja z komputera lokalnego do lokalizacji docelowej **8.8.8.8**.

![Filtruj adresy 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![Filtruj adresy 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Poniższy przykład pokazuje, jak wygląda dobry scenariusz. 

- Jeśli program Telnet **8.8.8.8 53** działa prawidłowo bez jakiegokolwiek problemu, można zobaczyć UZGADNIANIE protokołu TCP 3, a następnie sesja kończy się z UZGADNIANIEM protokołu TCP 4.

    ![przykład dobrego scenariusza 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![dobry przykład scenariusza 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- W oparciu o powyższe uzgadnianie TCP 3 można zobaczyć Poniższy przepływ pracy:

    ![Przepływ pracy uzgadniania TCP 3](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- Uzgadnianie protokołu TCP 4 do zakończenia sesji i przepływ pracy będzie pokazane w następujący sposób:

    ![Uzgadnianie protokołu TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Przepływ pracy uzgadniania TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


### <a name="receiving-email-to-update-the-network-configuration-to-allow-communication-with-new-ip-addresses"></a>Otrzymywanie wiadomości e-mail w celu zaktualizowania konfiguracji sieci w celu umożliwienia komunikacji z nowymi adresami IP

#### <a name="symptoms"></a>Objawy

Może zostać wyświetlony poniższy komunikat z powiadomieniem e-mail, który zaleca zaktualizowanie konfiguracji sieci w celu umożliwienia komunikacji z nowymi adresami IP dla Azure Data Factory do 8 listopada 2020:

   ![Powiadomienie e-mail](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="resolution"></a>Rozwiązanie

To powiadomienie dotyczy **komunikacji wychodzącej** z **Integration Runtime** działającego **lokalnie** lub wewnątrz **wirtualnej sieci prywatnej platformy Azure** w usłudze ADF. Na przykład jeśli w sieci wirtualnej platformy Azure jest używany własny program IR lub Azure-SQL Server Integration Services (SSIS), który musi mieć dostęp do usługi ADF, należy sprawdzić, czy trzeba dodać ten nowy zakres adresów IP w regułach **sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)** . Jeśli wychodząca reguła sieciowej grupy zabezpieczeń używa znacznika usługi, nie będzie to miało wpływu.

#### <a name="more-details"></a>Więcej szczegółów

Te nowe zakresy adresów IP **mają wpływ na reguły komunikacji wychodzącej** z **lokalnej zapory** lub **wirtualnej sieci prywatnej platformy Azure** do usługi ADF (zobacz [Konfiguracja zapory i lista dozwolonych adresów IP](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway) dla celów referencyjnych), w przypadku scenariuszy, w których masz własne środowisko IR IR lub SSIS w sieci lokalnej lub Azure Virtual Network, które muszą komunikować się z usługą ADF.

Dla istniejących użytkowników korzystających z **sieci VPN platformy Azure**:

1. Sprawdź wszystkie wychodzące reguły sieciowej grupy zabezpieczeń w sieci prywatnej, na których skonfigurowano SSIS lub Azure SSIS. Jeśli nie występują żadne ograniczenia wychodzące, nie ma to wpływu na te elementy.
1. Jeśli istnieją ograniczenia reguły ruchu wychodzącego, należy sprawdzić, czy jest używany tag usługi. Jeśli używasz znacznika usługi, nie trzeba zmieniać ani dodawać żadnych elementów, ponieważ nowe zakresy adresów IP są objęte istniejącym tagiem usługi. 
  
    ![Sprawdzenie lokalizacji docelowej](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)

1. Jeśli używasz adresów IP bezpośrednio w ustawieniu reguły, sprawdź, czy dodasz wszystkie zakresy adresów IP w [linku pobierania zakresu adresów IP](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files). Nowe zakresy adresów IP zostały już umieszczone w tym pliku. W przypadku nowych użytkowników należy wykonać czynności opisane w dokumencie, aby skonfigurować reguły sieciowej grupy zabezpieczeń z zastosowaniem samodzielnej konfiguracji środowiska IR lub usług SSIS w naszym środowisku.

W przypadku istniejących użytkowników **z systemem**SSIS IR lub własnym lokalnym systemem IR:

- Sprawdź poprawność od zespołu infrastruktury sieciowej i sprawdź, czy muszą zawierać nowe adresy zakresu adresów IP komunikacji dla reguł ruchu wychodzącego.
- W przypadku reguł zapory opartych na nazwach FQDN nie są wymagane żadne aktualizacje w przypadku korzystania z ustawień opisanych w obszarze [Konfiguracja zapory i listy dozwolonych adresów IP](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway). 
- Niektóre zapory lokalne obsługują Tagi usług, jeśli jest używany zaktualizowany plik konfiguracji tagów usługi platformy Azure, nie są potrzebne żadne inne zmiany.

## <a name="self-hosted-ir-sharing"></a>Udostępnianie własnego środowiska IR

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Udostępnianie samodzielnego środowiska IR z innej dzierżawy nie jest obsługiwane 

#### <a name="symptoms"></a>Objawy

Użytkownik może zauważyć inne fabryki danych (w różnych dzierżawcach) podczas próby udostępnienia samodzielnego środowiska IR z poziomu interfejsu użytkownika Azure Data Factory, ale nie może współdzielić środowiska IR obsługiwanego przez inne dzierżawców.

#### <a name="cause"></a>Przyczyna

Samoobsługowe środowisko IR nie może być współużytkowane przez wiele dzierżawców.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać pomoc dotyczącą rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo na platformie Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Strona pytania&pytań i odpowiedzi](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Forum przepełnienia stosu dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Przewodnik wydajności dotyczący mapowania przepływów danych](concepts-data-flow-performance.md)
