---
title: Informacje o błędach dotyczące kontroli kondycji rejestru
description: Kody błędów i możliwe rozwiązania problemów znalezionych przez uruchomienie polecenia az acr check-health diagnostic w Azure Container Registry
ms.topic: article
ms.date: 01/25/2021
ms.openlocfilehash: f9716c29093ae58518bc86ec06af40522d49047c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773445"
---
# <a name="health-check-error-reference"></a>Informacje o błędach kontroli kondycji

Poniżej przedstawiono szczegółowe informacje o kodach błędów zwracanych przez [polecenie az acr check-health.][az-acr-check-health] Dla każdego błędu są wyświetlane możliwe rozwiązania.

Aby uzyskać informacje na temat uruchamiania usługi `az acr check-healh` , [zobacz Sprawdzanie kondycji rejestru kontenerów platformy Azure.](container-registry-check-health.md)

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Ten błąd oznacza, że nie można odnaleźć klienta platformy Docker dla interfejsu wiersza polecenia. W związku z tym następujące dodatkowe kontrole nie są uruchamiane: znajdowanie wersji platformy Docker, ocenianie stanu demona platformy Docker i uruchamianie polecenia ściągania platformy Docker.

*Potencjalne rozwiązania:* zainstaluj klienta platformy Docker; dodaj ścieżkę platformy Docker do zmiennych systemowych.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Ten błąd oznacza, że stan demona platformy Docker jest niedostępny lub że nie można do niego osiągnąć za pomocą interfejsu wiersza polecenia. W związku z tym operacje platformy Docker (takie jak `docker login` i ) są niedostępne za `docker pull` pośrednictwem interfejsu wiersza polecenia.

*Potencjalne rozwiązania:* Uruchom ponownie demona platformy Docker lub sprawdź, czy jest on poprawnie zainstalowany.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie mógł uruchomić polecenia `docker --version` .

*Potencjalne rozwiązania:* Spróbuj uruchomić polecenie ręcznie, upewnij się, że masz najnowszą wersję interfejsu wiersza polecenia, i zbadaj komunikat o błędzie.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie mógł ściągnąć przykładowego obrazu do środowiska.

*Potencjalne rozwiązania:* sprawdź, czy wszystkie składniki niezbędne do ściągnięcie obrazu działają prawidłowo.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie może odnaleźć klienta Helm, co wyklucza inne operacje programu Helm.

*Potencjalne rozwiązania:* Sprawdź, czy klient programu Helm jest zainstalowany i czy jego ścieżka jest dodawana do systemowych zmiennych środowiskowych.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie mógł określić zainstalowanej wersji programu Helm. Taka możliwość może wystąpić, jeśli używana wersja interfejsu wiersza polecenia platformy Azure (lub wersja programu Helm) jest przestarzała.

*Potencjalne rozwiązania:* aktualizacja do najnowszej wersji interfejsu wiersza polecenia platformy Azure lub zalecanej wersji programu Helm; Uruchom polecenie ręcznie i zbadaj komunikat o błędzie.

## <a name="cmk_error"></a>CMK_ERROR

Ten błąd oznacza, że rejestr nie może uzyskać dostępu do przypisanej przez użytkownika lub przypisanej przez system tożsamości zarządzanej używanej do konfigurowania szyfrowania rejestru przy użyciu klucza zarządzanego przez klienta. Tożsamość zarządzana mogła zostać usunięta.  

*Potencjalne rozwiązanie:* Aby rozwiązać ten problem i obrócić klucz przy użyciu innej tożsamości zarządzanej, zobacz kroki rozwiązywania problemów z [tożsamością przypisaną przez użytkownika.](container-registry-customer-managed-keys.md#troubleshoot)

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Ten błąd oznacza, że serwer DNS dla danego serwera logowania rejestru został ping, ale nie odpowiedział, co oznacza, że jest niedostępny. Może to wskazywać na pewne problemy z łącznością. Alternatywnie rejestr może nie istnieć, użytkownik może nie mieć uprawnień do rejestru (aby prawidłowo pobrać serwer logowania) lub rejestr docelowy znajduje się w innej chmurze niż ta używana w interfejsie wiersza polecenia platformy Azure.

*Potencjalne rozwiązania:* weryfikowanie łączności; weryfikowanie pisowni rejestru i czy rejestr istnieje; Sprawdź, czy użytkownik ma odpowiednie uprawnienia do niego i czy chmura rejestru jest taka sama, jak używana w interfejsie wiersza polecenia platformy Azure.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Ten błąd oznacza, że punkt końcowy wyzwania dla danego rejestru odpowiedział stanem HTTP 403 Zabronione. Ten błąd oznacza, że użytkownicy nie mają dostępu do rejestru, najprawdopodobniej z powodu konfiguracji sieci wirtualnej lub z powodu niedozwolonego dostępu do publicznego punktu końcowego rejestru. Aby wyświetlić aktualnie skonfigurowane reguły zapory, `az acr show --query networkRuleSet --name <registry>` uruchom .

*Potencjalne rozwiązania:* usuń reguły sieci wirtualnej lub dodaj bieżący adres IP klienta do listy dozwolonych.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Ten błąd oznacza, że punkt końcowy wyzwania rejestru docelowego nie wyzłonił wyzwania.

*Potencjalne rozwiązania:* Spróbuj ponownie po pewnym czasie. Jeśli błąd będzie się powtarzać, otwórz problem na stronie https://aka.ms/acr/issues .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Ten błąd oznacza, że punkt końcowy wyzwania rejestru docelowego wystawił wyzwanie, ale rejestr nie obsługuje Azure Active Directory uwierzytelniania.

*Potencjalne rozwiązania:* Wypróbuj inny sposób uwierzytelniania, na przykład przy użyciu poświadczeń administratora. Jeśli użytkownicy muszą uwierzytelniać się przy Azure Active Directory, otwórz problem na stronie https://aka.ms/acr/issues .

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Ten błąd oznacza, że serwer logowania rejestru nie odpowiedział przy użyciu tokenu odświeżania, dlatego odmówiono dostępu do rejestru docelowego. Ten błąd może wystąpić, jeśli użytkownik nie ma odpowiednich uprawnień do rejestru lub jeśli poświadczenia użytkownika dla interfejsu wiersza polecenia platformy Azure są nieaktualne.

*Potencjalne rozwiązania:* sprawdź, czy użytkownik ma odpowiednie uprawnienia do rejestru; Uruchom `az login` , aby odświeżyć uprawnienia, tokeny i poświadczenia.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Ten błąd oznacza, że serwer logowania rejestru nie odpowiedział przy użyciu tokenu dostępu, dlatego odmówiono dostępu do rejestru docelowego. Ten błąd może wystąpić, jeśli użytkownik nie ma odpowiednich uprawnień do rejestru lub jeśli poświadczenia użytkownika dla interfejsu wiersza polecenia platformy Azure są nieaktualne.

*Potencjalne rozwiązania:* sprawdź, czy użytkownik ma odpowiednie uprawnienia do rejestru; Uruchom `az login` , aby odświeżyć uprawnienia, tokeny i poświadczenia.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Ten błąd oznacza, że klient nie mógł nawiązać bezpiecznego połączenia z rejestrem kontenerów. Ten błąd zazwyczaj występuje, jeśli używasz serwera proxy lub serwera proxy.

*Potencjalne rozwiązania:* Więcej informacji na temat pracy za serwerem proxy można [znaleźć tutaj.](/cli/azure/use-cli-effectively)

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie mógł znaleźć serwera logowania dla danego rejestru i nie znaleziono domyślnego sufiksu dla bieżącej chmury. Ten błąd może wystąpić, jeśli rejestr nie istnieje, jeśli użytkownik nie ma odpowiednich uprawnień do rejestru, jeśli chmura rejestru i bieżąca chmura interfejsu wiersza polecenia platformy Azure nie są zgodne lub jeśli wersja interfejsu wiersza polecenia platformy Azure jest przestarzała.

*Potencjalne rozwiązania:* sprawdź, czy pisownia jest poprawna i czy rejestr istnieje; sprawdź, czy użytkownik ma odpowiednie uprawnienia do rejestru oraz czy chmury rejestru i środowiska interfejsu wiersza polecenia są zgodne; zaktualizuj interfejs wiersza polecenia platformy Azure do najnowszej wersji.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Ten błąd oznacza, że interfejs wiersza polecenia nie jest zgodny z aktualnie zainstalowaną wersją platformy Docker/Notary. Spróbuj zmienić wersję pakietu notary.exe na wersję wcześniejszą niż 0.6.0, zastępując ręcznie klienta notarnego instalacji platformy Docker, aby rozwiązać ten problem.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat opcji sprawdzania kondycji rejestru, zobacz [Sprawdzanie kondycji rejestru kontenerów platformy Azure.](container-registry-check-health.md)

Zobacz często [zadawane pytania,](container-registry-faq.md) aby uzyskać odpowiedzi na często zadawane pytania i inne znane problemy dotyczące Azure Container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
