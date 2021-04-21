---
title: Uruchamianie wbudowanych kontenerów — często zadawane pytania
description: Znajdź odpowiedzi na często zadawane pytania dotyczące wbudowanych kontenerów systemu Linux w systemie Azure App Service.
keywords: azure app service, web app, faq, linux, oss, web app for containers, multi-container, multicontainer
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 82fc5707800e06e3221754bfa29d8e981ccdbd2d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782589"
---
# <a name="azure-app-service-on-linux-faq"></a>Azure App Service on Linux FAQ (Usługa Azure App Service w systemie Linux — często zadawane pytania)

Wraz z wydaniem App Service dla systemu Linux pracujemy nad dodaniem funkcji i wprowadzeniem ulepszeń na naszej platformie. Ten artykuł zawiera odpowiedzi na pytania, które ostatnio zadają nam nasi klienci.

Jeśli masz pytanie, skomentuj ten artykuł.

## <a name="built-in-images"></a>Wbudowane obrazy

**Chcę skuć wbudowane kontenery platformy Docker zapewniane przez platformę. Gdzie mogę znaleźć te pliki?**

Wszystkie pliki platformy Docker można znaleźć w witrynie [GitHub.](https://github.com/azure-app-service) Wszystkie kontenery platformy Docker można znaleźć w [Docker Hub](https://hub.docker.com/u/appsvc/).

<a id="#startup-file"></a>

**Jakie są oczekiwane wartości sekcji Plik startowy podczas konfigurowania stosu środowiska uruchomieniowego?**

| Stos           | Oczekiwana wartość                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | polecenie służące do uruchamiania aplikacji JAR (na przykład `java -jar /home/site/wwwroot/app.jar --server.port=80` ) |
| Tomcat          | lokalizacja skryptu w celu wykonania wszelkich niezbędnych konfiguracji (na przykład `/home/site/deployments/tools/startup_script.sh` )          |
| Node.js         | plik konfiguracji PM2 lub plik skryptu                                |
| .NET Core       | skompilowanej nazwy biblioteki DLL jako `dotnet <myapp>.dll`                                 |
| Ruby            | skrypt w języku Ruby, za pomocą którego chcesz zainicjować aplikację                     |

Te polecenia lub skrypty są wykonywane po uruchamianiu wbudowanego kontenera platformy Docker, ale przed rozpoczęciem działania kodu aplikacji.

## <a name="management"></a>Zarządzanie

**Co się stanie po naciśnięciu przycisku ponownego uruchamiania na Azure Portal?**

Ta akcja jest taka sama jak ponowne uruchomienie platformy Docker.

**Czy mogę użyć Secure Shell (SSH) do nawiązania połączenia z maszyną wirtualną kontenera aplikacji?**

Tak, można to zrobić za pośrednictwem lokacji zarządzania kontrolą źródła (SCM).

> [!NOTE]
> Możesz również nawiązać połączenie z kontenerem aplikacji bezpośrednio z lokalnego komputera deweloperskiego przy użyciu protokołu SSH, SFTP lub programu Visual Studio Code (na potrzeby debugowania na żywo aplikacji Node.js). Aby uzyskać więcej informacji, zobacz [Zdalne debugowanie i protokół SSH w usłudze App Service w systemie Linux](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html).
>

**Jak utworzyć plan usługi App Service Linux za pomocą zestawu SDK lub Azure Resource Manager szablonu?**

Ustaw pole **zarezerwowane** usługi App Service na *wartość true*.

## <a name="continuous-integration-and-deployment"></a>Ciągła integracja i ciągłe wdrażanie

**Moja aplikacja internetowa nadal używa starego obrazu kontenera platformy Docker po zaktualizowaniu obrazu w Docker Hub. Czy obsługujesz ciągłą integrację i wdrażanie kontenerów niestandardowych?**

Tak, aby skonfigurować ciągłą integrację/ciągłe wdrażanie dla usługi Azure Container Registry lub DockerHub, wykonaj czynności [ciągłego wdrażania](./deploy-ci-cd-custom-container.md)za pomocą Web App for Containers . W przypadku rejestrów prywatnych możesz odświeżyć kontener, zatrzymując, a następnie uruchamiać aplikację internetową. Możesz też zmienić lub dodać fikcyjne ustawienie aplikacji, aby wymusić odświeżenie kontenera.

**Czy obsługujesz środowiska przejściowe?**

Tak.

**Czy mogę wdrożyć moją aplikację internetową przy użyciu programu *WebDeploy/MSDeploy?***

Tak, należy ustawić ustawienie aplikacji o nazwie na `WEBSITE_WEBDEPLOY_USE_SCM` *wartość false*.

**Wdrożenie usługi Git mojej aplikacji kończy się niepowodzeniem podczas korzystania z aplikacji internetowej systemu Linux. Jak mogę omiń problem?**

Jeśli wdrożenie usługi Git nie powiedzie się z aplikacją internetową systemu Linux, wybierz jedną z następujących opcji, aby wdrożyć kod aplikacji:

- Korzystanie z funkcji ciągłego dostarczania (wersja zapoznawcza): kod źródłowy aplikacji można przechowywać w repozytorium git usługi Azure DevOps lub repozytorium GitHub, aby korzystać z usługi Azure Continuous Delivery. Aby uzyskać więcej informacji, zobacz How to configure Continuous Delivery for Linux web app (Jak skonfigurować [ciągłe dostarczanie dla aplikacji internetowej systemu Linux).](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/)

- Użyj [interfejsu API wdrażania](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)pliku ZIP: aby użyć tego interfejsu API, nasuń [SSH](configure-linux-open-ssh-session.md) do aplikacji internetowej i przejdź do folderu, w którym chcesz wdrożyć kod. Uruchom następujący kod:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Jeśli wystąpi błąd z komunikatem o tym, że polecenie nie zostanie znalezione, przed uruchomieniem poprzedniego polecenia zainstaluj program curl przy użyciu `curl` `apt-get install curl` polecenia `curl` .

## <a name="language-support"></a>Obsługa języków

**Chcę użyć gniazd internetowych w mojej aplikacji Node.js, jakieś specjalne ustawienia lub konfiguracje do ustawienia?**

Tak, wyłącz w kodzie po `perMessageDeflate` Node.js serwera. Jeśli na przykład używasz socket.io, użyj następującego kodu:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Czy obsługujesz nieskompilowane aplikacje .NET Core?**

Tak.

**Czy aplikacja Composer jest wspierana jako menedżer zależności dla aplikacji PHP?**

Tak, podczas wdrażania narzędzia Git kudu powinno wykryć, że wdrażasz aplikację PHP (dzięki obecności pliku composer.lock), a następnie Kudu wyzwoli instalację composera.

## <a name="custom-containers"></a>Kontenery niestandardowe

**Korzystam z własnego kontenera niestandardowego. Chcę, aby platforma zainstaluje udział SMB w `/home/` katalogu.**

Jeśli `WEBSITES_ENABLE_APP_SERVICE_STORAGE` ustawienie jest **nieokreślone** lub ma wartość *false,* katalog nie zostanie udostępniony w wystąpieniach skalowania, a zapisane pliki nie będą utrwalane `/home/` po ponownym uruchomieniu.   Jawne ustawienie `WEBSITES_ENABLE_APP_SERVICE_STORAGE` *wartości true* spowoduje włączenie instalacji.

**Mój kontener niestandardowy trwa długo, a platforma ponownie uruchamia kontener przed zakończeniem jego uruchamiania.**

Możesz skonfigurować czas oczekiwania na ponowne uruchomienie kontenera przez platformę. W tym celu ustaw `WEBSITES_CONTAINER_START_TIME_LIMIT` ustawienie aplikacji na wartość, której potrzebujesz. Wartość domyślna to 230 sekund, a wartość maksymalna to 1800 sekund.

**Jaki jest format adresu URL prywatnego serwera rejestru?**

Podaj pełny adres URL rejestru, w tym `http://` lub `https://` .

**Jaki jest format nazwy obrazu w opcji rejestru prywatnego?**

Dodaj pełną nazwę obrazu, w tym adres URL rejestru prywatnego (na przykład myacr.azurecr.io/dotnet:latest). Nazw obrazów, które używają portu niestandardowego, [nie można wprowadzać za pośrednictwem portalu](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Aby ustawić `docker-custom-image-name` wartość , użyj narzędzia wiersza [ `az` polecenia](/cli/azure/webapp/config/container#az_webapp_config_container_set).

**Czy mogę uwidocznić więcej niż jeden port w niestandardowym obrazie kontenera?**

Nie obsługujemy ujawniania więcej niż jednego portu.

**Czy mogę korzystać z własnego magazynu?**

Tak, [funkcja bring your own storage jest](./configure-connect-to-azure-storage.md) w wersji zapoznawczej.

**Dlaczego nie mogę przeglądać systemu plików kontenera niestandardowego lub uruchamiać procesów z witryny SCM?**

Lokacja SCM działa w oddzielnym kontenerze. Nie można sprawdzić systemu plików ani uruchomionych procesów kontenera aplikacji.

**Mój kontener niestandardowy nasłuchuje na porcie innym niż 80. Jak skonfigurować moją aplikację do rozsyłania żądań do tego portu?**

Mamy automatyczne wykrywanie portów. Możesz również określić ustawienie aplikacji o nazwie *WEBSITES_PORT* i nadać mu wartość oczekiwanego numeru portu. Wcześniej platforma używała ustawienia *aplikacji PORT.* Planujemy wycofać to ustawienie aplikacji i używać wyłącznie *WEBSITES_PORT* aplikacji.

**Czy muszę zaimplementować protokół HTTPS w kontenerze niestandardowym?**

Nie, platforma obsługuje zakończenie protokołu HTTPS na wspólnych frontonach.

**Czy muszę używać zmiennej PORT w kodzie dla kontenerów wbudowanych?**

Nie, zmienna PORT nie jest konieczna ze względu na automatyczne wykrywanie portów. Jeśli port nie zostanie wykryty, wartość domyślna to 80.
Aby ręcznie skonfigurować port niestandardowy, użyj instrukcji EXPOSE w pliku Dockerfile i ustawienia aplikacji WEBSITES_PORT z wartością portu do powiązania w kontenerze.

**Czy muszę używać WEBSITES_PORT kontenerów niestandardowych?**

Tak, jest to wymagane w przypadku kontenerów niestandardowych. Aby ręcznie skonfigurować port niestandardowy, użyj instrukcji EXPOSE w pliku Dockerfile i ustawienia aplikacji WEBSITES_PORT z wartością portu do powiązania w kontenerze.

**Czy mogę użyć ASPNETCORE_URLS w obrazie platformy Docker?**

Tak, zastąp zmienną środowiskową przed rozpoczęciem aplikacji .NET Core.
Na przykład W skrypcie init.sh: export ASPNETCORE_URLS={Your value}

## <a name="multi-container-with-docker-compose"></a>Wiele kontenerów z Docker Compose

**Jak mogę skonfigurować Azure Container Registry (ACR) do użycia z wieloma kontenerami?**

Aby można było używać usługi ACR z wieloma **kontenerami,** wszystkie obrazy kontenerów muszą być hostowane na tym samym serwerze rejestru usługi ACR. Gdy znajdują się one na tym samym serwerze rejestru, należy utworzyć ustawienia aplikacji, a następnie zaktualizować plik konfiguracji Docker Compose, aby uwzględnić nazwę obrazu usługi ACR.

Utwórz następujące ustawienia aplikacji:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (pełny adres URL, np. `https://<server-name>.azurecr.io` )
- DOCKER_REGISTRY_SERVER_PASSWORD (włącz dostęp administratora w ustawieniach usługi ACR)

W pliku konfiguracji odwołuj się do obrazu usługi ACR, tak jak w poniższym przykładzie:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Jak mogę, który kontener jest dostępny z Internetu?**

- Tylko jeden kontener może być otwarty w celu uzyskania dostępu
- Dostępny jest tylko port 80 i 8080 (widoczne porty)

Poniżej znajdują się reguły określania, który kontener jest dostępny — w kolejności pierwszeństwa:

- Ustawienie aplikacji `WEBSITES_WEB_CONTAINER_NAME` ustawione na nazwę kontenera
- Pierwszy kontener definiujący port 80 lub 8080
- Jeśli żadna z powyższych wartości nie jest prawdziwa, pierwszy kontener zdefiniowany w pliku będzie dostępny (ujawniony)


## <a name="web-sockets"></a>Web Sockets

Gniazda internetowe są obsługiwane w aplikacjach systemu Linux.

> [!IMPORTANT]
> Gniazda internetowe nie są obecnie obsługiwane w przypadku aplikacji systemu Linux w planach App Service Free. Pracujemy nad usunięciem tego ograniczenia i planujemy obsługę maksymalnie 5 połączeń gniazd internetowych w planach bezpłatnych App Service internetowych.

## <a name="pricing-and-sla"></a>Cennik i umowa SLA

**Jaka jest cena, teraz, gdy usługa jest ogólnie dostępna?**

Ceny różnią się w zależności od SKU i regionu, ale więcej szczegółów można znaleźć na naszej stronie z cennikiem: [App Service Cennik.](https://azure.microsoft.com/pricing/details/app-service/linux/)

## <a name="other-questions"></a>Inne pytania

**Co oznacza "Żądana funkcja nie jest dostępna w grupie zasobów"?**

Ten komunikat może zostać wyświetlony podczas tworzenia aplikacji internetowej przy użyciu Azure Resource Manager (ARM). Na podstawie bieżącego ograniczenia dla tej samej grupy zasobów nie można mieszać aplikacji dla systemów Windows i Linux w tym samym regionie.

**Jakie są obsługiwane znaki w nazwach ustawień aplikacji?**

W ustawieniach aplikacji można używać tylko liter (A–Z, a–z), cyfr (0–9) i znaku podkreślenia (_).

**Gdzie mogę zażądać nowych funkcji?**

Swój pomysł możesz przesłać na forum [Web Apps opinii.](https://aka.ms/webapps-uservoice) Dodaj pozycję "[Linux]" do tytułu swojego pomysłu.

## <a name="next-steps"></a>Następne kroki

- [Co to jest Azure App Service dla systemu Linux?](overview.md#app-service-on-linux)
- [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](deploy-staging-slots.md)
- [Ciągłe wdrażanie za pomocą Web App for Containers](./deploy-ci-cd-custom-container.md)
- [Co należy wiedzieć: Web Apps i Linux](https://techcommunity.microsoft.com/t5/apps-on-azure/things-you-should-know-web-apps-and-linux/ba-p/392472)
