---
title: Znane problemy — Azure Digital bliźniaczych reprezentacji
description: Uzyskaj pomoc w rozpoznawaniu i eliminowaniu znanych problemów z usługą Azure Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 49c790ae92537ab72fb9848ed4e57e222ef11d79
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545687"
---
# <a name="known-issues-in-azure-digital-twins"></a>Znane problemy w usłudze Azure Digital bliźniaczych reprezentacji

Ten artykuł zawiera informacje o znanych problemach związanych z usługą Azure Digital bliźniaczych reprezentacji.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400 błąd klienta: Nieprawidłowe żądanie" w Cloud Shell

**Opis problemu:** Polecenia w Cloud Shell uruchomione w systemie *https://shell.azure.com* mogą sporadycznie kończyć się niepowodzeniem z powodu błędu "400 błąd klienta: Nieprawidłowe żądanie dla adresu URL: http://localhost:50342/oauth2/token ", po którym następuje pełny ślad stosu.

| Czy ma to wpływ na mnie? | Przyczyna | Rozwiązanie |
| --- | --- | --- |
| W &nbsp; usłudze Azure &nbsp; Digital &nbsp; bliźniaczych reprezentacji ma to wpływ na następujące grupy poleceń:<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | Jest to wynik znanego problemu w Cloud Shell: [*pobieranie tokenu z Cloud Shell sporadycznie kończy się niepowodzeniem z powodu błędu 400 klienta: Nieprawidłowe żądanie*](https://github.com/Azure/azure-cli/issues/11749).<br><br>Powoduje to problem z tokenami uwierzytelniania wystąpienia usługi Azure Digital bliźniaczych reprezentacji oraz domyślnym uwierzytelnianiem opartym na [tożsamościach zarządzanych](../active-directory/managed-identities-azure-resources/overview.md) przez Cloud Shell. <br><br>Nie ma to wpływu na polecenia usługi Azure Digital bliźniaczych reprezentacji z `az dt` `az dt endpoint` grup poleceń lub, ponieważ używają one innego typu tokenu uwierzytelniania (opartego na Azure Resource Manager), który nie ma problemu z uwierzytelnianiem tożsamości zarządzanej Cloud Shell. | Jednym ze sposobów na rozwiązanie tego problemu jest ponowne uruchomienie `az login` polecenia w Cloud Shell i wykonanie kolejnych kroków logowania. Spowoduje to przełączenie sesji uwierzytelniania tożsamości zarządzanej, co pozwala uniknąć problemu z elementem głównym. Następnie powinno być możliwe ponowne uruchomienie polecenia.<br><br>Alternatywnie możesz otworzyć okienko Cloud Shell w Azure Portal i zakończyć pracę Cloud Shell z tego miejsca.<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Obraz ikony Cloud Shell na pasku ikon Azure Portal" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>Na koniec inne rozwiązanie polega na [zainstalowaniu interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) na komputerze, aby umożliwić lokalne uruchamianie poleceń interfejsu CLI platformy Azure. Ten problem nie występuje w lokalnym interfejsie wiersza polecenia. |


## <a name="missing-role-assignment-after-scripted-setup"></a>Brak przypisania roli po skonfigurowaniu skryptu

**Opis problemu:** Niektórzy użytkownicy mogą napotkać problemy z częścią przypisywania ról [*: Konfigurowanie wystąpienia i uwierzytelniania (skrypt)*](how-to-set-up-instance-scripted.md). Skrypt nie wskazuje błędu, ale rola *właściciela danych Digital bliźniaczych reprezentacji systemu Azure* nie została pomyślnie przypisana do użytkownika, a ten problem będzie miał wpływ na możliwość tworzenia innych zasobów w dół.

| Czy ma to wpływ na mnie? | Przyczyna | Rozwiązanie |
| --- | --- | --- |
| Aby określić, czy przypisanie roli zostało pomyślnie skonfigurowane po uruchomieniu skryptu, postępuj zgodnie z instrukcjami w sekcji [*Weryfikuj przypisanie roli użytkownika*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) w artykule Instalatora. Jeśli użytkownik nie jest wyświetlany z tą rolą, ten problem wystąpi. | W przypadku użytkowników zalogowanych przy użyciu osobistego [konto Microsoft (MSA)](https://account.microsoft.com/account)Identyfikator podmiotu zabezpieczeń użytkownika, który identyfikuje użytkownika w poleceniach, takich jak ten, może być inny niż adres e-mail logowania użytkowników, utrudniając skryptowi odnalezienie i użycie w celu poprawnego przypisania roli. | Aby rozwiązać ten problem, można skonfigurować przypisanie roli ręcznie przy użyciu [instrukcji interfejsu wiersza polecenia](how-to-set-up-instance-cli.md#set-up-user-access-permissions) lub [instrukcji Azure Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions). |

## <a name="issue-with-interactive-browser-authentication-on-azureidentity-120"></a>Problem z uwierzytelnianiem interakcyjnym przeglądarki na platformie Azure. 1.2.0 tożsamości

**Opis problemu:** Podczas pisania kodu uwierzytelniania w aplikacjach Digital bliźniaczych reprezentacji platformy Azure przy użyciu wersji **1.2.0** biblioteki **[Azure. Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)** mogą wystąpić problemy z metodą [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) . Jest to odpowiedź na błąd "Azure. Identity. AuthenticationFailedException" podczas próby uwierzytelnienia w oknie przeglądarki. Uruchomienie okna przeglądarki może zakończyć się niepowodzeniem lub pojawić się w celu pomyślnego uwierzytelnienia użytkownika, podczas gdy aplikacja kliencka nadal kończy się niepowodzeniem z powodu błędu.

| Czy ma to wpływ na mnie? | Przyczyna | Rozwiązanie |
| --- | --- | --- |
| Ta &nbsp; &nbsp; Metoda &nbsp; jest &nbsp; używana &nbsp; w &nbsp; &nbsp; następujących artykułach:<br><br>[*Samouczek: kod aplikacji klienckiej*](tutorial-code.md)<br><br>[*Instrukcje: zapisywanie kodu uwierzytelniania aplikacji*](how-to-authenticate-client.md)<br><br>[*Instrukcje: korzystanie z interfejsów API i zestawów SDK Digital bliźniaczych reprezentacji na platformie Azure*](how-to-use-apis-sdks.md) | Niektórzy użytkownicy mieli ten problem z wersją **1.2.0** `Azure.Identity` biblioteki. | Aby rozwiązać ten problem, zaktualizuj aplikacje tak, aby korzystały z [nowszej wersji](https://www.nuget.org/packages/Azure.Identity) programu `Azure.Identity` . Po zaktualizowaniu wersji biblioteki przeglądarka powinna ładować i uwierzytelniać się zgodnie z oczekiwaniami. |

## <a name="issue-with-default-azure-credential-authentication-on-azureidentity-130"></a>Problem z domyślnym uwierzytelnianiem poświadczeń platformy Azure na platformie Azure. tożsamość 1.3.0

**Opis problemu:** Podczas pisania kodu uwierzytelniania przy użyciu wersji **1.3.0** biblioteki **[Azure. Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)** niektórzy użytkownicy napotykali problemy z metodą [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet?view=azure-dotnet&preserve-view=true) używaną w wielu przykładach w tej dokumentacji Digital bliźniaczych reprezentacji for Azure. Jest to odpowiedź na błąd "Azure. Identity. AuthenticationFailedException: SharedTokenCacheCredential Authentication nie powiodła się", gdy kod próbuje się uwierzytelnić.

| Czy ma to wpływ na mnie? | Przyczyna | Rozwiązanie |
| --- | --- | --- |
| `DefaultAzureCredential` jest używany w większości przykładów dokumentacji dla tej usługi, która obejmuje uwierzytelnianie. W przypadku pisania kodu uwierzytelniania przy użyciu programu `DefaultAzureCredential` z wersją 1.3.0 `Azure.Identity` biblioteki i wyświetlenia tego komunikatu o błędzie ma to wpływ na użytkownika. | Jest to prawdopodobnie spowodowane jakimś problemem z konfiguracją programu `Azure.Identity` . | Jedną z strategii rozwiązania tego problemu jest wykluczenie `SharedTokenCacheCredential` z poświadczeń, zgodnie z opisem w tym artykule [DefaultAzureCredential](https://github.com/Azure/azure-sdk/issues/1970) , który jest aktualnie otwarty `Azure.Identity` .<br>Inną opcją jest zmiana aplikacji tak, aby korzystała z wcześniejszej wersji programu `Azure.Identity` , takiej jak [wersja 1.2.3](https://www.nuget.org/packages/Azure.Identity/1.2.3). Nie ma to wpływu na funkcjonalność usługi Azure Digital bliźniaczych reprezentacji, więc jest to również zaakceptowane rozwiązanie. |

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat zabezpieczeń i uprawnień w usłudze Azure Digital bliźniaczych reprezentacji:
* [*Koncepcje: zabezpieczenia rozwiązań Digital bliźniaczych reprezentacji na platformie Azure*](concepts-security.md)