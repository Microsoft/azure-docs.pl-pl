---
title: Często zadawane pytania
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure Container Registry service
author: sajayantony
ms.topic: article
ms.date: 03/15/2021
ms.author: sajaya
ms.openlocfilehash: a8c007d7f4419ddbe1555b50ceb6fb92ea0a6f98
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783903"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Często zadawane pytania dotyczące Azure Container Registry

Ten artykuł zawiera odpowiedzi na często zadawane pytania i znane problemy dotyczące Azure Container Registry.

Aby uzyskać wskazówki dotyczące rozwiązywania problemów z rejestrem, zobacz:
* [Rozwiązywanie problemów z logowaniem do rejestru](container-registry-troubleshoot-login.md)
* [Rozwiązywanie problemów z siecią za pomocą rejestru](container-registry-troubleshoot-access.md)
* [Rozwiązywanie problemów z wydajnością rejestru](container-registry-troubleshoot-performance.md)

## <a name="resource-management"></a>Zarządzanie zasobami

- [Czy mogę utworzyć rejestr kontenerów platformy Azure przy użyciu Resource Manager szablonu?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Czy istnieje skanowanie luk w zabezpieczeniach w celu skanowania obrazów w uciece ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Jak mogę skonfigurować usługę Kubernetes przy użyciu Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Jak mogę uzyskać poświadczenia administratora dla rejestru kontenerów?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Jak mogę uzyskać poświadczenia administratora w szablonie Resource Manager szablonu?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Usuwanie replikacji kończy się niepowodzeniem ze stanem Zabronione, mimo że replikacja jest usuwana przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Reguły zapory zostały pomyślnie zaktualizowane, ale nie obowiązują](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Czy mogę utworzyć szablon Azure Container Registry przy użyciu Resource Manager szablonu?

Tak. Oto [szablon,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) który umożliwia utworzenie rejestru.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Czy istnieje skanowanie luk w zabezpieczeniach w celu skanowania obrazów w uciece ACR?

Tak. Zapoznaj się z [dokumentacją](../security-center/defender-for-container-registries-introduction.md)Azure Security Center , [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) i [Aqua.](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Jak mogę skonfigurować usługę Kubernetes przy użyciu Azure Container Registry?

Zapoznaj się z dokumentacją [dotyczącą systemu Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) i [instrukcjami](../aks/cluster-container-registry-integration.md)Azure Kubernetes Service .

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Jak mogę uzyskać poświadczenia administratora dla rejestru kontenerów?

> [!IMPORTANT]
> Konto użytkownika administratora jest przeznaczone dla jednego użytkownika w celu uzyskania dostępu do rejestru, głównie do celów testowych. Nie zalecamy udostępniania poświadczeń konta administratora wielu użytkownikom. W przypadku scenariuszy bezgłowych zaleca się używanie indywidualnej tożsamości dla użytkowników i jednostki usługi. Zobacz [Omówienie uwierzytelniania.](container-registry-authentication.md)

Przed uzyskaniem poświadczeń administratora upewnij się, że administrator rejestru jest włączony.

Aby uzyskać poświadczenia przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az acr credential show -n myRegistry
```

Korzystanie z programu Azure PowerShell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Jak mogę uzyskać poświadczenia administratora w szablonie Resource Manager szablonu?

> [!IMPORTANT]
> Konto użytkownika administratora jest przeznaczone dla jednego użytkownika w celu uzyskania dostępu do rejestru, głównie do celów testowych. Nie zalecamy udostępniania poświadczeń konta administratora wielu użytkownikom. W przypadku scenariuszy bezgłowych zaleca się używanie indywidualnej tożsamości dla użytkowników i jednostki usługi. Zobacz [Omówienie uwierzytelniania.](container-registry-authentication.md)

Przed uzyskaniem poświadczeń administratora upewnij się, że administrator rejestru jest włączony.

Aby uzyskać pierwsze hasło:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Aby uzyskać drugie hasło:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Usuwanie replikacji kończy się niepowodzeniem ze stanem Zabronione, mimo że replikacja jest usuwana przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell

Ten błąd występuje, gdy użytkownik ma uprawnienia do rejestru, ale nie ma uprawnień na poziomie czytelnika w subskrypcji. Aby rozwiązać ten problem, przypisz użytkownikowi uprawnienia Czytelnik do subskrypcji:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Reguły zapory zostały pomyślnie zaktualizowane, ale nie obowiązują

Propagacja zmian reguł zapory zajmuje trochę czasu. Po zmianie ustawień zapory zaczekaj kilka minut przed zweryfikowaniem tej zmiany.


## <a name="registry-operations"></a>Operacje rejestru

- [Jak mogę dostęp do interfejsu API HTTP usługi Docker Registry w wersji 2?](#how-do-i-access-docker-registry-http-api-v2)
- [Jak mogę usunąć wszystkie manifesty, do których nie odwołuje się żaden tag w repozytorium?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Dlaczego użycie limitu przydziału rejestru nie zmniejsza się po usunięciu obrazów?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Jak mogę zweryfikować zmiany limitu przydziału magazynu?](#how-do-i-validate-storage-quota-changes)
- [Jak mogę się za pomocą rejestru podczas uruchamiania interfejsu wiersza polecenia w kontenerze?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Jak włączyć obsługę TLS 1.2?](#how-to-enable-tls-12)
- [Czy Azure Container Registry zaufanie do zawartości?](#does-azure-container-registry-support-content-trust)
- [Jak mogę udzielić dostępu do ściągania lub wypychania obrazów bez uprawnień do zarządzania zasobem rejestru?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Jak mogę włączyć automatyczną kwarantannę obrazu dla rejestru?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Jak mogę włączyć anonimowy dostęp do ściągania?](#how-do-i-enable-anonymous-pull-access)
- [Jak mogę wypychać warstwy niedystrybuowalne do rejestru?](#how-do-i-push-non-distributable-layers-to-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Jak mogę dostęp do interfejsu API HTTP usługi Docker Registry w wersji 2?

Usługa ACR obsługuje interfejs API HTTP usługi Docker Registry w wersji 2. Dostęp do interfejsów API można uzyskać na stronie `https://<your registry login server>/v2/` . Przykład: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Jak mogę usunąć wszystkie manifesty, do których nie odwołuje się żaden tag w repozytorium?

Jeśli używasz powłoki bash:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

W przypadku programu PowerShell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Uwaga: możesz dodać w `-y` poleceniu usuwania, aby pominąć potwierdzenie.

Aby uzyskać więcej informacji, zobacz [Usuwanie obrazów kontenerów w Azure Container Registry](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Dlaczego użycie limitu przydziału rejestru nie zmniejsza się po usunięciu obrazów?

Taka sytuacja może wystąpić, jeśli do podstawowych warstw nadal odwołują się inne obrazy kontenerów. Jeśli usuniesz obraz bez odwołań, użycie rejestru zostanie aktualizacji w ciągu kilku minut.

### <a name="how-do-i-validate-storage-quota-changes"></a>Jak mogę zweryfikuj zmiany limitu przydziału magazynu?

Utwórz obraz z warstwą 1 GB przy użyciu następującego pliku platformy Docker. Dzięki temu obraz ma warstwę, która nie jest współużytkowany przez żaden inny obraz w rejestrze.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Skompilowanie i wypchanie obrazu do rejestru przy użyciu interfejsu wiersza polecenia platformy Docker.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Powinno być możliwe zobaczenie, że użycie magazynu wzrosło w Azure Portal, lub zapytania dotyczące użycia można zapytania za pomocą interfejsu wiersza polecenia.

```azurecli
az acr show-usage -n myregistry
```

Usuń obraz przy użyciu interfejsu wiersza polecenia platformy Azure lub portalu i sprawdź zaktualizowane użycie w ciągu kilku minut.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Jak mogę się za pomocą rejestru podczas uruchamiania interfejsu wiersza polecenia w kontenerze?

Musisz uruchomić kontener interfejsu wiersza polecenia platformy Azure, ując gniazdo platformy Docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

W kontenerze zainstaluj program `docker` :

```bash
apk --update add docker
```

Następnie uwierzytelnij się w rejestrze:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Jak włączyć obsługę TLS 1.2?

Włącz obsługę TLS 1.2 przy użyciu dowolnego najnowszego klienta platformy Docker (wersja 18.03.0 lub nowsza). 

> [!IMPORTANT]
> Od 13 stycznia 2020 r. Azure Container Registry będzie wymagać wszystkich bezpiecznych połączeń z serwerów i aplikacji do korzystania z TLS 1.2. Obsługa TLS 1.0 i 1.1 zostanie wycofana.

### <a name="does-azure-container-registry-support-content-trust"></a>Czy Azure Container Registry zaufanie do zawartości?

Tak, można używać zaufanych obrazów w Azure Container Registry, ponieważ notariusz platformy [Docker](https://docs.docker.com/notary/getting_started/) został zintegrowany i można go włączyć. Aby uzyskać szczegółowe informacje, zobacz Content Trust in Azure Container Registry (Zaufanie [do zawartości w Azure Container Registry](container-registry-content-trust.md)).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Gdzie znajduje się plik odcisku palca?

W `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata` obszarze :

* Klucze publiczne i certyfikaty wszystkich ról (z wyjątkiem ról delegowania) są przechowywane w programie `root.json` .
* Klucze publiczne i certyfikaty roli delegowania są przechowywane w pliku JSON roli nadrzędnej (na przykład `targets.json` `targets/releases` dla roli).

Zalecamy zweryfikowanie tych kluczy publicznych i certyfikatów po ogólnej weryfikacji TUF wykonanej przez klienta usług Docker i Notary.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Jak mogę przyznać dostęp do ściągania lub wypychania obrazów bez uprawnień do zarządzania zasobem rejestru?

ACR obsługuje [role niestandardowe,](container-registry-roles.md) które zapewniają różne poziomy uprawnień. W szczególności `AcrPull` role i umożliwiają użytkownikom ściąganie i/lub wypychanie obrazów bez uprawnień do zarządzania zasobem `AcrPush` rejestru na platformie Azure.

* Azure Portal: Twój rejestr -> Access Control (IAM) -> Dodaj (Wybierz lub `AcrPull` `AcrPush` dla roli).
* Interfejs wiersza polecenia platformy Azure: znajdź identyfikator zasobu rejestru, uruchamiając następujące polecenie:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Następnie możesz przypisać rolę `AcrPull` lub `AcrPush` do użytkownika (w poniższym przykładzie użyto wartości `AcrPull` ):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Możesz też przypisać rolę do jednostki usługi identyfikowanej przez jej identyfikator aplikacji:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Następnie przypisany użytkownik może uwierzytelniać obrazy w rejestrze i uzyskać do nich dostęp.

* Aby uwierzytelnić się w rejestrze:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Aby wyświetlić listę repozytoriów:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Aby ściągnąć obraz:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

W przypadku używania tylko roli lub przypisany użytkownik nie ma uprawnień do zarządzania zasobem `AcrPull` `AcrPush` rejestru na platformie Azure. Na przykład `az acr list` wartość lub nie spowoduje `az acr show -n myRegistry` pokazania rejestru.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Jak mogę włączyć automatyczną kwarantannę obrazu dla rejestru?

Kwarantanna obrazu jest obecnie funkcją w wersji zapoznawczej acr. Tryb kwarantanny rejestru można włączyć tak, aby tylko te obrazy, które pomyślnie przeszły skanowanie zabezpieczeń, były widoczne dla zwykłych użytkowników. Aby uzyskać szczegółowe informacje, zobacz [repozytorium GitHub usługi ACR](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-enable-anonymous-pull-access"></a>Jak mogę włączyć anonimowy dostęp do ściągania?

Konfigurowanie rejestru kontenerów platformy Azure pod celu uzyskania dostępu anonimowego (nieuwierzytanego) do ściągania jest obecnie funkcją w wersji zapoznawczej dostępną w warstwach usług [Standardowa i Premium.](container-registry-skus.md) 

Aby włączyć anonimowy dostęp do ściągania, zaktualizuj rejestr przy użyciu interfejsu wiersza polecenia platformy Azure (w wersji 2.21.0 lub nowszej) i przekaż parametr do `--anonymous-pull-enabled` [polecenia az acr update:](/cli/azure/acr#az_acr_update)

```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

W dowolnym momencie możesz wyłączyć anonimowy dostęp do ściągania, ustawiając `--anonymous-pull-enabled` na `false` .

> [!NOTE]
> * Przed podjęciem próby anonimowej operacji ściągania uruchom operację , aby upewnić się, że wszystkie `docker logout` istniejące poświadczenia platformy Docker zostaną wyczyszkowane.
> * Tylko operacje płaszczyzny danych są dostępne dla nieuwierzytanych klientów.
> * Rejestr może ograniczać wysoką szybkość nieuwierzytanych żądań.

> [!WARNING]
> Anonimowy dostęp do ściągania ma obecnie zastosowanie do wszystkich repozytoriów w rejestrze. Jeśli zarządzasz dostępem do repozytorium przy użyciu [tokenów](container-registry-repository-scoped-permissions.md)w zakresie repozytorium, należy pamiętać, że wszyscy użytkownicy mogą ściągać z tych repozytoriów w rejestrze, w których włączono anonimowe ściąganie. Zalecamy usuwanie tokenów, gdy jest włączony anonimowy dostęp do ściągania.

### <a name="how-do-i-push-non-distributable-layers-to-a-registry"></a>Jak mogę wypychać warstwy niedystrybuowalne do rejestru?

Warstwa niedystrybucyjna w manifeście zawiera parametr adresu URL, z których można pobrać zawartość. Niektóre możliwe przypadki użycia do włączania wypchnięć warstwy niedystrybuowalnej są dla rejestrów z ograniczeniami sieci, rejestrów z dostępem ograniczonym lub rejestrów bez łączności z Internetem.

Jeśli na przykład masz reguły sieciowej organizacji sieciowej tak, aby maszyna wirtualna może ściągać obrazy tylko z rejestru kontenerów platformy Azure, platforma Docker ściągnie błędy dla warstw obcych/niedystrybuowalnych. Na przykład obraz Systemu Windows Server Core będzie zawierać odwołania do warstwy obcej do rejestru kontenerów platformy Azure w manifeście i nie ściągnie tego scenariusza.

Aby włączyć wypychanie warstw niedystrybuowalnych:

1. Edytuj plik `daemon.json` znajdujący się na hostach z systemem Linux `/etc/docker/` i na stronie w systemie Windows `C:\ProgramData\docker\config\daemon.json` Server. Przy założeniu, że plik był wcześniej pusty, dodaj następującą zawartość:

   ```json
   {
     "allow-nondistributable-artifacts": ["myregistry.azurecr.io"]
   }
   ```
   > [!NOTE]
   > Wartość jest tablicą adresów rejestru rozdzielonych przecinkami.

2. Zapisz i zamknij plik.

3. Uruchom ponownie program Docker.

Gdy wypychasz obrazy do rejestrów na liście, ich warstwy niedystrybuowalne są wypychane do rejestru.

> [!WARNING]
> Artefakty niedystrybuowalne zwykle mają ograniczenia dotyczące sposobu i miejsca ich rozpowszechniania i współużytknia. Tej funkcji należy używać tylko do wypychania artefaktów do rejestrów prywatnych. Upewnij się, że są zgodne z postanowieniami, które obejmują redystrybuowanie artefaktów niedystrybuowalnych.

## <a name="diagnostics-and-health-checks"></a>Diagnostyka i kontrole kondycji

- [Sprawdzanie kondycji za pomocą `az acr check-health`](#check-health-with-az-acr-check-health)
- [docker pull kończy się niepowodzeniem z błędem: net/http: żądanie zostało anulowane podczas oczekiwania na połączenie (Przekroczono limit czasu Client.Timeout podczas oczekiwania na nagłówki)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [Wypychanie docker kończy się powodzeniem, ale docker pull kończy się niepowodzeniem z błędem: brak autoryzacji: wymagane uwierzytelnianie](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login` kończy się powodzeniem, ale polecenia platformy Docker kończy się niepowodzeniem z błędem: brak autoryzacji: wymagane uwierzytelnianie](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Włączanie i uzyskiwanie dzienników debugowania demona platformy Docker](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [Nowe uprawnienia użytkownika mogą nie obowiązywać natychmiast po aktualizacji](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Informacje o uwierzytelnianiu nie są podane w prawidłowym formacie dla bezpośrednich wywołań interfejsu API REST](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Dlaczego w Azure Portal nie ma listy wszystkich repozytoriów lub tagów?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Dlaczego Azure Portal nie może pobrać repozytoriów lub tagów?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Dlaczego moje żądanie ściągnięcie lub wypchnięcie nie powiodło się z powodu niedozwolonej operacji?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Format repozytorium jest nieprawidłowy lub nieobsługiwany](#repository-format-is-invalid-or-unsupported)
- [Jak mogę zbierać ślady HTTP w systemie Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Sprawdzanie kondycji za pomocą `az acr check-health`

Aby rozwiązać typowe problemy ze środowiskiem i rejestrem, zobacz [Sprawdzanie kondycji rejestru kontenerów platformy Azure.](container-registry-check-health.md)

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull kończy się niepowodzeniem z błędem: net/http: żądanie zostało anulowane podczas oczekiwania na połączenie (Przekroczono limit czasu Client.Timeout podczas oczekiwania na nagłówki)

 - Jeśli ten błąd jest przejściowym problemem, ponów próbę zakończy się powodzeniem.
 - Jeśli `docker pull` błąd będzie stale się kończyć niepowodzeniem, może to być problem z demonem platformy Docker. Ten problem można zwykle rozwiązać, uruchamiając ponownie demona platformy Docker. 
 - Jeśli ten problem będzie nadal pojawiać się po ponownym uruchomieniu demona platformy Docker, problem może dotyczyć niektórych problemów z łącznością sieciową z maszyną. Aby sprawdzić, czy ogólna sieć na maszynie jest w dobrej kondycji, uruchom następujące polecenie, aby przetestować łączność z punktem końcowym. Minimalna wersja `az acr` zawierająca to polecenie sprawdzania łączności to 2.2.9. Uaktualnij interfejs wiersza polecenia platformy Azure, jeśli używasz starszej wersji.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - Zawsze należy mieć mechanizm ponawiania prób dla wszystkich operacji klienta platformy Docker.

### <a name="docker-pull-is-slow"></a>Ściąganie platformy Docker jest powolne
Użyj [tego](http://www.azurespeed.com/Azure/Download) narzędzia, aby przetestować szybkość pobierania sieci maszyny. Jeśli sieć maszynowa jest powolna, rozważ użycie maszyny wirtualnej platformy Azure w tym samym regionie co rejestr. Zwykle zapewnia to większą szybkość sieci.

### <a name="docker-push-is-slow"></a>Wypychanie do platformy Docker jest powolne
Użyj [tego narzędzia,](http://www.azurespeed.com/Azure/Upload) aby przetestować szybkość przekazywania danych do sieci maszyny. Jeśli sieć maszynowa jest powolna, rozważ użycie maszyny wirtualnej platformy Azure w tym samym regionie co rejestr. Zwykle zapewnia to większą szybkość sieci.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Wypychanie do platformy Docker kończy się pomyślnie, docker pull kończy się niepowodzeniem z błędem: brak autoryzacji: wymagane uwierzytelnianie

Ten błąd może wystąpić w przypadku wersji Red Hat demona platformy Docker, gdzie jest `--signature-verification` domyślnie włączona. Możesz sprawdzić opcje demona platformy Docker dla Red Hat Enterprise Linux (RHEL) lub Fedora, uruchamiając następujące polecenie:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Na przykład serwer Fedora 28 ma następujące opcje demona platformy Docker:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

Brakujący `--signature-verification=false` element `docker pull` kończy się niepowodzeniem z błędem podobnym do:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Aby rozwiązać ten problem:
1. Dodaj opcję do pliku konfiguracji demona platformy `--signature-verification=false` Docker `/etc/sysconfig/docker` . Na przykład:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Uruchom ponownie usługę demona platformy Docker, uruchamiając następujące polecenie:
   
   ```bash
   sudo systemctl restart docker.service
   ```

Szczegóły można `--signature-verification` znaleźć, uruchamiając . `man dockerd`

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>Az acr login succeeds but docker fails with error: unauthorized: authentication required

Upewnij się, że używasz tylko małych liter adresu URL serwera, na przykład , nawet jeśli nazwa zasobu rejestru zawiera wielkie lub mieszane `docker push myregistry.azurecr.io/myimage:latest` litery, takie jak `myRegistry` .

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Włączanie i uzyskiwanie dzienników debugowania demona platformy Docker    

Rozpocznij `dockerd` od `debug` opcji . Najpierw utwórz plik konfiguracji demona platformy Docker (), jeśli nie istnieje, i `/etc/docker/daemon.json` dodaj `debug` opcję :

```json
{    
    "debug": true    
}
```

Następnie uruchom ponownie demona. Na przykład w systemie Ubuntu 14.04:

```bash
sudo service docker restart
```

Szczegółowe informacje można znaleźć w [dokumentacji platformy Docker.](https://docs.docker.com/engine/admin/#enable-debugging)    

 * Dzienniki mogą być generowane w różnych lokalizacjach, w zależności od systemu. Na przykład w przypadku systemu Ubuntu 14.04 jest to `/var/log/upstart/docker.log` .    
Aby uzyskać [szczegółowe informacje, zobacz dokumentację platformy Docker.](https://docs.docker.com/engine/admin/#read-the-logs)    

 * Na Docker for Windows dzienniki są generowane w obszarze %LOCALAPPDATA%/docker/. Jednak może nie zawierać jeszcze wszystkich informacji debugowania.    

   Aby uzyskać dostęp do pełnego dziennika demona, konieczne może być kilka dodatkowych kroków:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Teraz masz dostęp do wszystkich plików maszyny wirtualnej z systemem `dockerd` . Dziennik znajduje się `/var/log/docker.log` pod .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Nowe uprawnienia użytkownika mogą nie obowiązywać natychmiast po aktualizacji

Po udzieleniu nowych uprawnień (nowych ról) do jednostki usługi zmiana może nie obowiązywać natychmiast. Istnieją dwie możliwe przyczyny:

* Azure Active Directory przypisanie roli. Zwykle jest szybkie, ale może to potrwać kilka minut z powodu opóźnienia propagacji.
* Opóźnienie uprawnień na serwerze tokenów usługi ACR. Może to potrwać do 10 minut. Aby rozwiązać ten problem, możesz `docker logout` uwierzytelnić się ponownie przy użyciu tego samego użytkownika po 1 minucie:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Obecnie usługi ACR nie obsługują usuwania replikacji domu przez użytkowników. Obejście tego problemu obejmuje utworzenie replikacji głównej w szablonie, ale pomiń jego tworzenie, dodając, `"condition": false` jak pokazano poniżej:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Informacje o uwierzytelnianiu nie są podane w prawidłowym formacie dla bezpośrednich wywołań interfejsu API REST

Może wystąpić `InvalidAuthenticationInfo` błąd, szczególnie przy użyciu narzędzia z `curl` `-L` opcją , `--location` (w celu obserwowania przekierowań).
Na przykład pobieranie obiektu blob przy użyciu opcji `curl` z `-L` opcją i uwierzytelnianiem podstawowym:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

może spowodować następującą odpowiedź:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

Główną przyczyną jest to, że niektóre `curl` implementacje podążają przekierowaniami z nagłówkami z oryginalnego żądania.

Aby rozwiązać ten problem, należy ręcznie wykonać przekierowania bez nagłówków. Wydrukuj nagłówki odpowiedzi z `-D -` opcją , a `curl` następnie wyodrębnij: `Location` nagłówek:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Dlaczego w Azure Portal nie ma listy wszystkich repozytoriów lub tagów? 

Jeśli używasz przeglądarki Microsoft Edge/IE, możesz zobaczyć co najwyżej 100 repozytoriów lub tagów. Jeśli rejestr zawiera ponad 100 repozytoriów lub tagów, zalecamy użycie przeglądarki Firefox lub Chrome do ich wszystkich elementów.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Dlaczego Azure Portal nie może pobrać repozytoriów lub tagów?

Przeglądarka może nie być w stanie wysłać żądania pobrania repozytoriów lub tagów na serwer. Mogą być różne przyczyny, takie jak:

* Brak łączności sieciowej
* Firewall
* Blokady reklam
* Błędy DNS

Skontaktuj się z administratorem sieci lub sprawdź konfigurację i łączność sieci. Spróbuj uruchomić program przy użyciu interfejsu wiersza polecenia platformy Azure, aby sprawdzić, czy twoje środowisko jest w stanie nawiązać połączenie `az acr check-health -n yourRegistry` z Container Registry. Ponadto możesz również wypróbować sesję incognito lub prywatną w przeglądarce, aby uniknąć nieaktywnej pamięci podręcznej przeglądarki lub plików cookie.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Dlaczego moje żądanie ściągnięcie lub wypchnięcie nie powiodło się z powodu niedozwolonej operacji?

Oto kilka scenariuszy, w których operacje mogą być niedozwolone:
* Rejestry klasyczne nie są już obsługiwane. Przeszliśmy do obsługiwanej [warstwy usługi przy](./container-registry-skus.md) użyciu narzędzia az [acr update](/cli/azure/acr#az_acr_update) lub Azure Portal.
* Obraz lub repozytorium mogą być zablokowane, aby nie można było go usunąć ani zaktualizować. Aby wyświetlić bieżące atrybuty, możesz użyć polecenia [az acr show repository.](./container-registry-image-lock.md)
* Niektóre operacje są niedozwolone, jeśli obraz znajduje się w kwarantannie. Dowiedz się więcej o [kwarantannie](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).
* Rejestr mógł osiągnąć limit [magazynu](container-registry-skus.md#service-tier-features-and-limits).

### <a name="repository-format-is-invalid-or-unsupported"></a>Format repozytorium jest nieprawidłowy lub nieobsługiwany

Jeśli podczas określania nazwy repozytorium w operacjach repozytorium zostanie wyświetlony błąd, taki jak "nieobsługiwany format repozytorium", "nieprawidłowy format" lub "żądane dane nie istnieją", sprawdź pisownię i literę nazwy. Prawidłowe nazwy repozytoriów mogą zawierać tylko małe litery alfanumeryczne, kropki, kreski, podkreślenia i ukośniki. 

Aby uzyskać pełne reguły nazewnictwa repozytorium, zobacz [Specyfikacja dystrybucji open container initiative distribution](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

### <a name="how-do-i-collect-http-traces-on-windows"></a>Jak mogę zbierać ślady HTTP w systemie Windows?

#### <a name="prerequisites"></a>Wymagania wstępne

- Włącz odszyfrowywanie protokołu HTTPS w programie Fiddler:  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Włącz platformę Docker, aby używać serwera proxy za pośrednictwem interfejsu użytkownika platformy Docker: <https://docs.docker.com/docker-for-windows/#proxies>
- Pamiętaj, aby przywrócić po zakończeniu.  Program Docker nie będzie działać z tym włączonym programem Fiddler i nie będzie działać.

#### <a name="windows-containers"></a>Kontenery systemu Windows

Skonfiguruj serwer proxy platformy Docker na 127.0.0.1:8888

#### <a name="linux-containers"></a>Kontenery systemu Linux

Znajdź adres IP przełącznika wirtualnego maszyny wirtualnej platformy Docker:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Skonfiguruj serwer proxy platformy Docker do danych wyjściowych poprzedniego polecenia i portu 8888 (na przykład 10.0.75.1:8888)

## <a name="tasks"></a>Zadania

- [Jak mogę anulować przebiegi wsadowe?](#how-do-i-batch-cancel-runs)
- [Jak mogę folder .git w poleceniu az acr build?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [Czy zadania obsługują wyzwalacze źródła w usłudze GitLab?](#does-tasks-support-gitlab-for-source-triggers)
- [Jaką usługę zarządzania repozytorium Git obsługuje usługa Tasks?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Jak mogę anulować przebiegi wsadowe?

Następujące polecenia anulują wszystkie uruchomione zadania w określonym rejestrze.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Jak mogę folder .git w poleceniu az acr build?

W przypadku przekazania lokalnego folderu źródłowego do polecenia folder zostanie domyślnie wykluczony z `az acr build` `.git` przekazanego pakietu. Plik można utworzyć `.dockerignore` przy użyciu następującego ustawienia. Polecenie nakazuje przywrócenie wszystkich plików w `.git` przekazanym pakiecie. 

`!.git/**`

To ustawienie dotyczy również `az acr run` polecenia .

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Czy zadania obsługują wyzwalacze źródła w usłudze GitLab?

Obecnie nie obsługujemy narzędzia GitLab dla wyzwalaczy źródła.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Jaką usługę zarządzania repozytorium Git obsługuje usługa Tasks?

| Usługa Git | Kontekst źródłowy | Kompilacja ręczna | Automatyczne kompilowanie za pomocą wyzwalacza zatwierdzania |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | Tak | Tak |
| Azure Repos | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | Tak | Tak |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | Tak | Nie |
| Bitbucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | Tak | Nie |

## <a name="run-error-message-troubleshooting"></a>Rozwiązywanie problemów z komunikatem o błędzie uruchomienia

| Komunikat o błędzie | Przewodnik rozwiązywania problemów |
|---|---|
|Nie skonfigurowano dostępu dla maszyny wirtualnej, dlatego nie znaleziono żadnych subskrypcji|Może się to zdarzyć, jeśli używasz w `az login --identity` zadaniu usługi ACR. Jest to błąd przejściowy, który występuje, gdy przypisanie roli tożsamości zarządzanej nie zostało rozpropagowane. Odczekanie kilku sekund przed podjęciem ponownej próby.|

## <a name="cicd-integration"></a>Integracja z integracją z ciasną integracją/cd

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Funkcja GitHub Actions](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej](container-registry-intro.md) o Azure Container Registry.
