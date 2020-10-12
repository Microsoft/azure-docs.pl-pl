---
title: Znane problemy — Azure Digital bliźniaczych reprezentacji
description: Uzyskaj pomoc w rozpoznawaniu i eliminowaniu znanych problemów z usługą Azure Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: e152c0227008dd12088660b2390a8d0a5f54de96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89290782"
---
# <a name="known-issues-in-azure-digital-twins"></a>Znane problemy w usłudze Azure Digital bliźniaczych reprezentacji

Ten artykuł zawiera informacje o znanych problemach związanych z usługą Azure Digital bliźniaczych reprezentacji.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400 błąd klienta: Nieprawidłowe żądanie" w Cloud Shell

Polecenia w Cloud Shell mogą sporadycznie kończyć się niepowodzeniem z powodu błędu "400 błąd klienta: Nieprawidłowe żądanie adresu URL: http://localhost:50342/oauth2/token ", po którym następuje pełny ślad stosu.

W odniesieniu do usługi Azure Digital bliźniaczych reprezentacji w systemie ma to wpływ na następujące grupy poleceń:
* `az dt route`
* `az dt model`
* `az dt twin`

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Można rozwiązać ten problem, uruchamiając `az login` polecenie w Cloud Shell i wykonując kolejne kroki logowania. Następnie powinno być możliwe ponowne uruchomienie polecenia.

Alternatywnym rozwiązaniem jest [zainstalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) na swoim komputerze, aby można było uruchomić polecenie interfejsu CLI platformy Azure lokalnie. Ten problem nie występuje w lokalnym interfejsie wiersza polecenia.

### <a name="possible-causes"></a>Możliwe przyczyny

Jest to wynik znanego problemu w Cloud Shell: [*pobieranie tokenu z Cloud Shell sporadycznie kończy się niepowodzeniem z powodu błędu 400 klienta: Nieprawidłowe żądanie*](https://github.com/Azure/azure-cli/issues/11749).

Powoduje to problem z tokenami uwierzytelniania wystąpienia usługi Azure Digital bliźniaczych reprezentacji oraz domyślnym uwierzytelnianiem opartym na [tożsamościach zarządzanych](../active-directory/managed-identities-azure-resources/overview.md) przez Cloud Shell. Krok rozwiązywania problemów z uruchamianiem `az login` przełączników z uwierzytelniania tożsamości zarządzanej, tym samym przechodzenie przez ten problem.

Nie ma to wpływu na polecenia usługi Azure Digital bliźniaczych reprezentacji z `az dt` `az dt endpoint` grup poleceń lub, ponieważ używają one innego typu tokenu uwierzytelniania (opartego na architekturze ARM), który nie ma problemu z uwierzytelnianiem tożsamości zarządzanej Cloud Shell.

## <a name="missing-role-assignment-after-scripted-setup"></a>Brak przypisania roli po skonfigurowaniu skryptu

Niektórzy użytkownicy mogą napotkać problemy z częścią przypisywania ról [*: Konfigurowanie wystąpienia i uwierzytelniania (skrypt)*](how-to-set-up-instance-scripted.md). Skrypt nie wskazuje błędu, ale rola *właściciela Digital bliźniaczych reprezentacji (wersja zapoznawcza)* nie została pomyślnie przypisana do użytkownika, a ten problem będzie miał wpływ na możliwość tworzenia innych zasobów w dół.

Aby określić, czy przypisanie roli zostało pomyślnie skonfigurowane po uruchomieniu skryptu, postępuj zgodnie z instrukcjami w sekcji [*Weryfikuj przypisanie roli użytkownika*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) w artykule Instalatora. Jeśli użytkownik nie jest wyświetlany z tą rolą, ten problem wystąpi.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Aby rozwiązać ten problem, można skonfigurować przypisanie roli ręcznie przy użyciu interfejsu wiersza polecenia lub Azure Portal. 

Wykonaj następujące instrukcje:
* [Interfejs wiersza polecenia](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>Możliwe przyczyny

W przypadku użytkowników zalogowanych przy użyciu osobistego [konto Microsoft (MSA)](https://account.microsoft.com/account)Identyfikator podmiotu zabezpieczeń użytkownika, który identyfikuje użytkownika w poleceniach, takich jak ten, może być inny niż adres e-mail logowania użytkowników, utrudniając skryptowi odnalezienie i użycie w celu poprawnego przypisania roli.

## <a name="issue-with-interactive-browser-authentication"></a>Problem z uwierzytelnianiem interakcyjnej przeglądarki

Podczas pisania kodu uwierzytelniania w aplikacjach Digital bliźniaczych reprezentacji platformy Azure przy użyciu wersji **1.2.0** biblioteki ** [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) **mogą wystąpić problemy z metodą [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) .

To nie jest Najnowsza wersja biblioteki. Najnowsza wersja to **1.2.2**.

Ta metoda jest używana w następujących artykułach: 
* [*Samouczek: kod aplikacji klienckiej*](tutorial-code.md)
* [*Instrukcje: zapisywanie kodu uwierzytelniania aplikacji*](how-to-authenticate-client.md)
* [*Instrukcje: korzystanie z interfejsów API i zestawów SDK Digital bliźniaczych reprezentacji na platformie Azure*](how-to-use-apis-sdks.md)

Problem zawiera odpowiedź o błędzie "Azure. Identity. AuthenticationFailedException" podczas próby uwierzytelnienia w oknie przeglądarki. Uruchomienie okna przeglądarki może zakończyć się niepowodzeniem lub pojawić się w celu pomyślnego uwierzytelnienia użytkownika, podczas gdy aplikacja kliencka nadal kończy się niepowodzeniem z powodu błędu.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Aby rozwiązać ten problem, zaktualizuj aplikacje do `Azure.Identity` wersji **1.2.2**. W tej wersji biblioteki przeglądarka powinna ładować i uwierzytelniać się zgodnie z oczekiwaniami.

### <a name="possible-causes"></a>Możliwe przyczyny

Jest to związane z otwartym problemem z najnowszą wersją `Azure.Identity` biblioteki (wersja **1.2.0**): [*nie można uwierzytelnić przy użyciu InteractiveBrowserCredential*](https://github.com/Azure/azure-sdk-for-net/issues/13940).

Ten problem zostanie wyświetlony, jeśli używasz wersji **1.2.0** w aplikacji Digital bliźniaczych reprezentacji platformy Azure lub biblioteka zostanie dodana do projektu bez określenia wersji (tak, że jest również wartością domyślną w tej najnowszej wersji).

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat zabezpieczeń i uprawnień w usłudze Azure Digital bliźniaczych reprezentacji:
* [*Koncepcje: zabezpieczenia rozwiązań Digital bliźniaczych reprezentacji na platformie Azure*](concepts-security.md)