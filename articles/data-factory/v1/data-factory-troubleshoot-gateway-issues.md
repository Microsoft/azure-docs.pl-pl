---
title: Rozwiązywanie problemów z bramą Zarządzanie danymi
description: Zawiera wskazówki dotyczące rozwiązywania problemów związanych z bramą Zarządzanie danymi.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 284486c5db248ced8ada6e7194c7bc5a9be5689f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388349"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Rozwiązywanie problemów z używaniem bramy zarządzania danymi
Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z usługą Zarządzanie danymi Gateway.

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [własne środowisko Integration Runtime w programie Data Factory](../create-self-hosted-integration-runtime.md).

Aby uzyskać szczegółowe informacje o bramie, zobacz artykuł dotyczący [bramy zarządzanie danymi](data-factory-data-management-gateway.md) . Zapoznaj się z artykułem [przenoszenie danych między środowiskiem lokalnym i chmurą](data-factory-move-data-between-onprem-and-cloud.md) , aby zapoznać się z przewodnikiem przenoszenia danych z bazy danych SQL Server do Microsoft Azure usługi BLOB Storage przy użyciu bramy.

## <a name="failed-to-install-or-register-gateway"></a>Nie można zainstalować lub zarejestrować bramy
### <a name="1-problem"></a>1. problem
Ten komunikat o błędzie jest wyświetlany podczas instalowania i rejestrowania bramy, w odróżnieniu od pobrania pliku instalacyjnego bramy.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Przyczyna
Na komputerze, na którym próbujesz zainstalować bramę, nie powiodło się pobranie najnowszego pliku instalacyjnego bramy z centrum pobierania z powodu problemu z siecią.

#### <a name="resolution"></a>Rozwiązanie
Sprawdź ustawienia serwera proxy zapory, aby sprawdzić, czy ustawienia blokują połączenie sieciowe z komputera do [Centrum pobierania](https://download.microsoft.com/), i odpowiednio zaktualizuj ustawienia.

Alternatywnie można pobrać plik instalacyjny dla najnowszej bramy z [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=39717) na innych maszynach, które mogą uzyskać dostęp do centrum pobierania. Następnie można skopiować plik Instalatora na komputer hosta bramy i uruchomić go ręcznie, aby zainstalować i zaktualizować bramę.

### <a name="2-problem"></a>2. problem
Ten błąd jest wyświetlany podczas próby zainstalowania bramy przez kliknięcie przycisku **Instaluj bezpośrednio na tym komputerze** w Azure Portal.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Przyczyna
Brama jest już zainstalowana na tym komputerze.

#### <a name="resolution"></a>Rozwiązanie
Odinstaluj istniejącą bramę na komputerze i ponownie kliknij link **Zainstaluj bezpośrednio na tym komputerze** .

### <a name="3-problem"></a>3. problem
Ten błąd może pojawić się podczas rejestrowania nowej bramy.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Przyczyna
Ten komunikat może zostać wyświetlony z jednego z następujących powodów:

* Format klucza bramy jest nieprawidłowy.
* Klucz bramy został unieważniony.
* Klucz bramy został ponownie wygenerowany z portalu.  

#### <a name="resolution"></a>Rozwiązanie
Sprawdź, czy korzystasz z odpowiedniego klucza bramy z portalu. W razie potrzeby Wygeneruj ponownie klucz i zarejestruj bramę przy użyciu klucza.

### <a name="4-problem"></a>4. problem
Podczas rejestrowania bramy może zostać wyświetlony następujący komunikat o błędzie.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![Zawartość lub format klucza jest nieprawidłowy](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Przyczyna
Zawartość lub format klucza bramy wejściowej jest niepoprawny. Jedną z przyczyn może być skopiowanie tylko części klucza z portalu lub użycie nieprawidłowego klucza.

#### <a name="resolution"></a>Rozwiązanie
Wygeneruj klucz bramy w portalu i użyj przycisku kopiowania, aby skopiować cały klucz. Następnie wklej go w tym oknie, aby zarejestrować bramę.

### <a name="5-problem"></a>5. problem
Podczas rejestrowania bramy może zostać wyświetlony następujący komunikat o błędzie.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Zrzut ekranu, który wyróżnia komunikat o błędzie wskazujący, że klucz bramy jest nieprawidłowy lub pusty.](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Przyczyna
Klucz bramy został wygenerowany ponownie lub Brama została usunięta z Azure Portal. Może się również zdarzyć, że konfiguracja bramy Zarządzanie danymi nie jest najnowsza.

#### <a name="resolution"></a>Rozwiązanie
Sprawdź, czy konfiguracja bramy Zarządzanie danymi to Najnowsza wersja. Najnowsza wersja można znaleźć w [Centrum pobierania](https://go.microsoft.com/fwlink/p/?LinkId=271260)Microsoft.

Jeśli konfiguracja jest aktualna/Najnowsza i nadal istnieje w portalu, ponownie Wygeneruj klucz bramy w Azure Portal i użyj przycisku kopiowania, aby skopiować cały klucz, a następnie wklej go w tym oknie, aby zarejestrować bramę. W przeciwnym razie należy ponownie utworzyć bramę i zacząć od nowa.

### <a name="6-problem"></a>6. problem
Podczas rejestrowania bramy może zostać wyświetlony następujący komunikat o błędzie.

`Error: Gateway has been online for a while, then shows "Gateway is not registered" with the status "Gateway key is invalid"`

![Klucz bramy jest nieprawidłowy lub pusty](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Przyczyna
Ten błąd może wystąpić, ponieważ Brama została usunięta lub ponownie wygenerowano skojarzony klucz bramy.

#### <a name="resolution"></a>Rozwiązanie
Jeśli Brama została usunięta, ponownie utwórz bramę z portalu, kliknij pozycję **zarejestruj**, skopiuj klucz z portalu, wklej go i spróbuj zarejestrować bramę.

Jeśli Brama nadal istnieje, ale jej klucz został wygenerowany ponownie, Użyj nowego klucza do zarejestrowania bramy. Jeśli nie masz klucza, ponownie Wygeneruj klucz w portalu.

### <a name="7-problem"></a>7. problem
Gdy rejestrujesz bramę, może być konieczne podanie ścieżki i hasła do certyfikatu.

![Zrzut ekranu pokazujący, w jaki sposób wprowadzić ścieżkę i hasło do certyfikatu.](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Przyczyna
Brama została zarejestrowana na innych maszynach. Podczas początkowej rejestracji bramy certyfikat szyfrowania został skojarzony z bramą. Certyfikat może zostać wygenerowany samodzielnie przez bramę lub dostarczony przez użytkownika.  Ten certyfikat jest używany do szyfrowania poświadczeń magazynu danych (połączonej usługi).  

![Eksportowanie certyfikatu](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Podczas przywracania bramy na innym komputerze-hoście, Kreator rejestracji pyta o ten certyfikat, aby odszyfrować poświadczenia wcześniej zaszyfrowane za pomocą tego certyfikatu.  Bez tego certyfikatu poświadczenia nie mogą zostać odszyfrowane przez nową bramę, a kolejne wykonania działania kopiowania skojarzone z tą nową bramą zakończą się niepowodzeniem.  

#### <a name="resolution"></a>Rozwiązanie
Jeśli certyfikat poświadczeń został wyeksportowany z oryginalnej maszyny bramy przy użyciu przycisku **Eksportuj** na karcie **ustawienia** w zarządzanie danymi Configuration Manager bramy, użyj tego certyfikatu w tym miejscu.

Nie można pominąć tego etapu podczas odzyskiwania bramy. Jeśli brakuje certyfikatu, musisz usunąć bramę z portalu i ponownie utworzyć nową bramę.  Ponadto należy zaktualizować wszystkie połączone usługi, które są powiązane z bramą, ponownie wprowadzając swoje poświadczenia.

### <a name="8-problem"></a>8. problem
Może zostać wyświetlony następujący komunikat o błędzie.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Przyczyna
Ten błąd występuje, gdy Brama znajduje się w środowisku, które wymaga serwera proxy HTTP w celu uzyskania dostępu do zasobów internetowych, lub hasło uwierzytelniania serwera proxy jest zmieniane, ale nie jest odpowiednio aktualizowane w bramie.

#### <a name="resolution"></a>Rozwiązanie
Postępuj zgodnie z instrukcjami w sekcji Uwagi dotyczące serwera proxy w tym artykule, a następnie skonfiguruj ustawienia serwera proxy za pomocą Configuration Manager Zarządzanie danymi bramy.

## <a name="gateway-is-online-with-limited-functionality"></a>Brama jest w trybie online z ograniczoną funkcjonalnością
### <a name="1-problem"></a>1. problem
Stan bramy jest wyświetlany jako online z ograniczoną funkcjonalnością.

#### <a name="cause"></a>Przyczyna
Stan bramy jest wyświetlany w trybie online z ograniczoną funkcjonalnością z jednego z następujących powodów:

* Brama nie może nawiązać połączenia z usługą w chmurze za poorednictwem Azure Service Bus.
* Usługa w chmurze nie może połączyć się z bramą za Service Bus.

Gdy brama jest w trybie online z ograniczoną funkcjonalnością, może nie być możliwe użycie Kreatora kopiowania Data Factory do tworzenia potoków danych do kopiowania danych do lub z lokalnych magazynów danych. Jako obejście można użyć edytora Data Factory w portalu, Visual Studio lub Azure PowerShell.

#### <a name="resolution"></a>Rozwiązanie
Rozwiązanie tego problemu (online z ograniczoną funkcjonalnością) jest zależne od tego, czy brama nie może nawiązać połączenia z usługą w chmurze czy w inny sposób. Poniższe sekcje zawierają te rozwiązania.

### <a name="2-problem"></a>2. problem
Zobaczysz następujący błąd.

`Error: Gateway cannot connect to cloud service through service bus`

![Brama nie może nawiązać połączenia z usługą w chmurze](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Przyczyna
Brama nie może nawiązać połączenia z usługą w chmurze za pomocą Service Bus.

#### <a name="resolution"></a>Rozwiązanie
Wykonaj następujące kroki, aby ponownie uzyskać dostęp do bramy w trybie online:

1. Zezwalaj na reguły wychodzące adresów IP na maszynie bramy i w zaporze firmowej. Adresy IP można znaleźć z dziennika zdarzeń systemu Windows (ID = = 401): podjęto próbę uzyskania dostępu do gniazda w sposób zabroniony przez jego uprawnienia dostępu XX. XX. XX. XX: 9350.
1. Skonfiguruj ustawienia serwera proxy na bramie. Szczegółowe informacje znajdują się w sekcji zagadnienia dotyczące serwerów proxy.
1. Włącz porty wychodzące 5671 i 9350-9354 zarówno w zaporze systemu Windows na maszynie bramy, jak i w firmowej zaporze. Szczegółowe informacje znajdują się w sekcji porty i zapory. Ten krok jest opcjonalny, ale zalecamy jego użycie w celu uwzględnienia wydajności.

### <a name="3-problem"></a>3. problem
Zobaczysz następujący błąd.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Przyczyna
Przejściowy błąd połączenia sieciowego.

#### <a name="resolution"></a>Rozwiązanie
Wykonaj następujące kroki, aby ponownie uzyskać dostęp do bramy w trybie online:

1. Poczekaj kilka minut, łączność zostanie automatycznie odzyskana po wystąpieniu błędu.
1. Jeśli błąd będzie się powtarzać, uruchom ponownie usługę bramy.

## <a name="failed-to-author-linked-service"></a>Tworzenie połączonej usługi nie powiodło się
### <a name="problem"></a>Problem
Ten błąd może wystąpić podczas próby użycia Menedżera poświadczeń w portalu do wprowadzania poświadczeń dla nowej połączonej usługi lub aktualizacji poświadczeń dla istniejącej połączonej usługi.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Po wyświetleniu tego błędu Strona Ustawienia Zarządzanie danymi bramy Configuration Manager może wyglądać podobnie jak na poniższym zrzucie ekranu.

![Nie można nawiązać połączenia z bazą danych](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Przyczyna
Certyfikat TLS/SSL mógł zostać utracony na maszynie bramy. Komputer bramy nie może załadować certyfikatu obecnie używanego do szyfrowania TLS. W dzienniku zdarzeń może zostać wyświetlony komunikat o błędzie podobny do następującego.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Rozwiązanie
Wykonaj następujące kroki, aby rozwiązać ten problem:

1. Uruchom Configuration Manager bramy Zarządzanie danymi.
2. Przejdź do karty **Ustawienia** .  
3. Kliknij przycisk **Zmień** , aby zmienić certyfikat TLS/SSL.

   ![Przycisk zmiany certyfikatu](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Wybierz nowy certyfikat jako certyfikat TLS/SSL. Możesz użyć dowolnego certyfikatu TLS/SSL, który jest generowany przez użytkownika lub dowolną organizację.

   ![Określ certyfikat](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Działanie kopiowania kończy się niepowodzeniem
### <a name="problem"></a>Problem
Po skonfigurowaniu potoku w portalu można zauważyć następujący błąd "UserErrorFailedToConnectToSqlserver".

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Przyczyna
Może się to zdarzyć z różnych powodów i środki zaradcze odpowiednio się różnią.

#### <a name="resolution"></a>Rozwiązanie
Przed nawiązaniem połączenia z bazą danych SQL Zezwól na wychodzące połączenia TCP na porcie TCP/1433 po stronie klienta bramy Zarządzanie danymi.

Jeśli docelowa baza danych znajduje się w Azure SQL Database, Sprawdź także SQL Server ustawienia zapory dla platformy Azure.

Zapoznaj się z poniższą sekcją, aby przetestować połączenie z lokalnym magazynem danych.

## <a name="data-store-connection-or-driver-related-errors"></a>Połączenie z magazynem danych lub błędy związane z sterownikiem
Jeśli widzisz błędy dotyczące połączenia z magazynem danych lub sterownika, wykonaj następujące czynności:

1. Uruchom Configuration Manager bramy Zarządzanie danymi na maszynie bramy.
2. Przejdź na kartę **Diagnostyka** .
3. W obszarze **połączenie testowe** Dodaj wartości grupy bramy.
4. Kliknij przycisk **Testuj** , aby sprawdzić, czy możesz nawiązać połączenie z lokalnym źródłem danych z maszyny bramy przy użyciu informacji o połączeniu i poświadczeń. Jeśli połączenie testowe nadal zakończy się niepowodzeniem po zainstalowaniu sterownika, uruchom bramę ponownie, aby wczytać najnowsze zmiany.

![Testuj połączenie na karcie Diagnostyka](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Dzienniki bramy
### <a name="send-gateway-logs-to-microsoft"></a>Wyślij dzienniki bramy do firmy Microsoft
Podczas kontaktowania się z pomoc techniczna firmy Microsoft, aby uzyskać pomoc dotyczącą rozwiązywania problemów z bramą, może zostać wyświetlony monit o udostępnienie dzienników bramy. Za pomocą wersji bramy można udostępniać wymagane dzienniki bramy przy użyciu dwóch kliknięć przycisku w Zarządzanie danymi Configuration Manager bramy.    

1. Przejdź do karty **Diagnostyka** w zarządzanie danymi Configuration Manager bramy.

    ![Karta Diagnostyka Zarządzanie danymi Gateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Kliknij pozycję **Wyślij dzienniki** , aby zobaczyć następujące okno dialogowe.

    ![Dzienniki wysyłania Zarządzanie danymi bramy](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. Obowiązkowe Kliknij przycisk **Wyświetl dzienniki** , aby przejrzeć dzienniki w Podglądzie zdarzeń.
4. Obowiązkowe Kliknij pozycję **prywatność** , aby przejrzeć zasady zachowania poufności informacji w usługach sieci Web firmy Microsoft.
5. Gdy jesteś zadowolony z tego, co zamierzasz przekazać, kliknij pozycję **Wyślij dzienniki** , aby faktycznie wysłać dzienniki z ostatnich siedmiu dni do firmy Microsoft w celu rozwiązywania problemów. Powinien zostać wyświetlony stan operacji wysyłania dzienników, jak pokazano na poniższym zrzucie ekranu.

    ![Zrzut ekranu pokazujący lokalizację, w której ma zostać wyświetlony stan operacji wysyłania dzienników.](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Po zakończeniu operacji zobaczysz okno dialogowe, jak pokazano na poniższym zrzucie ekranu.

    ![Stan dzienników wysyłania Zarządzanie danymi bramy](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Zapisz **Identyfikator raportu** i udostępnij go pomoc techniczna firmy Microsoft. Identyfikator raportu służy do lokalizowania dzienników bramy przekazanych w celu rozwiązywania problemów.  Identyfikator raportu jest również zapisywany w Podglądzie zdarzeń.  Można je znaleźć, przeglądając identyfikator zdarzenia "25" i sprawdzając datę i godzinę.

    ![Identyfikator raportu wysyłania dzienników bramy Zarządzanie danymi](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Archiwizuj dzienniki bramy na maszynie hosta bramy
Istnieją sytuacje, w których występują problemy z bramą i nie można udostępniać dzienników bramy bezpośrednio:

* Należy ręcznie zainstalować bramę i zarejestrować bramę.
* Podjęto próbę zarejestrowania bramy z wygenerowanym kluczem w Zarządzanie danymi Configuration Manager bramy.
* Podjęto próbę wysłania dzienników, a usługa hosta bramy nie może być podłączona.

W tych scenariuszach można zapisać dzienniki bramy jako plik zip i udostępnić je podczas kontaktowania się z pomocą techniczną firmy Microsoft. Na przykład, jeśli wystąpi błąd podczas rejestrowania bramy, jak pokazano na poniższym zrzucie ekranu.   

![Błąd rejestracji bramy Zarządzanie danymi](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Kliknij link **archiwizuj dzienniki bramy** , aby zarchiwizować i zapisać dzienniki, a następnie udostępnić plik zip w pomocy technicznej firmy Microsoft.

![Dzienniki archiwum Zarządzanie danymi Gateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Lokalizowanie dzienników bramy
Szczegółowe informacje dziennika bramy można znaleźć w dziennikach zdarzeń systemu Windows.

1. Uruchom **Podgląd zdarzeń** systemu Windows.
2. Zlokalizuj dzienniki w folderze **aplikacje i usługi Dzienniki**  >  **Zarządzanie danymi bramy** .

   W przypadku rozwiązywania problemów związanych z bramą Znajdź zdarzenia poziomu błędu w Podglądzie zdarzeń.

![Dzienniki bramy Zarządzanie danymi w Podglądzie zdarzeń](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
