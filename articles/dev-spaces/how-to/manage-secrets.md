---
title: Zarządzanie wpisami tajnymi podczas pracy z obszarem dev platformy Azure
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Dowiedz się, jak używać wpisów tajnych Kubernetes w czasie uruchamiania lub kompilowania podczas opracowywania aplikacji przy użyciu Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontenery
ms.custom: devx-track-js
ms.openlocfilehash: 8791480f420dfd76d5291ce82e8ebf7412a41326
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91972972"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Zarządzanie wpisami tajnymi podczas pracy z obszarem dev platformy Azure

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Twoje usługi mogą wymagać pewnych haseł, parametrów połączenia i innych wpisów tajnych, takich jak bazy danych lub inne bezpieczne usługi platformy Azure. Ustawiając wartości tych kluczy tajnych w plikach konfiguracji, można udostępnić je w kodzie jako zmienne środowiskowe.  Te pliki konfiguracji należy obsługiwać, aby uniknąć naruszenia bezpieczeństwa wpisów tajnych.

## <a name="storing-and-using-runtime-secrets"></a>Przechowywanie i używanie wpisów tajnych środowiska uruchomieniowego

Azure Dev Spaces oferuje dwie zalecane, Usprawnione opcje przechowywania wpisów tajnych na wykresach Helm generowanych przez narzędzia klienta Azure Dev Spaces: w `values.dev.yaml` pliku i wbudowane bezpośrednio w programie `azds.yaml` . Nie zaleca się przechowywania wpisów tajnych w programie `values.yaml` .

> [!NOTE]
> Poniższe podejścia pokazują, jak przechowywać klucze tajne dla wykresów Helm generowanych przez narzędzia klienta i korzystać z nich. W przypadku tworzenia własnego wykresu Helm można użyć wykresu Helm bezpośrednio do zarządzania wpisami tajnymi i ich przechowywania.

### <a name="using-valuesdevyaml"></a>Korzystanie z wartości. dev. YAML

W projekcie, który został już przygotowany przy użyciu Azure Dev Spaces, Utwórz `values.dev.yaml` plik w tym samym folderze, w którym można `azds.yaml` zdefiniować klucze tajne i wartości. Na przykład:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Sprawdź `azds.yaml` odwołania do pliku `values.dev.yaml` jako opcjonalne przy użyciu `?` . Na przykład:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Jeśli masz dodatkowe pliki tajne, możesz je dodać również w tym miejscu.

Zaktualizuj lub sprawdź, czy usługa odwołuje się do wpisów tajnych jako zmiennych środowiskowych. Na przykład:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Uruchom zaktualizowane usługi przy użyciu programu `azds up` .

```console
azds up
```
 
Użyj `kubectl` , aby sprawdzić, czy Twoje wpisy tajne zostały utworzone.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Nie zaleca się przechowywania wpisów tajnych w kontroli źródła. W przypadku korzystania z usługi git Dodaj `values.dev.yaml` do `.gitignore` pliku, aby uniknąć zatwierdzania wpisów tajnych w kontroli źródła.

### <a name="using-azdsyaml"></a>Korzystanie z azds. YAML

W projekcie, który został już przygotowany przy użyciu Azure Dev Spaces, Dodaj klucze tajne i wartość przy użyciu składni *$PlaceHolder* w obszarze *konfiguracje. programowanie. Install. Set* in `azds.yaml` . Na przykład:

```yaml
configurations:
  develop:
    ...
    install:
      set:
        secrets:
          redis:
            port: "$REDIS_PORT"
            host: "$REDIS_HOST"
            key: "$REDIS_KEY"
```

> [!NOTE]
> Wartości tajne można wprowadzać bezpośrednio bez używania składni *$PlaceHolder* w programie `azds.yaml` . Jednakże takie podejście nie jest zalecane, ponieważ `azds.yaml` jest przechowywane w kontroli źródła.
     
Utwórz `.env` plik w tym samym folderze, co `azds.yaml` w celu zdefiniowania wartości *$PlaceHolder* . Na przykład:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Nie zaleca się przechowywania wpisów tajnych w kontroli źródła. W przypadku korzystania z usługi git Dodaj `.env` do `.gitignore` pliku, aby uniknąć zatwierdzania wpisów tajnych w kontroli źródła.

Zaktualizuj lub sprawdź, czy usługa odwołuje się do wpisów tajnych jako zmiennych środowiskowych. Na przykład:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Uruchom zaktualizowane usługi przy użyciu programu `azds up` .

```console
azds up
```
 
Użyj `kubectl` , aby sprawdzić, czy Twoje wpisy tajne zostały utworzone.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Używanie wpisów tajnych jako argumentów kompilacji

W poprzedniej sekcji pokazano, jak przechowywać klucze tajne i używać ich do użycia w czasie wykonywania kontenera. Można również użyć dowolnego wpisu tajnego w czasie kompilacji kontenera, takiego jak hasło dla prywatnego NuGet, przy użyciu `azds.yaml` .

W programie `azds.yaml` Ustaw wpisy tajne czasu kompilacji w *konfiguracjach. opracowywać. Build. args* przy użyciu `<variable name>: ${secret.<secret name>.<secret key>}` składni. Na przykład:

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

W powyższym przykładzie *mynugetsecret* jest istniejącym wpisem tajnym, a *pattoken* jest istniejącym kluczem.

>[!NOTE]
> Nazwy i klucze tajne mogą zawierać `.` znak. Użyj `\` do ucieczki `.` podczas przekazywania wpisów tajnych jako argumentów kompilacji. Na przykład, aby przekazać wpis tajny o nazwie *foo.bar* z kluczem *tokenu*: `MYTOKEN: ${secret.foo\.bar.token}` . Ponadto wpisy tajne można ocenić przy użyciu prefiksu i przyrostka tekstu. Na przykład `MYURL: eus-${secret.foo\.bar.token}-version1`. Ponadto klucze tajne dostępne w obszarze nadrzędnym i nadrzędnym mogą być przekazane jako argumenty kompilacji.

W pliku dockerfile Użyj dyrektywy *ARG* , aby użyć klucza tajnego, a następnie użyj tej samej zmiennej w dalszej części pliku dockerfile. Na przykład:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Zaktualizuj usługi działające w klastrze przy użyciu tych zmian. W wierszu polecenia Uruchom polecenie:

```
azds up
```

## <a name="next-steps"></a>Następne kroki

Za pomocą tych metod można teraz bezpiecznie łączyć się z bazą danych, usługą Azure cache for Redis lub uzyskiwać dostęp do bezpiecznych usług platformy Azure.
 
