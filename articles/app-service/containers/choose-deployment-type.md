---
title: Opcje wdrażania dla kontenerów systemu Linux
description: Zdecyduj między niestandardowym wdrożeniem kontenera platformy Docker, wielokontenerem i wbudowaną strukturą aplikacji dla App Service w systemie Linux.
keywords: usługa Azure App Service, aplikacja sieci Web, system Linux, usługa OSS
author: msangapu-msft
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 7c4025e393c196fb98455bac26e94e5f252840b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74687513"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Obraz niestandardowy, wiele kontenerów lub wbudowanego obrazu platformy?

[App Service w systemie Linux](app-service-linux-intro.md) oferuje trzy różne ścieżki umożliwiające opublikowanie aplikacji w sieci Web:

- **Wdrożenie obrazu niestandardowego**: "przekształcać" aplikacji do obrazu platformy Docker, który zawiera wszystkie pliki i zależności w gotowym do uruchomienia pakiecie.
- **Wdrożenie z wieloma kontenerami**: "przekształcać" aplikację w wielu kontenerach przy użyciu pliku konfiguracji Docker Compose.
- **Wdrażanie aplikacji z wbudowanym obrazem platformy**: wbudowane obrazy platformy zawierają wspólne środowiska uruchomieniowe i zależności aplikacji sieci Web, takie jak Node i php. Użyj jednej z [Azure App Service metod wdrażania](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) , aby wdrożyć aplikację w magazynie aplikacji sieci Web, a następnie użyj wbudowanego obrazu platformy do jego uruchomienia.

## <a name="which-method-is-right-for-your-app"></a>Która metoda jest odpowiednia dla aplikacji? 

Podstawowe czynniki, które należy wziąć pod uwagę:

- **Dostępność platformy Docker w przepływie pracy deweloperskiej**: niestandardowe programowanie obrazów wymaga podstawowej znajomości przepływu pracy programowania platformy Docker. Wdrożenie obrazu niestandardowego w aplikacji internetowej wymaga publikacji obrazu niestandardowego na hoście repozytorium, takim jak Docker Hub. Jeśli znasz platformę Docker i możesz dodać zadania platformy Docker do przepływu pracy kompilacji lub jeśli aplikacja jest już publikowana jako obraz platformy Docker, obraz niestandardowy prawie najlepiej nadaje się do wyboru.
- **Architektura wielowarstwowa**: Wdróż wiele kontenerów, takich jak warstwa aplikacji sieci Web i warstwa interfejsu API, aby oddzielić możliwości przy użyciu wielu kontenerów. 
- **Wydajność aplikacji**: zwiększenie wydajności aplikacji wielokontenera przy użyciu warstwy pamięci podręcznej, takiej jak Redis. Wybierz pozycję wiele kontenerów, aby to osiągnąć.
- **Unikatowe wymagania dotyczące środowiska uruchomieniowego**: wbudowane obrazy platformy są przeznaczone do zaspokajania potrzeb większości aplikacji sieci Web, ale są ograniczone w ich szerszym. Twoja aplikacja może mieć unikatowe zależności lub inne wymagania dotyczące środowiska uruchomieniowego, które przekraczają możliwości programu.
- **Wymagania dotyczące kompilacji**: Dzięki [ciągłemu wdrażaniu](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)aplikacja może zostać uruchomiona na platformie Azure bezpośrednio z poziomu kodu źródłowego. Nie jest wymagany żaden zewnętrzny proces kompilacji ani publikacji. Istnieje jednak ograniczenie szerszym i dostępności narzędzi do kompilacji w ramach aparatu wdrażania [kudu](https://github.com/projectkudu/kudu/wiki) . Twoja aplikacja może skalowalność kudu możliwości w miarę rozwoju ich zależności lub wymagań dla niestandardowej logiki kompilacji.
- **Wymagania dotyczące odczytu/zapisu na dysku**: wszystkie aplikacje sieci Web są przydzielono woluminowi magazynu dla zawartości sieci Web. Ten wolumin, objęty usługą Azure Storage, jest instalowany `/home` w systemie plików aplikacji. W przeciwieństwie do plików w systemie plików kontenera pliki w woluminie zawartości są dostępne we wszystkich wystąpieniach skalowania aplikacji, a modyfikacje będą zachowywane między ponownymi uruchomieniami aplikacji. Opóźnienie dysku woluminu zawartości jest jednak większe i bardziej zmienne niż opóźnienie lokalnego systemu plików kontenera, a dostęp może mieć wpływ na uaktualnienia platformy, nieplanowany przestój i problemy z łącznością sieciową. Aplikacje wymagające dużego dostępu tylko do odczytu do plików zawartości mogą korzystać z niestandardowego wdrożenia obrazu, które umieszcza pliki w systemie plików obrazów zamiast na woluminie zawartości.
- **Kompilowanie użycia zasobów**: po wdrożeniu aplikacji ze źródła skrypty wdrażania uruchamiane przez kudu używają tego samego App Service Planuj zasoby obliczeniowe i magazynowe jako działającą aplikację. Duże wdrożenia aplikacji mogą zużywać więcej zasobów lub czasu. W szczególności wiele przepływów pracy wdrożenia generuje aktywność dysku w woluminie zawartości aplikacji, która nie jest zoptymalizowana pod kątem tego działania. Obraz niestandardowy zapewnia wszystkie pliki i zależności aplikacji na platformie Azure w jednym pakiecie bez potrzeby dodatkowych transferów plików ani akcji wdrażania.
- **Potrzeba szybkiej iteracji**: Dockerizing aplikacja wymaga dodatkowych kroków kompilacji. Aby zmiany zaczęły obowiązywać, należy wypchnąć nowy obraz do repozytorium z każdą aktualizacją. Te aktualizacje są następnie ściągane do środowiska platformy Azure. Jeśli jeden z kontenerów wbudowanych spełnia potrzeby aplikacji, wdrożenie ze źródła może oferować szybszy przepływ pracy programistycznej.

## <a name="next-steps"></a>Następne kroki

Kontener niestandardowy:
* [Uruchamianie niestandardowego kontenera](quickstart-docker-go.md)

Wiele kontenerów:
* [Tworzenie aplikacji z wieloma kontenerami](quickstart-multi-container.md)

Poniższe artykuły zawierają wprowadzenie do App Service w systemie Linux z wbudowanym obrazem platformy:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](quickstart-php.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)