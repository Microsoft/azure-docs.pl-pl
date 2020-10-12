---
title: Wersja zapoznawcza wdrożenia usługi Azure Service Fabric Docker Compose
description: Usługa Azure Service Fabric akceptuje format Docker Compose, aby ułatwić organizowanie istniejących kontenerów przy użyciu Service Fabric. Ta pomoc techniczna jest obecnie dostępna w wersji zapoznawczej.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: f84dd0ecb7a4002182c8455bfd86354d794a6f7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84691292"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Obsługa wdrażania Docker Compose w usłudze Azure Service Fabric (wersja zapoznawcza)

Platforma Docker używa pliku [Docker-Compose. yml](https://docs.docker.com/compose) do definiowania aplikacji wielokontenerowych. Aby ułatwić klientom zaznajomionym z platformą Docker w celu organizowania istniejących aplikacji kontenera w usłudze Azure Service Fabric, w tej platformie dodaliśmy obsługę wersji zapoznawczej dla Docker Compose wdrożenia w sposób natywny. Service Fabric może akceptować wersję 3 lub nowszą `docker-compose.yml` plików. 

Ponieważ ta obsługa jest dostępna w wersji zapoznawczej, obsługiwane są tylko podzbiór dyrektyw redagowania.

Aby użyć tej wersji zapoznawczej, należy utworzyć klaster z wersją 5,7 lub nowszą Service Fabric środowiska uruchomieniowego za pomocą Azure Portal wraz z odpowiednim zestawem SDK. 

> [!NOTE]
> Ta funkcja jest w wersji zapoznawczej i nie jest obsługiwana w środowisku produkcyjnym.
> Poniższe przykłady zależą od środowiska uruchomieniowego w wersji 6,0 i SDK w wersji 2,8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Wdróż plik Docker Compose na Service Fabric

Następujące polecenia tworzą aplikację Service Fabric (o nazwie `fabric:/TestContainerApp` ), którą można monitorować i zarządzać nią jak każda inna aplikacja Service Fabric. Można użyć określonej nazwy aplikacji na potrzeby zapytań dotyczących kondycji.
Service Fabric rozpoznaje wartość "Deploymentname" jako identyfikator wdrożenia redagowania.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Utwórz Service Fabric Tworzenie wdrożenia z pliku Docker-Compose. yml, uruchamiając następujące polecenie w programie PowerShell:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` i `RegistryPassword` zapoznaj się z nazwą użytkownika i hasłem rejestru kontenerów. Po zakończeniu wdrażania możesz sprawdzić jego stan przy użyciu następującego polecenia:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Aby usunąć wdrożenie redagowania za pomocą programu PowerShell, użyj następującego polecenia:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Aby rozpocząć uaktualnianie redagowania przy użyciu programu PowerShell, użyj następującego polecenia:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Aby wycofać uaktualnienie tworzenia i wdrażania za pomocą programu PowerShell, użyj następującego polecenia:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

Po zaakceptowaniu uaktualnienia można śledzić postęp uaktualniania przy użyciu następującego polecenia:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure Service Fabric (sfctl)

Alternatywnie, można użyć następującego Service Fabric polecenia interfejsu CLI:

```shell
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Po utworzeniu wdrożenia można sprawdzić jego stan przy użyciu następującego polecenia:

```shell
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Aby usunąć wdrożenie redagowania, użyj następującego polecenia:

```shell
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Aby rozpocząć uaktualnianie wdrożenia redagowania, użyj następującego polecenia:

```shell
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Aby wycofać uaktualnienie redagowania wdrożenia, użyj następującego polecenia:

```shell
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

Po zaakceptowaniu uaktualnienia można śledzić postęp uaktualniania przy użyciu następującego polecenia:

```shell
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Obsługiwane dyrektywy redagowania

Ta wersja zapoznawcza obsługuje podzestaw opcji konfiguracji w formacie tworzenia wersji 3, w tym następujące elementy podstawowe:

* Usługi > wdrażanie > replik
* Usługi > Wdróż > rozmieszczenia > ograniczenia
* Usługi > wdrożyć limity > zasobów >
    * -Procesor-udziały
    * -pamięć
    * -pamięć — wymiana
* > polecenia usług
* Usługi > środowiska
* > portów usług
* > obrazu usług
* Izolacja > usług (tylko dla systemu Windows)
* > rejestrowania > sterownika usługi
* Usługi > rejestrowanie > > opcji sterowników
* Wolumin & Wdróż wolumin >

Skonfiguruj klaster do wymuszania limitów zasobów, zgodnie z opisem w temacie [Service Fabric zarządzanie zasobami](service-fabric-resource-governance.md). Wszystkie inne dyrektywy Docker Compose nie są obsługiwane w tej wersji zapoznawczej.

### <a name="ports-section"></a>Sekcja porty

W sekcji Porty określ protokół http lub https, który będzie używany przez odbiornik usługi Service Fabric. Dzięki temu protokół punktu końcowego zostanie prawidłowo opublikowany przy użyciu usługi nazewnictwa, aby umożliwić zwrotny serwer proxy do przesyłania dalej żądań:
* Aby kierować do niezabezpieczonych Service Fabric redagowania, określ **/http**. Na przykład- **"80:80/http"**.
* Aby kierować do usługi w celu zabezpieczenia Service Fabric redagowania, określ **/https**. Na przykład- **"443:443/https"**.

> [!NOTE]
> Składnia sekcji portów/http i/HTTPS jest specyficzna dla Service Fabric w celu zarejestrowania poprawnego adresu URL odbiornika Service Fabric.  Jeśli składnia redagowania pliku platformy Docker jest programowo zweryfikowana, może to spowodować błąd walidacji.

## <a name="servicednsname-computation"></a>Obliczenia ServiceDnsName

Jeśli nazwa usługi określona w pliku redagowania to w pełni kwalifikowana nazwa domeny (to oznacza, że zawiera kropkę [.]), nazwa DNS zarejestrowana przez Service Fabric to `<ServiceName>` (łącznie z kropką). W przeciwnym razie każdy segment ścieżki w nazwie aplikacji staje się etykietą domeny w nazwie DNS usługi, a pierwszy segment ścieżki staje się etykietą domeny najwyższego poziomu.

Na przykład, jeśli określona nazwa aplikacji jest `fabric:/SampleApp/MyComposeApp` `<ServiceName>.MyComposeApp.SampleApp` zarejestrowana nazwa DNS.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Utwórz wdrożenie (definicję wystąpienia), a Service Fabric model aplikacji (definicja typu)

Plik Docker-Compose. yml zawiera opis zestawu kontenerów, w tym ich właściwości i konfiguracje.
Na przykład plik może zawierać zmienne środowiskowe i porty. Można także określić parametry wdrożenia, takie jak ograniczenia umieszczania, limity zasobów i nazwy DNS, w pliku Docker-Compose. yml.

[Model aplikacji Service Fabric](service-fabric-application-model.md) korzysta z typów usług i typów aplikacji, w których można korzystać z wielu wystąpień aplikacji tego samego typu. Na przykład można mieć jedno wystąpienie aplikacji na każdego klienta. Ten model oparty na typie obsługuje wiele wersji tego samego typu aplikacji, które są zarejestrowane w środowisku uruchomieniowym.

Na przykład klient A może mieć utworzoną aplikację z typem 1,0 of AppTypeA, a klient B może mieć inną aplikację z wystąpieniem tego samego typu i wersji. Typy aplikacji definiuje się w manifestach aplikacji, a podczas tworzenia aplikacji należy określić nazwę i parametry wdrożenia aplikacji.

Chociaż ten model oferuje elastyczność, planuje również obsługę prostszego, opartego na wystąpieniach modelu wdrażania, w którym typy są niejawne w pliku manifestu. W tym modelu każda aplikacja pobiera swój własny manifest niezależny. Przeglądamy ten proces, dodając obsługę Docker-Compose. yml, który jest formatem wdrożenia opartym na wystąpieniach.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [modelem aplikacji Service Fabric](service-fabric-application-model.md)
* [Get started with Service Fabric CLI](service-fabric-cli.md) (Wprowadzenie do interfejsu wiersza polecenia usługi Service Fabric)
