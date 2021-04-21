---
title: Rozwiązywanie typowych problemów
description: Dowiedz się, jak rozwiązywać typowe problemy podczas wdrażania, uruchamiania lub zarządzania Azure Container Instances
ms.topic: article
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ce7e3018e470df3840eb01127a7bf2ffa01b5cbc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771069"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Rozwiązywanie typowych problemów w usłudze Azure Container Instances

W tym artykule pokazano, jak rozwiązywać typowe problemy związane z zarządzaniem kontenerami lub ich wdrażaniem w Azure Container Instances. Zobacz też [często zadawane pytania.](container-instances-faq.md)

Jeśli potrzebujesz dodatkowej pomocy technicznej, zobacz dostępne **opcje Pomoc i obsługa techniczna** w [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="issues-during-container-group-deployment"></a>Problemy podczas wdrażania grupy kontenerów
### <a name="naming-conventions"></a>Konwencje nazewnictwa

Podczas definiowania specyfikacji kontenera niektóre parametry wymagają przestrzegania ograniczeń nazewnictwa. Poniżej znajduje się tabela z określonymi wymaganiami dla właściwości grupy kontenerów. Aby uzyskać więcej informacji, zobacz [Konwencje nazewnictwa][azure-name-restrictions] w te Centrum architektury platformy Azure i [Ograniczenia dotyczące nazewnictwa][naming-rules]zasobów platformy Azure.

| Zakres | Długość | Wielkość liter | Prawidłowe znaki | Sugerowany wzorzec | Przykład |
| --- | --- | --- | --- | --- | --- |
| Nazwa kontenera<sup>1</sup> | 1-63 |Małe litery | Alfanumeryczne i łącznik w dowolnym miejscu z wyjątkiem pierwszego lub ostatniego znaku |`<name>-<role>-container<number>` |`web-batch-container1` |
| Porty kontenerów | Od 1 do 65535 |Liczba całkowita |Liczba całkowita z wartości od 1 do 65535 |`<port-number>` |`443` |
| Etykieta nazwy DNS | 5-63 |Bez uwzględniania wielkości liter |Alfanumeryczne i łącznik w dowolnym miejscu z wyjątkiem pierwszego lub ostatniego znaku |`<name>` |`frontend-site1` |
| Zmienna środowiskowa | 1-63 |Bez uwzględniania wielkości liter |Znak alfanumeryczny i podkreślenie (_) w dowolnym miejscu z wyjątkiem pierwszego lub ostatniego znaku |`<name>` |`MY_VARIABLE` |
| Nazwa woluminu | 5-63 |Małe litery |Alfanumeryczne i łączniki w dowolnym miejscu z wyjątkiem pierwszego lub ostatniego znaku. Nie może zawierać dwóch kolejnych łączników. |`<name>` |`batch-output-volume` |

<sup>1</sup> Ograniczenie dotyczy również nazw grup kontenerów, jeśli nie są określone niezależnie od wystąpień kontenerów, na przykład w przypadku `az container create` wdrożeń poleceń.

### <a name="os-version-of-image-not-supported"></a>Wersja systemu operacyjnego obrazu nie jest obsługiwana

Jeśli określisz obraz, który Azure Container Instances nie obsługuje, `OsVersionNotSupported` zostanie zwrócony błąd. Błąd jest podobny do następującego, `{0}` gdzie to nazwa obrazu, który próbujesz wdrożyć:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Ten błąd występuje najczęściej podczas wdrażania obrazów systemu Windows opartych na wersji 1709 lub 1803 programu Semi-Annual Channel, które nie są obsługiwane. Aby uzyskać informacje o obsługiwanych obrazach systemu Windows Azure Container Instances, [zobacz często zadawane pytania.](container-instances-faq.md#what-windows-base-os-images-are-supported)

### <a name="unable-to-pull-image"></a>Nie można ściągnąć obrazu

Jeśli Azure Container Instances początkowo nie można ściągnąć obrazu, będzie on przez określony czas ponowny prób. Jeśli operacja ściągania obrazu będzie nadal kończyć się niepowodzeniem, wdrożenie usługi ACI ostatecznie zakończy się niepowodzeniem i może zostać wyświetlony `Failed to pull image` błąd.

Aby rozwiązać ten problem, usuń wystąpienie kontenera i ponów próbę wdrożenia. Upewnij się, że obraz istnieje w rejestrze i że nazwa obrazu jest wpisana poprawnie.

Jeśli nie można ściągnąć obrazu, zdarzenia podobne do następujących są wyświetlane w danych wyjściowych [az container show][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type&quot;: &quot;Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type&quot;: &quot;Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type&quot;: &quot;Normal"
  }
],
```
### <a name="resource-not-available-error"></a>Błąd niedostępny zasobu

Z powodu różnego obciążenia zasobów regionalnych na platformie Azure podczas próby wdrożenia wystąpienia kontenera może wystąpić następujący błąd:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Ten błąd wskazuje, że z powodu intensywnego obciążenia w regionie, w którym próbujesz wdrożyć, nie można w tym czasie alokować zasobów określonych dla kontenera. Aby rozwiązać problem, użyj co najmniej jednego z poniższych kroków zaradczych.

* Sprawdź, czy ustawienia wdrażania kontenera należą do parametrów zdefiniowanych w obszarze [Dostępność regionu dla Azure Container Instances](container-instances-region-availability.md)
* Określanie niższych ustawień procesora i pamięci dla kontenera
* Wdrażanie w innym regionie świadczenia usługi Azure
* Wdrażanie w późniejszym czasie

## <a name="issues-during-container-group-runtime"></a>Problemy podczas środowiska uruchomieniowego grupy kontenerów
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Kontener ciągle kończy działanie i uruchamia się ponownie (brak długotrwałego procesu)

Domyślnie zasady ponownego uruchamiania grup [kontenerów](container-instances-restart-policy.md) **to Always**, więc kontenery w grupie kontenerów są zawsze uruchamiane ponownie po zakończeniu ich uruchamiania. Jeśli zamierzasz uruchamiać kontenery oparte  na zadaniach, może być konieczne zmianę na **Wł.** lub Nigdy. Jeśli określisz **wartość OnFailure** i nadal będziesz widzieć ciągłe ponowne uruchamianie, może to być problem z aplikacją lub skryptem wykonywanym w kontenerze.

W przypadku uruchamiania grup kontenerów bez długotrwałych procesów możesz zobaczyć powtarzające się zakończenia i ponowne uruchomienia z obrazami, takimi jak Ubuntu lub Alpine. Nawiązywanie połączenia za pośrednictwem [exec](container-instances-exec.md) nie będzie działać, ponieważ kontener nie ma procesu utrzymującego jego aktywności. Aby rozwiązać ten problem, uwzględnij polecenie start podobne do poniższego we wdrożeniu grupy kontenerów, aby zachować działanie kontenera.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

Interfejs CONTAINER INSTANCES API Azure Portal zawiera `restartCount` właściwość . Aby sprawdzić liczbę ponownego uruchomienia kontenera, możesz użyć polecenia [az container show][az-container-show] w interfejsie wiersza polecenia platformy Azure. W następujących przykładowych danych wyjściowych (które zostały obcięte w celu zwięzłości) na końcu danych wyjściowych widać `restartCount` właściwość .

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Większość obrazów kontenerów dla dystrybucji systemu Linux ustawia powłokę, taką jak bash, jako polecenie domyślne. Ponieważ powłoka sama w sobie nie jest długotrwałą usługą, te kontenery natychmiast wyjść i wpadają do pętli ponownego uruchamiania po skonfigurowaniu przy użyciu domyślnych **zasad zawsze** ponownego uruchamiania.

### <a name="container-takes-a-long-time-to-start"></a>Uruchomienie kontenera trwa długo

Trzy podstawowe czynniki wpływające na czas uruchamiania kontenera w Azure Container Instances to:

* [Rozmiar obrazu](#image-size)
* [Lokalizacja obrazu](#image-location)
* [Buforowane obrazy](#cached-images)

Obrazy systemu Windows [mają dodatkowe zagadnienia.](#cached-images)

#### <a name="image-size"></a>Rozmiar obrazu

Jeśli uruchomienie kontenera zajmuje dużo czasu, ale ostatecznie się powiedzie, zacznij od oglądu rozmiaru obrazu kontenera. Ponieważ Azure Container Instances obraz kontenera na żądanie, wyświetlony czas uruchamiania jest bezpośrednio związany z jego rozmiarem.

Rozmiar obrazu kontenera można wyświetlić za pomocą polecenia w `docker images` interfejsie wiersza polecenia platformy Docker:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

Kluczem do utrzymania małych rozmiarów obrazów jest zapewnienie, że końcowy obraz nie zawiera niczego, co nie jest wymagane w czasie wykonywania. Jednym ze sposobów na to jest [kompilacje wieloetapowe][docker-multi-stage-builds]. Kompilacje wieloetapowe ułatwiają zapewnienie, że obraz końcowy zawiera tylko artefakty potrzebne dla aplikacji, a nie dodatkową zawartość, która była wymagana w czasie kompilacji.

#### <a name="image-location"></a>Lokalizacja obrazu

Innym sposobem zmniejszenia wpływu ściągania obrazu na czas uruchamiania kontenera jest hostenie obrazu kontenera w u Azure Container Registry w tym samym regionie, w którym zamierzasz wdrożyć wystąpienia kontenerów. [](../container-registry/index.yml) Skraca to ścieżkę sieciową, którą obraz kontenera musi przejechać, co znacznie skraca czas pobierania.

#### <a name="cached-images"></a>Buforowane obrazy

Azure Container Instances używa mechanizmu buforowania, aby przyspieszyć czas uruchamiania kontenera dla obrazów zbudowanych na typowych obrazach podstawowych systemu [Windows,](container-instances-faq.md#what-windows-base-os-images-are-supported)w tym `nanoserver:1809` , i `servercore:ltsc2019` `servercore:1809` . Często używane obrazy systemu Linux, takie `ubuntu:1604` jak `alpine:3.6` i, również są buforowane. W przypadku obrazów systemów Windows i Linux unikaj używania `latest` tagu . Aby uzyskać Container Registry, zapoznaj się z [najlepszymi rozwiązaniami w zakresie](../container-registry/container-registry-image-tag-version.md) tagów obrazów. Aby uzyskać aktualny wykaz buforowanych obrazów i tagów, użyj interfejsu API [list obrazów buforowanych.][list-cached-images]

> [!NOTE]
> Korzystanie z obrazów opartych na systemie Windows Server 2019 w Azure Container Instances jest w wersji zapoznawczej.

#### <a name="windows-containers-slow-network-readiness"></a>Wolne gotowość sieci kontenerów systemu Windows

Po początkowym utworzeniu kontenery systemu Windows mogą nie mieć łączności przychodzącej lub wychodzącej przez maksymalnie 30 sekund (lub dłużej, w rzadkich przypadkach). Jeśli aplikacja kontenera wymaga połączenia internetowego, dodaj logikę opóźnienia i ponawiania próby, aby umożliwić nawiązanie łączności z Internetem przez 30 sekund. Po wstępnej konfiguracji sieć kontenerów powinna zostać odpowiednio wznowiona.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Nie można nawiązać połączenia z bazowym interfejsem API platformy Docker ani uruchamiać kontenerów uprzywilejowanych

Azure Container Instances nie uwidacznia bezpośredniego dostępu do podstawowej infrastruktury, która hostuje grupy kontenerów. Obejmuje to dostęp do interfejsu API platformy Docker uruchomionego na hoście kontenera i uruchomionych kontenerów uprzywilejowanych. Jeśli potrzebujesz interakcji z platformą Docker, zapoznaj się z dokumentacją [referencyjną REST,](/rest/api/container-instances/) aby zobaczyć, co obsługuje interfejs API ACI. Jeśli brakuje informacji, prześlij żądanie na forach opinii [na temat ACI.](https://aka.ms/aci/feedback)

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>Adres IP grupy kontenerów może nie być dostępny z powodu niezgodności portów

Azure Container Instances nie obsługuje jeszcze mapowania portów, takiego jak w przypadku zwykłej konfiguracji platformy Docker. Jeśli uważasz, że adres IP grupy kontenerów nie jest dostępny, upewnij się, że skonfigurowano obraz kontenera do nasłuchiwać tych samych portów, które zostały uwidocznione w grupie kontenerów za pomocą `ports` właściwości .

Jeśli chcesz potwierdzić, Azure Container Instances nasłuchiwać na porcie skonfigurowanym w obrazie kontenera, przetestuj wdrożenie obrazu, które `aci-helloworld` uwidacznia port. Uruchom również `aci-helloworld` aplikację, aby nasłuchiwać na porcie. `aci-helloworld` akceptuje opcjonalną zmienną `PORT` środowiskową w celu zastąpienia domyślnego portu 80, na którym nasłuchuje. Aby na przykład przetestować port 9000, ustaw zmienną [środowiskową](container-instances-environment-variables.md) podczas tworzenia grupy kontenerów:

1. Skonfiguruj grupę kontenerów, aby uwidocznić port 9000, i przekaż numer portu jako wartość zmiennej środowiskowej. Przykład jest sformatowany dla powłoki Bash. Jeśli wolisz inną powłokę, taką jak program PowerShell lub wiersz polecenia, musisz odpowiednio dostosować przypisanie zmiennej.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Znajdź adres IP grupy kontenerów w danych wyjściowych polecenia `az container create` . Poszukaj wartości **ip**. 
1. Po pomyślnym aprowizeniu kontenera przejdź do adresu IP i portu aplikacji kontenera w przeglądarce, na przykład: `192.0.2.0:9000` . 

    Powinien zostać wyświetlony ekran "Witamy w Azure Container Instances!" przez aplikację internetową.
1. Po pracy z kontenerem usuń go za pomocą `az container delete` polecenia :

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [pobierać dzienniki kontenerów i zdarzenia,](container-instances-get-logs.md) aby ułatwić debugowanie kontenerów.

<!-- LINKS - External -->
[azure-name-restrictions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[naming-rules]: ../azure-resource-manager/management/resource-name-rules.md
[windows-sac-overview]: /windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az_container_show
[list-cached-images]: /rest/api/container-instances/location/listcachedimages
