---
title: Sprawdź kondycję rejestru
description: Dowiedz się, jak uruchomić szybkie polecenie diagnostyczne, aby zidentyfikować typowe problemy występujące podczas korzystania z usługi Azure Container Registry, w tym konfiguracji lokalnej platformy Docker i łączności z rejestrem.
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: f27a99818260553cbd7ba26158db0064c145a21f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88245387"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Sprawdzanie kondycji usługi Azure Container Registry

W przypadku korzystania z usługi Azure Container Registry czasami mogą wystąpić problemy. Na przykład może nie być możliwe odtworzenie obrazu kontenera z powodu problemu z platformą Docker w środowisku lokalnym. Problem z siecią może również uniemożliwić połączenie się z rejestrem. 

Jako pierwszy krok diagnostyczny Uruchom polecenie [AZ ACR Check-Health][az-acr-check-health] , aby uzyskać informacje o kondycji środowiska i opcjonalnie uzyskać dostęp do rejestru docelowego. To polecenie jest dostępne w interfejsie wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

Aby uzyskać dodatkowe wskazówki dotyczące rozwiązywania problemów z rejestrem, zobacz:
* [Rozwiązywanie problemów z logowaniem do rejestru](container-registry-troubleshoot-login.md)
* [Rozwiązywanie problemów z siecią przy użyciu rejestru](container-registry-troubleshoot-access.md)
* [Rozwiązywanie problemów z wydajnością rejestru](container-registry-troubleshoot-performance.md)

## <a name="run-az-acr-check-health"></a>Uruchom AZ ACR Check-Health

W poniższych przykładach pokazano różne sposoby uruchomienia `az acr check-health` polecenia.

> [!NOTE]
> Jeśli uruchomisz polecenie w Azure Cloud Shell, lokalne środowisko nie jest zaznaczone. Można jednak sprawdzić dostęp do rejestru docelowego.

### <a name="check-the-environment-only"></a>Sprawdź tylko środowisko

Aby sprawdzić lokalny demon platformy Docker, wersję interfejsu wiersza polecenia i konfigurację klienta Helm, uruchom polecenie bez dodatkowych parametrów:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Sprawdź środowisko i rejestr docelowy

Aby sprawdzić dostęp do rejestru, a także przeprowadzić testy środowiska lokalnego, należy przekazać nazwę docelowego rejestru. Na przykład:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Raportowanie błędów

Polecenie rejestruje informacje w standardowym wyjściu. Jeśli problem zostanie wykryty, zawiera kod błędu i opis. Aby uzyskać więcej informacji o kodach i możliwych rozwiązaniach, zobacz [Informacje o błędzie](container-registry-health-error-reference.md).

Domyślnie polecenie jest przerywane za każdym razem, gdy odnajdzie błąd. Możesz również uruchomić polecenie, aby dostarczać dane wyjściowe dla wszystkich kontroli kondycji, nawet jeśli zostaną znalezione błędy. Dodaj `--ignore-errors` parametr, jak pokazano w następujących przykładach:

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

Aby uzyskać szczegółowe informacje o kodach błędów zwracanych przez polecenie [AZ ACR Check-Health][az-acr-check-health] , zobacz [Informacje o błędzie sprawdzania kondycji](container-registry-health-error-reference.md).

Zapoznaj się z [często](container-registry-faq.md) zadawanymi pytaniami i innymi znanymi problemami dotyczącymi Azure Container Registry.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
