---
title: Azure AD Connect rozwiązywania problemów z synchronizacją chmury
description: W tym artykule opisano sposób rozwiązywania problemów, które mogą wystąpić w przypadku agenta aprowowania w chmurze.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 01/19/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 65022d98c7ee7e90d8f1fe5b6854605c841ad05b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530311"
---
# <a name="cloud-sync-troubleshooting"></a>Rozwiązywanie problemów z synchronizacją chmury

Synchronizacja z chmurą obejmuje wiele zagadnień i ma wiele różnych zależności. Ten szeroki zakres może przyczyniać się do powstawania różnych problemów. Ten artykuł ułatwia rozwiązywanie tych problemów. Przedstawiono w nim typowe obszary, na które należy zwrócić uwagę, sposób zbierania dodatkowych informacji oraz różne techniki umożliwiające śledzenie problemów.


## <a name="common-troubleshooting-areas"></a>Typowe obszary rozwiązywania problemów

|Nazwa|Opis|
|-----|-----|
|[Problemy z agentem](#agent-problems)|Sprawdź, czy agent został poprawnie zainstalowany i czy komunikuje się z Azure Active Directory (Azure AD).|
|[Problemy z synchronizacją obiektów](#object-synchronization-problems)|Użyj dzienników aprowizowania, aby rozwiązać problemy z synchronizacją obiektów.|
|[Problemy z aprowizowaniem poddane kwarantannie](#provisioning-quarantined-problems)|Opis problemów z aprowizowaniem kwarantanny i sposobu ich naprawiania.|


## <a name="agent-problems"></a>Problemy z agentem
Niektóre z pierwszych rzeczy, które chcesz zweryfikować za pomocą agenta, to:

-  Czy jest zainstalowany?
-  Czy agent działa lokalnie?
-  Czy agent znajduje się w portalu?
-  Czy agent jest oznaczony jako w dobrej kondycji?

Te elementy można zweryfikować w Azure Portal i na serwerze lokalnym, na którym działa agent.

### <a name="azure-portal-agent-verification"></a>Azure Portal weryfikacji agenta

Aby sprawdzić, czy agent jest widoczny na platformie Azure i czy jest w dobrej kondycji, wykonaj następujące kroki.

1. Zaloguj się w witrynie Azure Portal.
1. Po lewej stronie wybierz **pozycję Azure Active Directory**  >  **Azure AD Connect**. W środku wybierz pozycję Zarządzaj **synchronizacją.**
1. Na **ekranie Azure AD Connect synchronizacji chmury** wybierz pozycję **Przejrzyj wszystkich agentów.**

   ![Przegląd wszystkich agentów](media/how-to-install/install-7.png)</br>
 
1. Na **ekranie Lokalni agenci aprowi** ci widzą zainstalowanych agentów. Sprawdź, czy agent jest tam i czy jest oznaczony jako Healthy *(W dobrej kondycji).*

   ![Ekran lokalnych agentów aprowizowania](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>Weryfikowanie portu

Sprawdź, czy platforma Azure nasłuchuje na porcie 443 i czy agent może się z nim komunikować. 

Ten test sprawdza, czy agenci mogą komunikować się z platformą Azure za pośrednictwem portu 443. Otwórz przeglądarkę i przejdź do poprzedniego adresu URL z serwera, na którym zainstalowano agenta.

![Weryfikacja osiągalności portu](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>Na serwerze lokalnym

Aby sprawdzić, czy agent jest uruchomiony, wykonaj następujące kroki.

1. Na serwerze z zainstalowanym agentem otwórz okno **Usługi,** przechodząc do niego lub przechodząc do menu **Uruchom**  >    >  **usługi.msc.**
1. W **obszarze Usługi** upewnij się, że Microsoft Azure AD **updater** agenta i agent **Microsoft Azure AD Connect Provisioning Agent** znajdują się na tej stronie, a ich stan to *Uruchomiony.*

   ![Ekran usług](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>Typowe problemy z instalacją agenta

W poniższych sekcjach opisano niektóre typowe problemy z instalacją agenta i typowe rozwiązania.

#### <a name="agent-failed-to-start"></a>Nie można uruchomić agenta

Może zostać wyświetlony komunikat o błędzie informujący o tym, że:

**Nie można uruchomić Microsoft Azure AD usługi "Microsoft Azure AD Connect Provisioning Agent". Sprawdź, czy masz wystarczające uprawnienia do uruchamiania usług systemowych.** 

Ten problem jest zwykle spowodowany przez zasady grupy, które uniemożliwiały zastosowanie uprawnień do lokalnego konta logowania usługi NT utworzonego przez instalatora (NT SERVICE\AADConnectProvisioningAgent). Te uprawnienia są wymagane do uruchomienia usługi.

Aby rozwiązać ten problem, wykonaj następujące kroki.

1. Zaloguj się na serwerze przy użyciu konta administratora.
1. Otwórz **program Services,** przechodząc do niego lub przechodząc do menu **Uruchom**  >    >  **usługi.msc.**
1. W **obszarze Usługi** kliknij dwukrotnie pozycję Microsoft Azure AD Connect **Provisioning Agent**.
1. Na karcie **Logowanie zmień** pozycję **To konto** na administratora domeny. Następnie uruchom ponownie usługę. 

   ![Karta Logowanie](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Uchylił czas agenta lub certyfikat jest nieprawidłowy

Podczas próby zarejestrowania agenta może zostać wyświetlony następujący komunikat o błędzie.

![Komunikat o błędzie prze wychodzącym](media/how-to-troubleshoot/troubleshoot-4.png)

Ten problem zwykle występuje, ponieważ agent nie może nawiązać połączenia z usługą zarządzania tożsamościami hybrydowymi i wymaga skonfigurowania serwera proxy HTTP. Aby rozwiązać ten problem, skonfiguruj serwer proxy ruchu wychodzącego. 

Agent aprowizowania obsługuje korzystanie z serwera proxy ruchu wychodzącego. Można go skonfigurować, edytując plik konfiguracji agenta *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*. Dodaj do niego następujące wiersze pod koniec pliku tuż przed tagiem `</configuration>` zamykającym.
Zastąp zmienne i `[proxy-server]` `[proxy-port]` wartościami nazwy i portu serwera proxy.

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

#### <a name="agent-registration-fails-with-security-error"></a>Rejestracja agenta kończy się niepowodzeniem z błędem zabezpieczeń

Podczas instalowania agenta aprowowania w chmurze może zostać wyświetlony komunikat o błędzie.

Ten problem jest zwykle spowodowany tym, że agent nie może wykonać skryptów rejestracji programu PowerShell ze względu na lokalne zasady wykonywania programu PowerShell.

Aby rozwiązać ten problem, zmień zasady wykonywania programu PowerShell na serwerze. Zasady komputera i użytkownika muszą być ustawione jako *Niezdefiniowane lub* *RemoteSigned.* Jeśli zostanie ustawiona wartość *Unrestricted*(Nieograniczone), zostanie wyświetlony ten błąd. Aby uzyskać więcej informacji, zobacz [Zasady wykonywania programu PowerShell.](/powershell/module/microsoft.powershell.core/about/about_execution_policies) 

### <a name="log-files"></a>Pliki dziennika

Domyślnie agent generuje minimalną liczbę komunikatów o błędach i ilość informacji śledzenia stosu. Te dzienniki śledzenia można znaleźć w **folderze C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace**.

Aby zebrać dodatkowe szczegóły dotyczące rozwiązywania problemów związanych z agentem, wykonaj następujące kroki.

1.  Zainstaluj moduł AADCloudSyncTools programu PowerShell zgodnie z opisem [w tym miejscu.](reference-powershell.md#install-the-aadcloudsynctools-powershell-module)
2. Użyj polecenia `Export-AADCloudSyncToolsLogs` cmdlet programu PowerShell, aby przechwycić informacje.  Aby dostosować kolekcję danych, można użyć następujących przełączników.
      - SkipVerboseTrace do eksportowania tylko bieżących dzienników bez przechwytywania pełnych dzienników (wartość domyślna = false)
      - TracingDurationMins w celu określenia innego czasu trwania przechwytywania (wartość domyślna = 3 minuty)
      - OutputPath — określanie innej ścieżki wyjściowej (wartość domyślna = Dokumenty użytkownika)


## <a name="object-synchronization-problems"></a>Problemy z synchronizacją obiektów

W poniższej sekcji przedstawiono informacje dotyczące rozwiązywania problemów z synchronizacją obiektów.

### <a name="provisioning-logs"></a>Dzienniki aprowizowania

W Azure Portal aprowizowania dzienniki mogą być używane do śledzenia i rozwiązywania problemów z synchronizacją obiektów. Aby wyświetlić dzienniki, wybierz pozycję **Dzienniki.**

![Przycisk Dzienniki](media/how-to-troubleshoot/log-1.png)

Dzienniki aprowizowania zawierają wiele informacji na temat stanu obiektów synchronizowanych między środowiskiem lokalna usługa Active Directory a platformą Azure.

![Ekran dzienników aprowowania](media/how-to-troubleshoot/log-2.png)

Możesz użyć pól rozwijanych w górnej części strony, aby odfiltrować widok do zera w przypadku określonych problemów, takich jak daty. Kliknij dwukrotnie pojedyncze zdarzenie, aby wyświetlić dodatkowe informacje.

![Informacje o polu listy rozwijanej Dzienniki aprowiowania](media/how-to-troubleshoot/log-3.png)

Te informacje zawierają szczegółowe instrukcje i miejsca wystąpienia problemu z synchronizacją. W ten sposób można wskazać dokładne miejsce problemu.


## <a name="provisioning-quarantined-problems"></a>Problemy z aprowizowaniem poddane kwarantannie

Synchronizacja z chmurą monitoruje kondycję konfiguracji i umieszcza obiekty w złej kondycji w stanie kwarantanny. Jeśli większość lub wszystkie wywołania dotyczące systemu docelowego stale się nie powiodą z powodu błędu, na przykład nieprawidłowych poświadczeń administratora, zadanie synchronizacji zostanie oznaczone jako poddane kwarantannie.

![Stan kwarantanny](media/how-to-troubleshoot/quarantine-1.png)

Po wybraniu stanu można wyświetlić dodatkowe informacje o kwarantannie. Możesz również uzyskać kod błędu i komunikat.

![Zrzut ekranu przedstawiający dodatkowe informacje o kwarantannie.](media/how-to-troubleshoot/quarantine-2.png)

Kliknięcie prawym przyciskiem myszy stanu spowoduje dodatkowe opcje:
    
   - wyświetlanie dzienników aprowizowania
   - wyświetlanie agenta
   - wyczyść kwarantannę

![Zrzut ekranu przedstawiający opcje menu dostępne po kliknięciu prawym przyciskiem myszy.](media/how-to-troubleshoot/quarantine-4.png)


### <a name="resolve-a-quarantine"></a>Rozwiązywanie problemów z kwarantanną
Istnieją dwa różne sposoby rozwiązania kwarantanny.  Są to:

  - wyczyść kwarantannę — czyszczy limit i uruchamia synchronizację różnicową
  - uruchom ponownie zadanie aprowowania — powoduje wyczyszczenie limitu i uruchomienie synchronizacji początkowej

#### <a name="clear-quarantine"></a>Wyczyść kwarantannę
Aby wyczyścić limit i uruchomić synchronizację różnicową dla zadania aprowiwizowania po jego zweryfikowaniu, po prostu kliknij prawym przyciskiem myszy stan i wybierz pozycję **Wyczyść kwarantannę.**

Powinno zostać wyświetlony powiadomienie, że kwarantanna jest czyszcząca.

![Zrzut ekranu przedstawiający powiadomienie o wyczyszczeniu kwarantanny.](media/how-to-troubleshoot/quarantine-5.png)

Następnie na agencie powinien być wyświetlony stan "w dobrej kondycji".

![Informacje o stanie kwarantanny](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>Uruchom ponownie zadanie aprowowania
Użyj Azure Portal, aby ponownie uruchomić zadanie aprowowania. Na stronie konfiguracji agenta wybierz pozycję Uruchom **ponownie aprowizowanie.**

  ![Ponowne uruchamianie aprowowania](media/how-to-troubleshoot/quarantine-3.png)

- Użyj [Microsoft Graph, aby ponownie uruchomić zadanie aprowowania.](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true) Będziesz mieć pełną kontrolę nad tym, co uruchamiasz ponownie. Możesz wyczyścić:
  - Escrows ( w celu ponownego uruchomienia licznika środków, które są naliczane w kierunku stanu kwarantanny).
  - Kwarantanna, aby usunąć aplikację z kwarantanny.
  - Znaki wodne. 
  
  Użyj następującego żądania:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>Naprawianie konta usługi synchronizacji w chmurze
Jeśli musisz naprawić konto usługi synchronizacji w chmurze, możesz użyć `Repair-AADCloudSyncToolsAccount` .  


   1.  Aby rozpocząć, [](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) a następnie kontynuować pracę z pozostałymi krokami, należy wykonać kroki instalacji opisane tutaj.
   2.  W sesji Windows PowerShell z uprawnieniami administracyjnymi wpisz lub skopiuj i wklej następujące polecenie: 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. Wprowadzanie poświadczeń administratora globalnego usługi Azure AD
   4. Wpisz lub skopiuj i wklej następujące polecenie: 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. Po zakończeniu tego procesu powinno być wiadomo, że konto zostało naprawione pomyślnie.

## <a name="next-steps"></a>Następne kroki 

- [Znane ograniczenia](how-to-prerequisites.md#known-limitations)
- [Kody błędów](reference-error-codes.md)
