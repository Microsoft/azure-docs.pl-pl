---
title: Uruchamianie wbudowanych kontenerów — często zadawane pytania
description: Znajdź odpowiedzi na często zadawane pytania dotyczące wbudowanych kontenerów systemu Linux w Azure App Service.
keywords: usługa Azure App Service, aplikacja sieci Web, często zadawane pytania, Linux, OSS, Web App for Containers, wiele kontenerów i wielokontenera
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: fb5203629915914ab9af22d89e5f2865078a8e44
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012611"
---
# <a name="azure-app-service-on-linux-faq"></a>Azure App Service on Linux FAQ (Usługa Azure App Service w systemie Linux — często zadawane pytania)

Wraz z wydaniem App Service w systemie Linux pracujemy nad dodaniem funkcji i wprowadzeniem ulepszeń dla naszej platformy. W tym artykule znajdują się odpowiedzi na pytania, na które często prosili klienci.

Jeśli masz pytanie, komentarz dotyczący tego artykułu.

## <a name="built-in-images"></a>Wbudowane obrazy

**Chcę utworzyć rozwidlenie wbudowanych kontenerów platformy Docker dostępnych na platformie. Gdzie mogę znaleźć te pliki?**

Wszystkie pliki platformy Docker można znaleźć w witrynie [GitHub](https://github.com/azure-app-service). Wszystkie kontenery platformy Docker można znaleźć w usłudze [Docker Hub](https://hub.docker.com/u/appsvc/).

<a id="#startup-file"></a>

**Jakie są oczekiwane wartości w sekcji pliku startowego podczas konfigurowania stosu środowiska uruchomieniowego?**

| Stos           | Oczekiwana wartość                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | polecenie uruchamiania aplikacji JAR (na przykład `java -jar /home/site/wwwroot/app.jar --server.port=80` ) |
| Tomcat          | Lokalizacja skryptu do wykonania wszelkich niezbędnych konfiguracji (na przykład `/home/site/deployments/tools/startup_script.sh` )          |
| Node.js         | plik konfiguracji PM2 lub plik skryptu                                |
| .NET Core       | Nazwa skompilowanej biblioteki DLL jako `dotnet <myapp>.dll`                                 |
| Ruby            | skrypt Ruby, z którym chcesz zainicjować aplikację                     |

Te polecenia lub skrypty są wykonywane po uruchomieniu wbudowanego kontenera Docker, ale przed uruchomieniem kodu aplikacji.

## <a name="management"></a>Zarządzanie

**Co się stanie, gdy naciśniesz przycisk Uruchom ponownie w Azure Portal?**

Ta akcja jest taka sama jak w przypadku ponownego uruchomienia platformy Docker.

**Czy można używać Secure Shell (SSH) do nawiązywania połączenia z maszyną wirtualną kontenera aplikacji?**

Tak. możesz to zrobić za pomocą lokacji zarządzania kontrolą źródła (SCM).

> [!NOTE]
> Możesz również nawiązać połączenie z kontenerem aplikacji bezpośrednio z lokalnego komputera deweloperskiego przy użyciu protokołu SSH, SFTP lub programu Visual Studio Code (na potrzeby debugowania na żywo aplikacji Node.js). Aby uzyskać więcej informacji, zobacz [Zdalne debugowanie i protokół SSH w usłudze App Service w systemie Linux](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html).
>

**Jak mogę utworzyć plan App Service systemu Linux za pomocą zestawu SDK lub szablonu Azure Resource Manager?**

Ustaw pole **zastrzeżone** usługi App Service na *wartość true*.

## <a name="continuous-integration-and-deployment"></a>Ciągła integracja i ciągłe wdrażanie

**Moja aplikacja internetowa nadal używa starego obrazu kontenera Docker po zaktualizowaniu obrazu w usłudze Docker Hub. Czy obsługujesz ciągłą integrację i wdrażanie kontenerów niestandardowych?**

Tak, aby skonfigurować ciągłą integrację/wdrożenie dla Azure Container Registry lub DockerHub, za [pomocą ciągłego wdrażania z Web App for Containers](./deploy-ci-cd-custom-container.md). W przypadku rejestrów prywatnych można odświeżyć kontener, zatrzymując i uruchamiając aplikację sieci Web. Lub można zmienić lub dodać fikcyjne ustawienie aplikacji, aby wymusić odświeżenie kontenera.

**Czy są obsługiwane środowiska przejściowe?**

Tak.

**Czy mogę użyć narzędzia *webdeploy/MSDeploy* do wdrożenia mojej aplikacji sieci Web?**

Tak, należy ustawić `WEBSITE_WEBDEPLOY_USE_SCM` *wartość false* dla ustawienia aplikacji.

**Wdrożenie narzędzia Git dla mojej aplikacji kończy się niepowodzeniem w przypadku korzystania z aplikacji sieci Web systemu Linux. Jak można obejść ten problem?**

Jeśli wdrożenie narzędzia Git nie powiedzie się w aplikacji sieci Web systemu Linux, wybierz jedną z następujących opcji, aby wdrożyć kod aplikacji:

- Użyj funkcji ciągłego dostarczania (wersja zapoznawcza): możesz przechowywać kod źródłowy swojej aplikacji w repozytorium usługi Azure DevOps Git lub repozytorium GitHub, aby korzystać z ciągłego dostarczania platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ciągłego dostarczania dla aplikacji sieci Web systemu Linux](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- Użyj [interfejsu API Deploy zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file): Aby użyć tego interfejsu API, [SSH do aplikacji sieci Web](configure-linux-open-ssh-session.md) i przejdź do folderu, w którym chcesz wdrożyć swój kod. Uruchom następujący kod:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Jeśli zostanie wyświetlony komunikat o błędzie, że `curl` nie można znaleźć polecenia, upewnij się, że zainstalowano za pomocą `apt-get install curl` przed uruchomieniem poprzedniego `curl` polecenia.

## <a name="language-support"></a>Obsługa języków

**Chcę używać gniazd sieci Web w aplikacji Node.js, dowolnych ustawień specjalnych lub konfiguracji do ustawienia?**

Tak, wyłącz `perMessageDeflate` w kodzie Node.js po stronie serwera. Na przykład jeśli używasz socket.io, użyj następującego kodu:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Czy są obsługiwane Nieskompilowane aplikacje .NET Core?**

Tak.

**Czy chcesz obsługiwać układacz jako Menedżer zależności dla aplikacji PHP?**

Tak, podczas wdrażania usługi git kudu powinien wykryć, czy wdrażasz aplikację PHP (z zawieszeniem obecności pliku Composer. Lock), a następnie kudu wyzwoli instalację kompozytora.

## <a name="custom-containers"></a>Kontenery niestandardowe

**Używam własnego kontenera niestandardowego. Chcę, aby platforma zainstalowała udział SMB w `/home/` katalogu.**

Jeśli `WEBSITES_ENABLE_APP_SERVICE_STORAGE` ustawienie jest **nieokreślone** lub ma wartość *false*, `/home/` katalog **nie będzie współużytkowany** przez wystąpienia skalowania, a pliki zapisywane **nie będą się powtarzać** po ponownym uruchomieniu. Jawne ustawienie `WEBSITES_ENABLE_APP_SERVICE_STORAGE` *wartości true* spowoduje włączenie instalacji.

**Nie można rozpocząć pracy z kontenerem niestandardowym, a platforma ponownie uruchomi kontener przed jego rozpoczęciem.**

Można skonfigurować czas oczekiwania platformy przed ponownym uruchomieniem kontenera. W tym celu należy określić `WEBSITES_CONTAINER_START_TIME_LIMIT` wartość ustawienia aplikacji. Wartość domyślna to 230 sekund, a maksymalna wartość to 1800 sekund.

**Jaki jest format adresu URL serwera rejestru prywatnego?**

Podaj pełny adres URL rejestru, włącznie z `http://` lub `https://` .

**Jaki jest format nazwy obrazu w opcji rejestru prywatnego?**

Dodaj pełną nazwę obrazu, w tym adres URL rejestru prywatnego (na przykład myacr.azurecr.io/dotnet:latest). [W portalu nie można wprowadzać](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650)nazw obrazów używających portu niestandardowego. Aby ustawić `docker-custom-image-name` , użyj [ `az` narzędzia wiersza polecenia](/cli/azure/webapp/config/container#az-webapp-config-container-set).

**Czy mogę uwidocznić więcej niż jeden port w obrazie niestandardowego kontenera?**

Nie obsługujemy ujawniania więcej niż jednego portu.

**Czy mogę przenieść własny magazyn?**

Tak, [Przenieś własny magazyn](./configure-connect-to-azure-storage.md) w wersji zapoznawczej.

**Dlaczego nie mogę przeglądać mojego systemu plików kontenera niestandardowego ani uruchamiać procesów z poziomu witryny SCM?**

Lokacja SCM działa w osobnym kontenerze. Nie można sprawdzić systemu plików ani procesów uruchamiania kontenera aplikacji.

**Mój kontener niestandardowy nasłuchuje na porcie innym niż port 80. Jak skonfigurować aplikację do kierowania żądań do tego portu?**

Mamy automatyczne wykrywanie portów. Można również określić ustawienie aplikacji o nazwie *WEBSITES_PORT* i nadać jej wartość oczekiwanego numeru portu. Wcześniej platforma używała ustawienia aplikacji *port* . Planujemy zaniechanie tego ustawienia aplikacji i użycie wyłącznie *WEBSITES_PORT* .

**Czy muszę zaimplementować protokół HTTPS w moim kontenerze niestandardowym?**

Nie, Platforma obsługuje zakończenie protokołu HTTPS na udostępnianych frontonach.

**Czy muszę używać zmiennej portów w kodzie w przypadku kontenerów wbudowanych?**

Nie, zmienna portu nie jest konieczna z powodu automatycznego wykrywania portów. Jeśli port nie zostanie wykryty, wartość domyślna to 80.
Aby ręcznie skonfigurować port niestandardowy, użyj instrukcji UWIDACZNIAnia w pliku dockerfile i ustawienia aplikacji, WEBSITES_PORT, z wartością portu, która ma zostać powiązana z kontenerem.

**Czy muszę używać WEBSITES_PORT w przypadku kontenerów niestandardowych?**

Tak, jest to wymagane w przypadku kontenerów niestandardowych. Aby ręcznie skonfigurować port niestandardowy, użyj instrukcji UWIDACZNIAnia w pliku dockerfile i ustawienia aplikacji, WEBSITES_PORT, z wartością portu, która ma zostać powiązana z kontenerem.

**Czy można użyć ASPNETCORE_URLS w obrazie platformy Docker?**

Tak, Zastąp zmienną środowiskową przed uruchomieniem aplikacji .NET Core.
Na przykład W skrypcie init.sh: Export ASPNETCORE_URLS = {Twoja wartość}

## <a name="multi-container-with-docker-compose"></a>Wiele kontenerów z Docker Compose

**Jak mogę skonfigurować Azure Container Registry (ACR) do użycia z obsługą wiele kontenerów?**

Aby można było używać ACR z obsługą wiele kontenerów, **wszystkie obrazy kontenerów** muszą być hostowane na tym samym serwerze rejestru ACR. Gdy znajdują się na tym samym serwerze rejestru, konieczne będzie utworzenie ustawień aplikacji, a następnie zaktualizowanie pliku konfiguracji Docker Compose w celu uwzględnienia nazwy obrazu ACR.

Utwórz następujące ustawienia aplikacji:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (pełny adres URL, np. `https://<server-name>.azurecr.io` )
- DOCKER_REGISTRY_SERVER_PASSWORD (Włącz dostęp administratora w ustawieniach ACR)

W pliku konfiguracji odwołuje się do obrazu ACR, tak jak w poniższym przykładzie:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Jak mogę wiedzieć, który kontener jest dostępny dla Internetu?**

- Tylko jeden kontener może być otwarty na potrzeby dostępu
- Dostępny jest tylko port 80 i 8080 (uwidocznione porty)

Poniżej przedstawiono reguły określania, który kontener jest dostępny — w kolejności pierwszeństwa:

- Ustawienie aplikacji `WEBSITES_WEB_CONTAINER_NAME` ustawione na nazwę kontenera
- Pierwszy kontener do zdefiniowania portu 80 lub 8080
- Jeśli żaden z powyższych wartości nie jest spełniony, pierwszy kontener zdefiniowany w pliku będzie dostępny (uwidoczniony)


## <a name="web-sockets"></a>Gniazda sieci Web

Gniazda Web Sockets są obsługiwane w aplikacjach systemu Linux.

> [!IMPORTANT]
> Gniazda Web Sockets nie są obecnie obsługiwane w przypadku aplikacji systemu Linux w planach App Service w warstwie Bezpłatna. Pracujemy nad usunięciem tego ograniczenia i zaplanowaniu obsługi do 5 połączeń gniazd sieci Web w planach App Service w warstwie Bezpłatna.

## <a name="pricing-and-sla"></a>Cennik i Umowa SLA

**Jakie są ceny, a teraz usługa jest ogólnie dostępna?**

Ceny różnią się w zależności od jednostki SKU i regionu, ale więcej szczegółów można znaleźć na naszej stronie cennika: [ceny App Service](https://azure.microsoft.com/pricing/details/app-service/linux/).

## <a name="other-questions"></a>Inne pytania

**Co oznacza "żądana funkcja jest niedostępna w grupie zasobów"?**

Ten komunikat może zostać wyświetlony podczas tworzenia aplikacji sieci Web przy użyciu Azure Resource Manager (ARM). W oparciu o bieżące ograniczenie dla tej samej grupy zasobów, nie można mieszać aplikacji systemu Windows i Linux w tym samym regionie.

**Jakie są obsługiwane znaki w nazwach ustawień aplikacji?**

Do ustawień aplikacji można używać tylko liter (A-Z, a-z), cyfr (0-9) i znaku podkreślenia (_).

**Gdzie mogę zażądać nowych funkcji?**

Swój pomysł można przesłać na [forum opinii Web Apps](https://aka.ms/webapps-uservoice). Dodaj "[Linux]" do tytułu Twojego pomysłu.

## <a name="next-steps"></a>Następne kroki

- [Co to jest Azure App Service w systemie Linux?](overview.md#app-service-on-linux)
- [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](deploy-staging-slots.md)
- [Ciągłe wdrażanie za pomocą Web App for Containers](./deploy-ci-cd-custom-container.md)
- [Elementy, które należy znać: Web Apps i Linux](https://techcommunity.microsoft.com/t5/apps-on-azure/things-you-should-know-web-apps-and-linux/ba-p/392472)
