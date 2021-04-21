---
title: Sprawdzanie kondycji rejestru
description: Dowiedz się, jak uruchomić szybkie polecenie diagnostyczne, aby zidentyfikować typowe problemy podczas korzystania z rejestru kontenerów platformy Azure, w tym lokalną konfigurację platformy Docker i łączność z rejestrem
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: fec05efe67f5c502f36ee90eec57ba283b15a4a0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761749"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Sprawdzanie kondycji rejestru kontenerów platformy Azure

Podczas korzystania z usługi Azure Container Registry mogą czasami wystąpić problemy. Na przykład może nie być możliwe ściągnięcie obrazu kontenera z powodu problemu z platformą Docker w środowisku lokalnym. Lub problem z siecią może uniemożliwić nawiązanie połączenia z rejestrem. 

W pierwszym kroku diagnostycznym uruchom polecenie [az acr check-health,][az-acr-check-health] aby uzyskać informacje o kondycji środowiska i opcjonalnie uzyskać dostęp do rejestru docelowego. To polecenie jest dostępne w interfejsie wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

Aby uzyskać dodatkowe wskazówki dotyczące rozwiązywania problemów z rejestrem, zobacz:
* [Rozwiązywanie problemów z logowaniem do rejestru](container-registry-troubleshoot-login.md)
* [Rozwiązywanie problemów z siecią za pomocą rejestru](container-registry-troubleshoot-access.md)
* [Rozwiązywanie problemów z wydajnością rejestru](container-registry-troubleshoot-performance.md)

## <a name="run-az-acr-check-health"></a>Uruchom az acr check-health

W przykładach poniżej przedstawiono różne sposoby uruchamiania `az acr check-health` polecenia.

> [!NOTE]
> Jeśli polecenie zostanie uruchomione w Azure Cloud Shell, środowisko lokalne nie zostanie zaznaczone. Można jednak sprawdzić dostęp do rejestru docelowego.

### <a name="check-the-environment-only"></a>Sprawdzanie tylko środowiska

Aby sprawdzić lokalny demon platformy Docker, wersję interfejsu wiersza polecenia i konfigurację klienta Helm, uruchom polecenie bez dodatkowych parametrów:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Sprawdzanie środowiska i rejestru docelowego

Aby sprawdzić dostęp do rejestru, a także sprawdzić środowisko lokalne, należy przekazać nazwę rejestru docelowego. Na przykład:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Raportowanie błędów

Polecenie rejestruje informacje w standardowych danych wyjściowych. Jeśli zostanie wykryty problem, będzie on zawierał kod błędu i opis. Aby uzyskać więcej informacji na temat kodów i możliwych rozwiązań, zobacz informacje [o błędach](container-registry-health-error-reference.md).

Domyślnie polecenie zatrzymuje się za każdym razem, gdy znajdzie błąd. Można również uruchomić polecenie , aby dostarczało dane wyjściowe dla wszystkich kontroli kondycji, nawet jeśli znaleziono błędy. Dodaj parametr `--ignore-errors` , jak pokazano w poniższych przykładach:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Przykładowe dane wyjściowe:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje o kodach błędów zwracanych przez [polecenie az acr check-health,][az-acr-check-health] zobacz informacje o błędach [kontroli kondycji](container-registry-health-error-reference.md).

Zobacz często [zadawane pytania,](container-registry-faq.md) aby uzyskać odpowiedzi na często zadawane pytania i inne znane problemy dotyczące Azure Container Registry.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
