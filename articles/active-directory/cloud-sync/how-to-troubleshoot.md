---
title: Azure AD Connect Rozwiązywanie problemów z synchronizacją w chmurze
description: W tym artykule opisano sposób rozwiązywania problemów, które mogą wystąpić w przypadku agenta aprowizacji w chmurze.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 01/19/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: fad065795345314c58e83c8d2614ed01c9e78e49
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805533"
---
# <a name="cloud-sync-troubleshooting"></a>Rozwiązywanie problemów z synchronizacją w chmurze

Synchronizacja w chmurze dotyka wielu różnych rzeczy i ma wiele różnych zależności. Ten rozległy zakres może prowadzić do różnych problemów. Ten artykuł pomaga w rozwiązywaniu tych problemów. Przedstawiono w nim typowe obszary, w których można skoncentrować się na tym, jak zbierać dodatkowe informacje i różne techniki, których można użyć do śledzenia problemów.


## <a name="common-troubleshooting-areas"></a>Typowe obszary rozwiązywania problemów

|Nazwa|Opis|
|-----|-----|
|[Problemy z agentem](#agent-problems)|Sprawdź, czy Agent został zainstalowany prawidłowo i czy komunikuje się z Azure Active Directory (Azure AD).|
|[Problemy z synchronizacją obiektów](#object-synchronization-problems)|Dzienniki aprowizacji umożliwiają rozwiązywanie problemów z synchronizacją obiektów.|
|[Inicjowanie obsługi administracyjnej problemów z kwarantanną](#provisioning-quarantined-problems)|Informacje o aprowizacji problemów z kwarantanną i sposobach ich naprawy.|


## <a name="agent-problems"></a>Problemy z agentem
Poniżej wymieniono niektóre z nich, które mają być zweryfikowane przez agenta:

-  Czy jest zainstalowana?
-  Czy agent działa lokalnie?
-  Czy Agent jest w portalu?
-  Czy Agent jest oznaczony jako w dobrej kondycji?

Te elementy można zweryfikować w Azure Portal i na serwerze lokalnym, na którym jest uruchomiony Agent programu.

### <a name="azure-portal-agent-verification"></a>Weryfikacja agenta Azure Portal

Aby sprawdzić, czy Agent jest widziany przez platformę Azure i jest w dobrej kondycji, wykonaj następujące kroki.

1. Zaloguj się do witryny Azure Portal.
1. Po lewej stronie wybierz pozycję **Azure Active Directory**  >  **Azure AD Connect**. W środkowym obszarze Wybierz pozycję **Zarządzaj synchronizacją**.
1. Na ekranie **Azure AD Connect synchronizacji w chmurze** wybierz pozycję **Przejrzyj wszystkich agentów**.

   ![Przejrzyj wszystkich agentów](media/how-to-install/install-7.png)</br>
 
1. Na ekranie **na lokalnym agencie aprowizacji** można zobaczyć zainstalowanych agentów. Sprawdź, czy dany Agent jest tam i jest oznaczony jako w *dobrej kondycji*.

   ![Ekran lokalnych agentów aprowizacji](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>Weryfikowanie portu

Sprawdź, czy platforma Azure nasłuchuje na porcie 443 i czy Agent może się z nim komunikować. 

Ten test sprawdza, czy agenci mogą komunikować się z platformą Azure przez port 443. Otwórz przeglądarkę i przejdź do poprzedniego adresu URL z serwera, na którym zainstalowano agenta.

![Weryfikacja osiągalności portów](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>Na serwerze lokalnym

Aby sprawdzić, czy agent działa, wykonaj następujące kroki.

1. Na serwerze z zainstalowanym agentem Otwórz pozycję **usługi** , przechodząc do niej lub klikając **polecenie Uruchom program**  >    >  **Services. msc**.
1. W obszarze **usługi** upewnij się, że **Microsoft Azure AD Connect agent Aktualizator** i **Microsoft Azure AD Connect Agent aprowizacji** jest tam i ich stan jest *uruchomiony*.

   ![Ekran usług](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>Typowe problemy z instalacją agenta

W poniższych sekcjach opisano niektóre typowe problemy z instalacją agenta i typowe rozwiązania.

#### <a name="agent-failed-to-start"></a>Uruchomienie agenta nie powiodło się

Może zostać wyświetlony komunikat o błędzie informujący o tym, że:

**Nie można uruchomić usługi "Microsoft Azure AD połączyć się z agentem aprowizacji. Sprawdź, czy masz wystarczające uprawnienia do uruchamiania usług systemowych.** 

Ten problem jest zwykle spowodowany przez zasady grupy, które uniemożliwiły zastosowanie uprawnień do lokalnego konta logowania usługi NT, utworzonego przez Instalatora (NT SERVICE\AADConnectProvisioningAgent). Te uprawnienia są wymagane do uruchomienia usługi.

Aby rozwiązać ten problem, wykonaj następujące kroki.

1. Zaloguj się na serwerze przy użyciu konta administratora.
1. Otwórz **usługi** , przechodząc do niej lub naciskając **pozycję Uruchom**  >    >  **Services. msc**.
1. W obszarze **usługi** kliknij dwukrotnie pozycję **Microsoft Azure AD Połącz agenta aprowizacji**.
1. Na karcie **Logowanie** Zmień **to konto** na administratora domeny. Następnie uruchom ponownie usługę. 

   ![Karta logowanie](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Limit czasu agenta lub nieprawidłowy certyfikat

Podczas próby zarejestrowania agenta może zostać wyświetlony następujący komunikat o błędzie.

![Komunikat o błędzie limitu czasu](media/how-to-troubleshoot/troubleshoot-4.png)

Ten problem zwykle występuje, ponieważ agent nie może nawiązać połączenia z usługą zarządzania tożsamościami hybrydowymi i wymaga skonfigurowania serwera proxy HTTP. Aby rozwiązać ten problem, skonfiguruj serwer proxy ruchu wychodzącego. 

Agent aprowizacji obsługuje korzystanie z wychodzącego serwera proxy. Można ją skonfigurować, edytując plik konfiguracji agenta *C:\Program Files\Microsoft Azure AD Connect aprowizacji Agent\AADConnectProvisioningAgent.exe.config*. Dodaj do niego następujące wiersze, w kierunku końca pliku tuż przed tagiem zamykającym `</configuration>` .
Zastąp zmienne `[proxy-server]` i `[proxy-port]` wartościami nazw i portów serwera proxy.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>Rejestracja agenta kończy się niepowodzeniem z powodu błędu zabezpieczeń

Podczas instalowania agenta aprowizacji w chmurze może zostać wyświetlony komunikat o błędzie.

Ten problem jest zwykle spowodowany tym, że Agent nie może wykonać skryptów rejestracji programu PowerShell z powodu lokalnych zasad wykonywania programu PowerShell.

Aby rozwiązać ten problem, Zmień zasady wykonywania programu PowerShell na serwerze. Zasady komputera i użytkownika muszą mieć ustawioną wartość *undefined* lub *RemoteSigned*. Jeśli są ustawione jako *nieograniczone*, zobaczysz ten błąd. Aby uzyskać więcej informacji, zobacz [zasady wykonywania programu PowerShell](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Pliki dziennika

Domyślnie agent generuje minimalną liczbę komunikatów o błędach i ilość informacji śledzenia stosu. Te dzienniki śledzenia można znaleźć w folderze **C:\ProgramData\Microsoft\Azure usługi AD Connect Provisioning Agent\Trace**.

Aby zebrać dodatkowe szczegóły dotyczące rozwiązywania problemów związanych z agentem, wykonaj następujące czynności.

1.  Zainstaluj moduł AADCloudSyncTools PowerShell zgodnie z opisem w [tym miejscu](reference-powershell.md#install-the-aadcloudsynctools-powershell-module).
2. Użyj `Export-AADCloudSyncToolsLogs` polecenia cmdlet programu PowerShell, aby przechwycić informacje.  Aby dostosować zbieranie danych, można użyć następujących przełączników.
      - SkipVerboseTrace tylko na eksportowanie bieżących dzienników bez przechwytywania pełnych dzienników (wartość domyślna = false)
      - TracingDurationMins, aby określić inny czas trwania przechwytywania (wartość domyślna = 3 minuty)
      - OutputPath aby określić inną ścieżkę wyjściową (domyślnie = dokumenty użytkownika)


## <a name="object-synchronization-problems"></a>Problemy z synchronizacją obiektów

Poniższa sekcja zawiera informacje dotyczące rozwiązywania problemów z synchronizacją obiektów.

### <a name="provisioning-logs"></a>Dzienniki aprowizowania

W Azure Portal dzienniki aprowizacji mogą pomóc w śledzeniu i rozwiązywaniu problemów z synchronizacją obiektów. Aby wyświetlić dzienniki, wybierz pozycję **dzienniki**.

![Przycisk dzienniki](media/how-to-troubleshoot/log-1.png)

Dzienniki aprowizacji zapewniają mnóstwo informacji na temat stanu obiektów synchronizowanych między środowiskiem lokalnym Active Directory i platformą Azure.

![Ekran dzienniki aprowizacji](media/how-to-troubleshoot/log-2.png)

Możesz użyć pól rozwijanych w górnej części strony, aby filtrować widok do zera w przypadku określonych problemów, takich jak daty. Kliknij dwukrotnie pojedyncze zdarzenie, aby wyświetlić dodatkowe informacje.

![Informacje o polu listy rozwijanej dzienniki aprowizacji](media/how-to-troubleshoot/log-3.png)

Te informacje zawierają szczegółowe instrukcje i miejsce, w którym występuje problem z synchronizacją. W ten sposób można zlokalizować dokładne miejsce problemu.


## <a name="provisioning-quarantined-problems"></a>Inicjowanie obsługi administracyjnej problemów z kwarantanną

Usługa Cloud Sync monitoruje kondycję konfiguracji i umieszcza w stanie kwarantanny obiekty w złej kondycji. Jeśli większość lub wszystkie wywołania związane z systemem docelowym są spójne niepowodzeniem z powodu błędu, na przykład nieprawidłowe poświadczenia administratora, zadanie synchronizacji zostanie oznaczone jako objęte kwarantanną.

![Stan kwarantanny](media/how-to-troubleshoot/quarantine-1.png)

Po wybraniu stanu można wyświetlić dodatkowe informacje o kwarantannie. Możesz również uzyskać kod błędu i komunikat.

![Zrzut ekranu, na którym są wyświetlane dodatkowe informacje o kwarantannie.](media/how-to-troubleshoot/quarantine-2.png)

Kliknięcie prawym przyciskiem myszy stanu spowoduje wyświetlenie dodatkowych opcji:
    
   - Wyświetlanie dzienników aprowizacji
   - Wyświetl agenta
   - Wyczyść kwarantannę

![Zrzut ekranu, na którym są wyświetlane opcje menu dostępnego po kliknięciu prawym przyciskiem myszy.](media/how-to-troubleshoot/quarantine-4.png)


### <a name="resolve-a-quarantine"></a>Rozwiązywanie kwarantanny
Istnieją dwa różne sposoby rozwiązania kwarantanny.  Są to:

  - Wyczyść kwarantannę — czyści znak wodny i uruchamia synchronizację Delta
  - Uruchom ponownie zadanie aprowizacji — czyści znak wodny i uruchamia synchronizację początkową.

#### <a name="clear-quarantine"></a>Wyczyść kwarantannę
Aby wyczyścić znak wodny i uruchomić synchronizację Delta w zadaniu aprowizacji po jego zweryfikowaniu, po prostu kliknij prawym przyciskiem myszy stan i wybierz pozycję **Wyczyść kwarantannę**.

Powinna zostać wyświetlona informacja, że Kwarantanna jest czyszczona.

![Zrzut ekranu przedstawiający informację o tym, że Kwarantanna jest czyszczona.](media/how-to-troubleshoot/quarantine-5.png)

Następnie stan agenta powinien być widoczny w dobrej kondycji.

![Informacje o stanie kwarantanny](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>Uruchom ponownie zadanie aprowizacji
Użyj Azure Portal, aby ponownie uruchomić zadanie aprowizacji. Na stronie Konfiguracja agenta wybierz pozycję **Uruchom ponownie Inicjowanie obsługi administracyjnej**.

  ![Ponowne uruchamianie aprowizacji](media/how-to-troubleshoot/quarantine-3.png)

- Użyj Microsoft Graph, aby [ponownie uruchomić zadanie aprowizacji](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). Będziesz mieć pełną kontrolę nad tym, co zostało ponownie uruchomione. Możesz wyczyścić:
  - Usługa Escrow umożliwia ponowne uruchomienie licznika Escrow, który naliczy się na stan kwarantanny.
  - Kwarantanna, aby usunąć aplikację z kwarantanny.
  - Znaki wodne. 
  
  Użyj następującego żądania:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>Naprawianie konta usługi synchronizacji w chmurze
Jeśli musisz naprawić konto usługi synchronizacji w chmurze, możesz użyć `Repair-AADCloudSyncToolsAccount` .  


   1.  Wykonaj kroki instalacji opisane [tutaj](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) , aby rozpocząć pracę, a następnie kontynuuj pozostałe kroki.
   2.  W sesji programu Windows PowerShell z uprawnieniami administracyjnymi wpisz lub skopiuj i wklej następujące elementy: 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. Wprowadź swoje poświadczenia administratora globalnego usługi Azure AD
   4. Wpisz lub skopiuj i wklej następujące elementy: 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. Po zakończeniu tego procesu należy się zastanowić, że konto zostało naprawione pomyślnie.

## <a name="next-steps"></a>Następne kroki 

- [Znane ograniczenia](how-to-prerequisites.md#known-limitations)
- [Kody błędów](reference-error-codes.md)
