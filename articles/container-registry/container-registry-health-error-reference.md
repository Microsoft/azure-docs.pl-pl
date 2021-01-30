---
title: Informacje o błędzie dotyczące kontroli kondycji rejestru
description: Kody błędów i możliwe rozwiązania problemów odnalezionych przez uruchomienie polecenia AZ ACR Check-Health Diagnostic w Azure Container Registry
ms.topic: article
ms.date: 01/25/2021
ms.openlocfilehash: 05ae5a7ac19bb7748d5313ccb4974b639ab52d9c
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99061872"
---
# <a name="health-check-error-reference"></a>Informacje o błędach sprawdzania kondycji

Poniżej znajdują się szczegółowe informacje o kodach błędów zwracanych przez polecenie [AZ ACR Check-Health][az-acr-check-health] . Dla każdego błędu są wyświetlane możliwe rozwiązania.

Aby uzyskać informacje o uruchamianiu programu `az acr check-healh` , zobacz [Sprawdzanie kondycji usługi Azure Container Registry](container-registry-check-health.md).

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Ten błąd oznacza, że nie można odnaleźć klienta platformy Docker dla interfejsu wiersza polecenia. W związku z tym następujące dodatkowe kontrole nie są uruchamiane: znajdowania wersji platformy Docker, oceniania stanu demona platformy Docker i uruchamiania polecenia Docker pull.

*Potencjalne rozwiązania*: Zainstaluj klienta platformy Docker; Dodaj ścieżkę Docker do zmiennych systemowych.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Ten błąd oznacza, że stan demona platformy Docker jest niedostępny lub nie można go osiągnąć przy użyciu interfejsu wiersza polecenia. W efekcie operacje platformy Docker (takie jak `docker login` i `docker pull` ) są niedostępne za pomocą interfejsu wiersza polecenia.

*Potencjalne rozwiązania*: ponownie uruchom demona platformy Docker lub sprawdź, czy jest ona prawidłowo zainstalowana.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie był w stanie uruchomić polecenie `docker --version` .

*Potencjalne rozwiązania*: Spróbuj uruchomić polecenie ręcznie, upewnij się, że masz najnowszą wersję interfejsu wiersza polecenia i Sprawdź komunikat o błędzie.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie może ściągnąć przykładowego obrazu do środowiska.

*Potencjalne rozwiązania*: Sprawdź, czy wszystkie składniki niezbędne do ściągnięcia obrazu działają prawidłowo.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Ten błąd oznacza, że nie można odnaleźć klienta Helm za pomocą interfejsu wiersza polecenia, co uniemożliwia wykonywanie innych operacji Helm.

*Potencjalne rozwiązania*: Upewnij się, że jest zainstalowany klient Helm i że jego ścieżka jest dodawana do systemowych zmiennych środowiskowych.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie może określić zainstalowanej wersji programu Helm. Taka sytuacja może wystąpić, jeśli używana wersja interfejsu wiersza polecenia platformy Azure (lub wersja Helm) jest przestarzała.

*Potencjalne rozwiązania*: zaktualizuj do najnowszej wersji interfejsu wiersza polecenia platformy Azure lub zalecaną wersję Helm; Uruchom polecenie ręcznie i Sprawdź komunikat o błędzie.

## <a name="cmk_error"></a>CMK_ERROR

Ten błąd oznacza, że rejestr nie może uzyskać dostępu do tożsamości zarządzanej przypisanej przez użytkownika lub sysem użytej do skonfigurowania szyfrowania rejestru przy użyciu klucza zarządzanego przez klienta. Tożsamość zarządzana mogła zostać usunięta.  

*Potencjalne rozwiązanie*: Aby rozwiązać problem i obrócić klucz przy użyciu innej tożsamości zarządzanej, zobacz kroki rozwiązywania problemów [z tożsamością przypisaną przez użytkownika](container-registry-customer-managed-keys.md#troubleshoot).

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Ten błąd oznacza, że serwer DNS dla danego serwera logowania rejestru został wysłany za pomocą polecenia ping, ale nie odpowiedział, co oznacza, że jest niedostępny. Może to wskazywać na problemy z łącznością. Alternatywnie rejestr może nie istnieć, użytkownik może nie mieć uprawnień do rejestru (w celu poprawnego pobrania serwera logowania) lub rejestr docelowy znajduje się w innej chmurze niż używany w interfejsie wiersza polecenia platformy Azure.

*Potencjalne rozwiązania*: Sprawdź poprawność łączności; Sprawdź pisownię rejestru i czy rejestr istnieje; Sprawdź, czy użytkownik ma odpowiednie uprawnienia i czy chmura rejestru jest taka sama, która jest używana w interfejsie wiersza polecenia platformy Azure.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Ten błąd oznacza, że punkt końcowy wyzwania dla danego rejestru odpowiedział z niedozwolonym stanem HTTP 403. Ten błąd oznacza, że użytkownicy nie mają dostępu do rejestru, najprawdopodobniej ze względu na konfigurację sieci wirtualnej lub że dostęp do publicznego punktu końcowego rejestru jest niedozwolony. Aby wyświetlić aktualnie skonfigurowane reguły zapory, uruchom polecenie `az acr show --query networkRuleSet --name <registry>` .

*Potencjalne rozwiązania*: Usuń reguły sieci wirtualnej lub Dodaj bieżący adres IP klienta do listy dozwolonych.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Ten błąd oznacza, że punkt końcowy wyzwania rejestru docelowego nie wystawia wyzwania.

*Potencjalne rozwiązania*: spróbuj ponownie za jakiś czas. Jeśli błąd będzie się powtarzać, Otwórz problem pod adresem https://aka.ms/acr/issues .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Ten błąd oznacza, że punkt końcowy wyzwania rejestru docelowego wygenerował wyzwanie, ale rejestr nie obsługuje uwierzytelniania Azure Active Directory.

*Potencjalne rozwiązania*: wypróbuj inny sposób uwierzytelniania, na przykład z poświadczeniami administratora. Jeśli użytkownicy muszą uwierzytelniać się przy użyciu Azure Active Directory, należy otworzyć problem o https://aka.ms/acr/issues .

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Ten błąd oznacza, że serwer logowania rejestru nie odpowiedział z tokenem odświeżania, więc odmowa dostępu do docelowego rejestru. Ten błąd może wystąpić, jeśli użytkownik nie ma odpowiednich uprawnień do rejestru lub jeśli poświadczenia użytkownika dla interfejsu wiersza polecenia platformy Azure są przestarzałe.

*Potencjalne rozwiązania*: Sprawdź, czy użytkownik ma odpowiednie uprawnienia do rejestru; Uruchom `az login` polecenie, aby odświeżyć uprawnienia, tokeny i poświadczenia.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Ten błąd oznacza, że serwer logowania do rejestru nie odpowiedział z tokenem dostępu, aby uzyskać dostęp do docelowego rejestru. Ten błąd może wystąpić, jeśli użytkownik nie ma odpowiednich uprawnień do rejestru lub jeśli poświadczenia użytkownika dla interfejsu wiersza polecenia platformy Azure są przestarzałe.

*Potencjalne rozwiązania*: Sprawdź, czy użytkownik ma odpowiednie uprawnienia do rejestru; Uruchom `az login` polecenie, aby odświeżyć uprawnienia, tokeny i poświadczenia.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Ten błąd oznacza, że klient nie mógł nawiązać bezpiecznego połączenia z rejestrem kontenerów. Ten błąd występuje na ogół w przypadku korzystania z serwera proxy lub korzystania z niego.

*Potencjalne rozwiązania*: więcej informacji na temat pracy za serwerem proxy można [znaleźć tutaj](/cli/azure/use-cli-effectively).

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie może znaleźć serwera logowania danego rejestru i nie znaleziono domyślnego sufiksu dla bieżącej chmury. Ten błąd może wystąpić, jeśli rejestr nie istnieje, jeśli użytkownik nie ma odpowiednich uprawnień do rejestru, jeśli chmura rejestru i bieżąca chmura interfejsu wiersza polecenia platformy Azure nie są zgodne lub jeśli wersja interfejsu wiersza polecenia platformy Azure jest przestarzała.

*Potencjalne rozwiązania*: Sprawdź, czy pisownia jest poprawna i czy rejestr istnieje; Upewnij się, że użytkownik ma odpowiednie uprawnienia do rejestru oraz że chmury rejestru i środowiska interfejsu wiersza polecenia pasują do siebie. Zaktualizuj interfejs wiersza polecenia platformy Azure do najnowszej wersji.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie jest zgodny z aktualnie zainstalowaną wersją platformy Docker/notariusz. Aby rozwiązać ten problem, Wypróbuj wersję notary.exe wersji starszej niż 0.6.0 przez zastąpienie klienta programu Docker Installation.

## <a name="next-steps"></a>Następne kroki

Aby sprawdzić kondycję rejestru, zobacz [Sprawdzanie kondycji usługi Azure Container Registry](container-registry-check-health.md).

Zapoznaj się z [często](container-registry-faq.md) zadawanymi pytaniami i innymi znanymi problemami dotyczącymi Azure Container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
