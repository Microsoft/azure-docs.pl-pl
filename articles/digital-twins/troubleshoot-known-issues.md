---
title: Znane problemy — Azure Digital bliźniaczych reprezentacji
description: Uzyskaj pomoc w rozpoznawaniu i eliminowaniu znanych problemów z usługą Azure Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 0c008061d2d4fafa96eda934d5026c92839a0bdb
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661490"
---
# <a name="known-issues-in-azure-digital-twins"></a>Znane problemy w usłudze Azure Digital bliźniaczych reprezentacji

Ten artykuł zawiera informacje o znanych problemach związanych z usługą Azure Digital bliźniaczych reprezentacji.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400 błąd klienta: Nieprawidłowe żądanie" w Cloud Shell

Polecenia w Cloud Shell mogą sporadycznie kończyć się niepowodzeniem z powodu błędu "400 błąd klienta: Nieprawidłowe żądanie adresu URL: http://localhost:50342/oauth2/token ", po którym następuje pełny ślad stosu.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Można to rozwiązać przez ponowne uruchomienie `az login` polecenia i zakończenie kolejnych kroków logowania.

Następnie powinno być możliwe ponowne uruchomienie polecenia.

### <a name="possible-causes"></a>Możliwe przyczyny

Jest to wynik znanego problemu w Cloud Shell: [*pobieranie tokenu z Cloud Shell sporadycznie kończy się niepowodzeniem z powodu błędu 400 klienta: Nieprawidłowe żądanie*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="missing-role-assignment-after-scripted-setup"></a>Brak przypisania roli po skonfigurowaniu skryptu

Niektórzy użytkownicy mogą napotkać problemy z częścią przypisywania ról [*: Konfigurowanie wystąpienia i uwierzytelniania (skrypt)*](how-to-set-up-instance-scripted.md). Skrypt nie wskazuje błędu, ale rola *właściciela Digital bliźniaczych reprezentacji (wersja zapoznawcza)* nie została pomyślnie przypisana do użytkownika i będzie mieć wpływ na możliwość tworzenia innych zasobów w dół.

Aby określić, czy przypisanie roli zostało pomyślnie skonfigurowane po uruchomieniu skryptu, postępuj zgodnie z instrukcjami w sekcji [*Weryfikuj przypisanie roli użytkownika*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) w artykule Instalatora. Jeśli użytkownik nie jest wyświetlany z tą rolą, ten problem wystąpi.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Aby rozwiązać ten problem, można skonfigurować przypisanie roli ręcznie przy użyciu interfejsu wiersza polecenia lub Azure Portal. 

Wykonaj następujące instrukcje:
* [Interfejs wiersza polecenia](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>Możliwe przyczyny

W przypadku użytkowników zalogowanych przy użyciu osobistego [konto Microsoft (MSA)](https://account.microsoft.com/account)Identyfikator podmiotu zabezpieczeń użytkownika, który identyfikuje użytkownika w poleceniach, takich jak ten, może być inny niż adres e-mail, dzięki czemu skrypt ma być trudny do odnalezienia i użycia w celu poprawnego przypisania roli.

## <a name="issue-with-interactive-browser-authentication"></a>Problem z uwierzytelnianiem interakcyjnej przeglądarki

Podczas pisania kodu uwierzytelniania w aplikacjach Digital bliźniaczych reprezentacji platformy Azure przy użyciu najnowszej wersji (wersja **1.2.0**) **biblioteki [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) **mogą wystąpić problemy z metodą [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) .

Ta metoda jest używana w następujących artykułach: 
* [*Samouczek: kod aplikacji klienckiej*](tutorial-code.md)
* [*Instrukcje: zapisywanie kodu uwierzytelniania aplikacji*](how-to-authenticate-client.md)
* [*Instrukcje: korzystanie z interfejsów API i zestawów SDK Digital bliźniaczych reprezentacji na platformie Azure*](how-to-use-apis-sdks.md)

Problem zawiera odpowiedź o błędzie "Azure. Identity. AuthenticationFailedException" podczas próby uwierzytelnienia w oknie przeglądarki. Uruchomienie okna przeglądarki może zakończyć się niepowodzeniem lub pojawić się w celu pomyślnego uwierzytelnienia użytkownika, podczas gdy aplikacja kliencka nadal kończy się niepowodzeniem z powodu błędu.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Aby można było rozwiązać ten problem, aplikacja musi jawnie korzystać z platformy Azure. tożsamość w wersji **1.1.1**. W tej wersji biblioteki przeglądarka powinna ładować i uwierzytelniać się zgodnie z oczekiwaniami.

>[!NOTE]
> Nie wystarczy dodać biblioteki bez określenia żadnej wersji, ponieważ będzie ona nadal domyślną **1.2.0**. Należy jawnie określić wersję **1.1.1** .

### <a name="possible-causes"></a>Możliwe przyczyny

Jest to związane z otwartym problemem z najnowszą wersją biblioteki Azure. Identity Library (wersja **1.2.0**): [*nie można uwierzytelnić przy użyciu InteractiveBrowserCredential*](https://github.com/Azure/azure-sdk-for-net/issues/13940).

Ten problem zostanie wyświetlony, jeśli używasz wersji **1.2.0** w aplikacji Digital bliźniaczych reprezentacji platformy Azure lub biblioteka zostanie dodana do projektu bez określenia wersji (tak, że jest również wartością domyślną w tej najnowszej wersji).

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat zabezpieczeń i uprawnień w usłudze Azure Digital bliźniaczych reprezentacji:
* [*Koncepcje: zabezpieczenia rozwiązań Digital bliźniaczych reprezentacji na platformie Azure*](concepts-security.md)