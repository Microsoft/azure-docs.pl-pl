---
title: Wdrażanie bramy samohostowanej na platformie Docker | Microsoft Docs
description: Dowiedz się, jak wdrożyć składnik bramy samoobsługowej usługi Azure API Management na platformie Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: b9e990988770e8aca015ae8b1159bb4f5e50df57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82205094"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Wdrażanie bramy samohostowanej na platformie Azure API Management w usłudze Docker

W tym artykule przedstawiono kroki wdrażania samoobsługowego składnika bramy platformy Azure API Management w środowisku Docker.

> [!NOTE]
> Hosting bramy samoobsługowej w programie Docker najlepiej nadaje się do zastosowań testowych i programistycznych. Kubernetes jest zalecane do użycia w środowisku produkcyjnym. Zapoznaj się z [tym](how-to-deploy-self-hosted-gateway-kubernetes.md) dokumentem, aby dowiedzieć się, jak wdrożyć bramę samohostowaną w usłudze Kubernetes.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
- Utwórz środowisko platformy Docker. Platforma [Docker dla komputerów stacjonarnych](https://www.docker.com/products/docker-desktop) jest dobrą opcją do celów deweloperskich i testowych. Zapoznaj się z [dokumentacją platformy Docker](https://docs.docker.com) , aby uzyskać informacje na temat wszystkich wersji platformy Docker, ich funkcji oraz kompleksowej dokumentacji dotyczącej platformy Docker.
- [Inicjowanie obsługi administracyjnej zasobu bramy w wystąpieniu API Management](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Brama samoobsługowa jest pakowana jako kontener platformy Docker oparty na architekturze x86-64.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Wdrażanie bramy samohostowanej na platformie Docker

1. Wybierz pozycję **bramy** z sekcji **wdrażanie i infrastruktura**.
2. Wybierz zasób bramy, który ma zostać wdrożony.
3. Wybierz pozycję **wdrożenie**.
4. Należy zauważyć, że token dostępu w polu tekstowym **token** został wygenerowany automatycznie przy użyciu domyślnych wartości kluczy **wygaśnięcia** i **tajnych** . W razie potrzeby wybierz odpowiednie wartości w jednej lub obu kontrolkach, aby wygenerować nowy token.
4. Upewnij się, że w obszarze **Skrypty wdrażania**wybrano **platformę Docker** .
5. Wybierz łącze plik **ENV. conf** obok **środowiska** , aby pobrać plik.
6. Wybierz ikonę **Kopiuj** znajdującą się po prawej stronie pola tekstowego **Uruchom** , aby skopiować polecenie Docker do Schowka.
7. Wklej polecenie do okna terminalu (lub polecenia). Dostosuj mapowania portów i nazwę kontenera odpowiednio do wymagań. Należy pamiętać, że polecenie założono, że pobrany plik środowiska znajduje się w bieżącym katalogu.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Wykonaj polecenie. Polecenie instruuje środowisko Docker, aby uruchomić kontener przy użyciu [obrazu kontenera](https://aka.ms/apim/sputnik/dhub) pobranego z programu Microsoft Container Registry i ZMAPOWAĆ porty HTTP (8080) i HTTPS (8081) do portów 80 i 443 na hoście.
9. Uruchom poniższe polecenie, aby sprawdzić, czy kontener bramy jest uruchomiony:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Wróć do Azure Portal, kliknij pozycję **Przegląd** i upewnij się, że właśnie wdrożony kontener bramy jest w dobrej kondycji.

![stan bramy](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> Użyj <code>console docker container logs <gateway-name></code> polecenia, aby wyświetlić migawkę dziennika bramy samohostowanej.
>
> Użyj <code>docker container logs --help</code> polecenia, aby wyświetlić wszystkie opcje wyświetlania dziennika.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat bramy samoobsługowej, zobacz [Omówienie usługi Azure API Management Brama](self-hosted-gateway-overview.md)samodzielna.
* [Skonfiguruj niestandardową nazwę domeny dla bramy samohostowanej](api-management-howto-configure-custom-domain-gateway.md).
