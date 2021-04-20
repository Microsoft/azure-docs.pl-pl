---
title: Wdrażanie własnej bramy na platformie Docker | Microsoft Docs
description: Dowiedz się, jak wdrożyć składnik własnej bramy usługi Azure API Management na platformie Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/19/2021
ms.author: apimpm
ms.openlocfilehash: 531421726bc1e081d85eca9d535267520d3fea5f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725613"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Wdrażanie bramy usługi Azure API Management hostowanej na platformie Docker

Ten artykuł zawiera instrukcje wdrażania własnego składnika bramy usługi Azure API Management w środowisku platformy Docker.

> [!NOTE]
> Hostowanie własnej bramy na platformie Docker jest najlepszym rozwiązaniem w przypadku przypadków użycia oceny i projektowania. Użycie kubernetes jest zalecane do użytku produkcyjnego. Zapoznaj [się z tym](how-to-deploy-self-hosted-gateway-kubernetes.md) dokumentem, aby dowiedzieć się, jak wdrożyć bramę hostowaną samodzielnie na platformie Kubernetes.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
- Utwórz środowisko platformy Docker. [Program Docker for Desktop](https://www.docker.com/products/docker-desktop) jest dobrą opcją do celów tworzenia i oceny. Zobacz [dokumentację platformy Docker,](https://docs.docker.com) aby uzyskać informacje o wszystkich wersjach platformy Docker, ich funkcjach i kompleksowej dokumentacji samej platformy Docker.
- [Aprowizować zasób bramy w API Management wystąpienia](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Brama hostowana samodzielnie jest spakowana jako kontener platformy Docker oparty na systemie Linux w wersji x86-64.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Wdrażanie własnej bramy na platformie Docker

1. W **obszarze Wdrażanie i infrastruktura** wybierz pozycję **Bramy.**
2. Wybierz zasób bramy, który zamierzasz wdrożyć.
3. Wybierz **pozycję Wdrożenie.**
4. Pamiętaj, że token dostępu w polu **tekstowym Token** został **wygenerowany** automatycznie przy użyciu domyślnych wartości Wygaśnięcie i **Klucz tajny.** W razie potrzeby wybierz żądane wartości w jednej lub obu kontrolkach, aby wygenerować nowy token.
5. Upewnij się, że w obszarze Skrypty wdrażania **wybrano** opcję **Docker.**
6. Wybierz **link env.conf** obok opcji **Środowisko,** aby pobrać plik.
7. Wybierz **ikonę** kopiowania znajdującą  się na prawym końcu pola tekstowego Uruchom, aby skopiować polecenie platformy Docker do schowka.
8. Wklej polecenie w oknie terminalu (lub polecenia). Dostosuj mapowania portów i nazwę kontenera zgodnie z potrzebami. Należy pamiętać, że polecenie zakłada, że pobrany plik środowiska znajduje się w bieżącym katalogu.
   ```
       docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
   ```
9. Wykonaj polecenie. Polecenie nakazuje środowisku platformy Docker uruchomienie [](https://aka.ms/apim/sputnik/dhub) kontenera przy użyciu obrazu kontenera pobranego z usługi Microsoft Container Registry oraz zamapowanie portów HTTP (8080) i HTTPS (8081) kontenera na porty 80 i 443 na hoście.
10. Uruchom poniższe polecenie, aby sprawdzić, czy kontener bramy jest uruchomiony:
    ```console
    docker ps
    CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
    895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
    ```
10. Wstecz, Azure Portal pozycję Przegląd i  upewnij się, że wdrożony kontener bramy (Self-hosted) zgłasza stan dobrej kondycji.

    ![stan bramy](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> Użyj <code>console docker container logs <gateway-name></code> polecenia , aby wyświetlić migawkę własnego dziennika bramy.
>
> Użyj <code>docker container logs --help</code> polecenia , aby wyświetlić wszystkie opcje wyświetlania dzienników.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o bramie hostowanej samodzielnie, zobacz Azure API Management self-hosted gateway overview (Omówienie własnej bramy usługi [Azure API Management self-hosted).](self-hosted-gateway-overview.md)
* [Skonfiguruj niestandardową nazwę domeny dla własnej bramy](api-management-howto-configure-custom-domain-gateway.md).
