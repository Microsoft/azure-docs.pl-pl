---
title: Azure AD Connect kodów błędów i opisach usługi Cloud Sync
description: Artykuł referencyjny dotyczący kodów błędów synchronizacji w chmurze
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/14/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0b1dbd9064e24327f129e8b8f957414d9162386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650944"
---
# <a name="azure-ad-connect-cloud-sync-error-codes-and-descriptions"></a>Azure AD Connect kodów błędów i opisach usługi Cloud Sync
Poniżej przedstawiono listę kodów błędów i ich opis


## <a name="error-codes"></a>Kody błędów

|Kod błędu|Szczegóły|Scenariusz|Rozwiązanie|
|-----|-----|-----|-----|
|Limit czasu|Komunikat o błędzie: wykryto błąd limitu czasu żądania podczas kontaktowania się z agentem lokalnym i synchronizowania konfiguracji. Dodatkowe problemy związane z agentem synchronizacji w chmurze można znaleźć w temacie Wskazówki dotyczące rozwiązywania problemów.|Upłynął limit czasu żądania. Bieżąca wartość limitu czasu to 10 minut.|Zobacz nasze [wskazówki dotyczące rozwiązywania problemów](how-to-troubleshoot.md)|
|HybridSynchronizationActiveDirectoryInternalServerError|Komunikat o błędzie: nie można przetworzyć tego żądania w tym momencie. Jeśli ten problem będzie się powtarzać, skontaktuj się z pomocą techniczną i podaj następujący identyfikator zadania: AD2AADProvisioning. 30b500eaf9c643b2b78804e80c1421fe. 5c291d3c-d29f-4570-9d6b-f0c2fa3d5926. Dodatkowe szczegóły: przetwarzanie żądania HTTP spowodowało wyjątek. |Nie można przetworzyć parametrów odebranych w żądaniu Standard scim do żądania wyszukiwania.|Aby uzyskać szczegółowe informacje, sprawdź odpowiedź HTTP zwróconą przez właściwość "Response" tego wyjątku.|
|HybridIdentityServiceNoAgentsAssigned|Komunikat o błędzie: nie można odnaleźć aktywnego agenta dla domeny, którą próbujesz zsynchronizować. Sprawdź, czy agenci zostali usunięci. Jeśli tak, ponownie zainstaluj agenta ponownie.|Brak uruchomionych agentów. Prawdopodobnie agenci zostali usunięci. Zarejestruj nowego agenta.|"W tym przypadku nie będzie widoczny żaden Agent przypisany do domeny w portalu.|
|HybridIdentityServiceNoActiveAgents|Komunikat o błędzie: nie można odnaleźć aktywnego agenta dla domeny, którą próbujesz zsynchronizować. Sprawdź, czy Agent jest uruchomiony, przechodząc do serwera, na którym zainstalowano agenta, i sprawdź, czy w obszarze usługi są uruchomione "Microsoft Azure AD agenta synchronizacji w chmurze".|"Agenci nie nasłuchują punktu końcowego ServiceBus. [Agent znajduje się za zaporą, która nie zezwala na połączenia z usługą Service Bus](../../active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers.md#use-the-outbound-proxy-server)|
|HybridIdentityServiceInvalidResource|Komunikat o błędzie: nie można przetworzyć tego żądania w tym momencie. Jeśli ten problem będzie się powtarzać, skontaktuj się z pomocą techniczną i podaj następujący identyfikator zadania: AD2AADProvisioning. 3a2a0d8418f34f54a03da5b70b1f7b0c. d583d090-9cd3-4d0a-aee6-8d666658c3e9. Dodatkowe szczegóły: prawdopodobnie wystąpił problem z konfiguracją synchronizacji z chmurą. Zarejestruj ponownie agenta synchronizacji chmury w domenie usługi AD Premium i uruchom ponownie konfigurację z poziomu witryny Azure Portal.|Nazwa zasobu musi być ustawiona tak, aby wiedział, z którym agentem skontaktował się.|Zarejestruj ponownie agenta synchronizacji chmury w domenie usługi AD Premium i uruchom ponownie konfigurację z poziomu witryny Azure Portal.|
|HybridIdentityServiceAgentSignalingError|Komunikat o błędzie: nie można przetworzyć tego żądania w tym momencie. Jeśli ten problem będzie się powtarzać, skontaktuj się z pomocą techniczną i podaj następujący identyfikator zadania: AD2AADProvisioning. 92d2e8750f37407fa2301c9e52ad7e9b. efb835ef-62e8-42e3-B495-18d5272eb3f9. Dodatkowe szczegóły: nie można przetworzyć tego żądania w tym momencie. Jeśli ten problem będzie się powtarzać, skontaktuj się z pomocą techniczną, podając identyfikator zadania (z okienka stanu konfiguracji).|Service Bus nie może wysłać komunikatu do agenta. Może to być awaria usługi Service Bus lub Agent nie odpowiada.|Jeśli ten problem będzie się powtarzać, skontaktuj się z pomocą techniczną, podając identyfikator zadania (z okienka stanu konfiguracji).|
|AzureDirectoryServiceServerBusy|Komunikat o błędzie: Wystąpił błąd. Kod błędu: 81. Opis błędu: Azure Active Directory jest obecnie zajęty. Ta operacja zostanie ponowiona automatycznie. Jeśli ten problem będzie się powtarzał przez ponad 24 godziny, skontaktuj się z pomocą techniczną. Identyfikator śledzenia: Nazwa serwera 8a4ab3b5-3664-4278-ab64-9cff37fd3f4f:|Azure Active Directory jest obecnie zajęta.|Jeśli ten problem będzie się powtarzał przez ponad 24 godziny, skontaktuj się z pomocą techniczną.|
|AzureActiveDirectoryInvalidCredential|Komunikat o błędzie: znaleziono problem z kontem usługi używanym do uruchamiania Azure AD Connect synchronizacji w chmurze. Możesz naprawić konto usługi w chmurze, postępując zgodnie z instrukcjami w [tym miejscu](./how-to-troubleshoot.md). Jeśli błąd będzie się powtarzać, skontaktuj się z pomocą techniczną, podając identyfikator zadania (z okienka stanu konfiguracji). Dodatkowe szczegóły błędu: CredentialsInvalid AADSTS50034: konto użytkownika {EmailHidden} nie istnieje w katalogu skydrive365.onmicrosoft.com. Aby zalogować się do tej aplikacji, należy dodać konto do katalogu. Identyfikator śledzenia: identyfikator korelacji 14b63033-3bc9-4bd4-b871-5eb4b3500200:57d93ed1-be4d-483c-997c-a3b6f03deb00 sygnatura czasowa: 2021-01-12 21:08:29Z |Ten błąd jest zgłaszany, gdy konto usługi synchronizacji ADToAADSyncServiceAccount nie istnieje w dzierżawie. Może to być spowodowane przypadkowym usunięciem konta.|Użyj [Repair-AADCloudSyncToolsAccount](reference-powershell.md#repair-aadcloudsynctoolsaccount) , aby naprawić konto usługi.|
|AzureActiveDirectoryExpiredCredentials|Komunikat o błędzie: nie można przetworzyć tego żądania w tym momencie. Jeśli ten problem będzie się powtarzać, skontaktuj się z pomocą techniczną, podając identyfikator zadania (z okienka stanu konfiguracji). Dodatkowe szczegóły błędu: CredentialsExpired AADSTS50055: hasło wygasło. Identyfikator śledzenia: identyfikator korelacji 989b1841-dbe5-49c9-ab6c-9aa25f7b0e00:1c69b196-1c3a-4381-9187-c84747807155 timestamp: 2021-01-12 20:59:31Z | Kod stanu odpowiedzi nie wskazuje sukcesu: 401 (Brak autoryzacji).|Poświadczenia konta usługi AAD Sync wygasły.|Możesz naprawić konto usługi w chmurze, postępując zgodnie z instrukcjami w https://go.microsoft.com/fwlink/?linkid=2150988 . Jeśli błąd będzie się powtarzać, skontaktuj się z pomocą techniczną, podając identyfikator zadania (z okienka stanu konfiguracji).  Dodatkowe szczegóły błędu: poświadczenia dzierżawy Azure Active Directory administracyjnych zostały wymienione dla tokenu OAuth, który wygasł. "|
|AzureActiveDirectoryAuthenticationFailed|Komunikat o błędzie: nie można przetworzyć tego żądania w tym momencie. Jeśli ten problem będzie się powtarzać, skontaktuj się z pomocą techniczną i podaj następujący identyfikator zadania: AD2AADProvisioning. 60b943e88f234db2b887f8cb91dee87c. 707be0d2-c6a9-405d-a3b9-de87761dc3ac. Dodatkowe szczegóły: nie można przetworzyć tego żądania w tym momencie. Jeśli ten problem będzie się powtarzać, skontaktuj się z pomocą techniczną, podając identyfikator zadania (z okienka stanu konfiguracji). Dodatkowe szczegóły błędu: UnexpectedError.|Nieznany błąd.|Jeśli ten problem będzie się powtarzać, skontaktuj się z pomocą techniczną, podając identyfikator zadania (z okienka stanu konfiguracji).|

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest Azure AD Connect Sync Cloud?](what-is-cloud-sync.md)