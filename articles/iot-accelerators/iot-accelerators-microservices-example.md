---
title: Zmiana i wdrożenie mikrousługi na platformie Azure | Microsoft Docs
description: W tym samouczku przedstawiono sposób zmiany i ponownego wdrożenia mikrousługi w ramach zdalnego monitorowania
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9ff3e12721628e244f247e174af101e71ea91191
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88998326"
---
# <a name="customize-and-redeploy-a-microservice"></a>Dostosowywanie i ponowne wdrażanie mikrousługi

W tym samouczku pokazano, jak edytować jedną z [mikrousług](https://azure.com/microservices) w rozwiązaniu do zdalnego monitorowania, utworzyć obraz mikrousługi, wdrożyć obraz w centrum Docker, a następnie użyć go w rozwiązaniu do zdalnego monitorowania. Aby wprowadzić tę koncepcję, samouczek używa podstawowego scenariusza, w którym wywoływany jest interfejs API mikrousług i który zmienia komunikat o stanie "Alive" i "działa" na "nowe zmiany wprowadzone w tym miejscu".

Rozwiązanie do zdalnego monitorowania używa mikrousług, które są tworzone przy użyciu obrazów platformy Docker, które są pobierane z usługi Docker Hub. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

>[!div class="checklist"]
> * Edytowanie i kompilowanie mikrousługi w rozwiązaniu do zdalnego monitorowania
> * Tworzenie obrazu platformy Docker
> * Wypychanie obrazu platformy Docker do centrum platformy Docker
> * Ściąganie nowego obrazu platformy Docker
> * Wizualizuj zmiany 

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać czynności opisane w tym samouczku, musisz:

>[!div class="checklist"]
> * [Wdróż lokalnie Akcelerator rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Konto platformy Docker](https://hub.docker.com/)
> * [Poster](https://www.getpostman.com/) — wymagany do wyświetlenia odpowiedzi interfejsu API

## <a name="call-the-api-and-view-response-status"></a>Wywoływanie interfejsu API i wyświetlanie stanu odpowiedzi

W tej części należy wywołać domyślny interfejs API mikrousługi programu IoT Hub Manager. Interfejs API zwraca komunikat o stanie, który można zmienić później przez dostosowanie mikrousługi.

1. Upewnij się, że rozwiązanie do monitorowania zdalnego działa lokalnie na komputerze.
2. Znajdź lokalizację, w której pobrano program Poster, i otwórz go.
3. W programie Poster wprowadź następujące polecenie w temacie GET: `http://localhost:8080/iothubmanager/v1/status` .
4. Wyświetl zwrot, a zobaczysz "status": "OK: Alive i dobrze".

    ![Komunikat dotyczący aktywności i ogłoszenia](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Zmienianie stanu i kompilowanie obrazu

Teraz Zmień komunikat o stanie mikrousługi Menedżera IoT Hub na "nowe zmiany wprowadzone w tym miejscu" a następnie ponownie skompiluj obraz platformy Docker przy użyciu tego nowego stanu. Jeśli w tym miejscu znajdziesz problemy, zapoznaj się z sekcją [rozwiązywania problemów](#Troubleshoot) .

1. Upewnij się, że terminal jest otwarty i przejdź do katalogu, w którym Sklonowano rozwiązanie do zdalnego monitorowania. 
1. Zmień katalog na "Azure-IoT-komputery-Remote-Monitoring-dotnet/Services/iothub-Manager/Services".
1. Otwórz StatusService.cs w dowolnym edytorze tekstu lub w środowisku IDE. 
1. Znajdź następujący kod:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    i zmień go na poniższy kod, a następnie zapisz go.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Wróć do terminalu, ale teraz przejdź do następującego katalogu: "Azure-IoT-komputery-Remote-Monitoring-dotnet/Services/iothub-Manager/scripters/Docker".
6. Aby skompilować nowy obraz platformy Docker, wpisz

    ```sh
    sh build
    ```
    
    lub w systemie Windows:
    
    ```cmd
    ./build.cmd
    ```

7. Aby sprawdzić, czy nowy obraz został pomyślnie utworzony, wpisz

    ```cmd/sh
    docker images 
    ```

Repozytorium powinno być "azureiotpcs/iothub-Manager-dotnet".

![Udany obraz platformy Docker](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Tagowanie i wypychanie obrazu
Przed wypchnięciem nowego obrazu platformy Docker do narzędzia Docker Hub program Docker oczekuje, że obrazy będą znakowane. Jeśli w tym miejscu znajdziesz problemy, zapoznaj się z sekcją [rozwiązywania problemów](#Troubleshoot) .

1. Zlokalizuj Identyfikator obrazu tworzonego obrazu platformy Docker, wpisując:

    ```cmd/sh
    docker images
    ```

2. Aby oznaczyć obraz z typem "test"

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Aby wypchnąć nowo otagowany obraz do centrum Docker, wpisz

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Otwórz przeglądarkę internetową i przejdź do [centrum Docker](https://hub.docker.com/) i zaloguj się.
5. Teraz powinien zostać wyświetlony nowo wypchnięcie obrazu platformy Docker w usłudze Docker Hub.
![Obraz platformy Docker w usłudze Docker Hub](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Aktualizowanie rozwiązania do monitorowania zdalnego
Teraz musisz zaktualizować lokalny plik Docker-Compose. yml w celu ściągnięcia nowego obrazu platformy Docker z usługi Docker Hub. Jeśli w tym miejscu znajdziesz problemy, zapoznaj się z sekcją [rozwiązywania problemów](#Troubleshoot) .

1. Wróć do terminalu i przejdź do następującego katalogu: "Azure-IoT-komputery-Remote-Monitoring-dotnet/Services/scripts/local".
2. Otwórz Docker-Compose. yml w dowolnym edytorze tekstu lub w środowisku IDE.
3. Znajdź następujący kod:

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    i zmień go tak, aby wyglądał jak na poniższym obrazie, i Zapisz go.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Wyświetl nowy stan odpowiedzi
Uzupełnij przez ponowne wdrożenie lokalnego wystąpienia rozwiązania do zdalnego monitorowania i wyświetlenie nowej odpowiedzi o stanie w programie Poster.

1. Wróć do terminalu i przejdź do następującego katalogu: "Azure-IoT-komputery-Remote-Monitoring-dotnet/scripts/local".
2. Uruchom lokalne wystąpienie rozwiązania do zdalnego monitorowania, wpisując następujące polecenie w terminalu:

    ```cmd/sh
    docker-compose up
    ```

3. Znajdź lokalizację, w której pobrano program Poster, i otwórz go.
4. W programie Poster wprowadź następujące żądanie w temacie GET: `http://localhost:8080/iothubmanager/v1/status` . Teraz należy zobaczyć "status": "OK: nowe zmiany wprowadzone w tym miejscu!".

![Nowe zmiany wprowadzone w tym miejscu](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="troubleshoot"></a><a name="Troubleshoot"></a>Rozwiązywanie problemów

Jeśli występują problemy, spróbuj usunąć obrazy platformy Docker i kontenery na komputerze lokalnym.

1. Aby usunąć wszystkie kontenery, należy najpierw zatrzymać wszystkie uruchomione kontenery. Otwórz Terminal i wpisz

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Aby usunąć wszystkie obrazy, Otwórz Terminal i wpisz 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Możesz sprawdzić, czy na maszynie istnieją kontenery, wpisując

    ```cmd/sh
    docker ps -aq 
    ```

    Jeśli wszystkie kontenery zostały pomyślnie usunięte, nic nie powinno być widoczne.

4. Możesz sprawdzić, czy na maszynie znajdują się obrazy, wpisując

    ```cmd/sh
    docker images
    ```

    Jeśli wszystkie kontenery zostały pomyślnie usunięte, nic nie powinno być widoczne.

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Edytowanie i kompilowanie mikrousługi w rozwiązaniu do zdalnego monitorowania
> * Tworzenie obrazu platformy Docker
> * Wypychanie obrazu platformy Docker do centrum platformy Docker
> * Ściąganie nowego obrazu platformy Docker
> * Wizualizuj zmiany 

Następnym krokiem, który należy podjąć [w celu dostosowania mikrousługi symulatora urządzeń w rozwiązaniu do zdalnego monitorowania](iot-accelerators-microservices-example.md)

Aby uzyskać więcej informacji programistycznych dotyczących rozwiązania do zdalnego monitorowania, zobacz:

* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

