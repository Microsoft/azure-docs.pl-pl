---
title: Jak uruchomić Self-Hosted Integration Runtime w kontenerze systemu Windows
description: Dowiedz się więcej na temat uruchamiania Self-Hosted Integration Runtime w kontenerze systemu Windows.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: 7035da173102e59aec9c643381bad701d6facf38
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92634542"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Jak uruchomić Self-Hosted Integration Runtime w kontenerze systemu Windows

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule wyjaśniono, jak uruchomić Self-Hosted Integration Runtime w kontenerze systemu Windows.
Azure Data Factory dostarcza oficjalną obsługę kontenera systemu Windows Self-Hosted Integration Runtime. Możesz pobrać kod źródłowy kompilacji platformy Docker i połączyć proces kompilowania i uruchamiania we własnym potoku ciągłego dostarczania. 

## <a name="prerequisites"></a>Wymagania wstępne 
- [Wymagania dotyczące kontenera systemu Windows](/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker w wersji 2,3 lub nowszej 
- Self-Hosted Integration Runtime w wersji 4.11.7512.1 lub nowszej 
## <a name="get-started"></a>Wprowadzenie 
1.  Zainstaluj platformę Docker i Włącz kontener systemu Windows 
2.  Pobieranie kodu źródłowego z lokalizacji https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container
3.  Pobierz najnowszą wersję SHIR w folderze "SHIR" 
4.  Otwórz folder w powłoce: 
```console
cd "yourFolderPath"
```

5.  Kompilowanie obrazu platformy Docker systemu Windows: 
```console
docker build . -t "yourDockerImageName" 
```
6.  Uruchom kontener platformy Docker: 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY jest wymagany dla tego polecenia. NODE_NAME, ENABLE_HA i HA_PORT są opcjonalne. Jeśli wartość nie zostanie ustawiona, polecenie będzie używać wartości domyślnych. Wartość domyślna ENABLE_HA to false, a HA_PORT to 8060.

## <a name="container-health-check"></a>Sprawdzenie kondycji kontenera 
Po upływie 120 sekund okres uruchomienia sprawdzania kondycji będzie uruchamiany co 30 sekund. Zapewni ona stan kondycji IR aparatu kontenera. 

## <a name="limitations"></a>Ograniczenia
Obecnie nie są obsługiwane poniższe funkcje podczas uruchamiania Self-Hosted Integration Runtime w kontenerze systemu Windows:
- Serwer proxy HTTP 
- Zaszyfrowana komunikacja węzłów węzła z certyfikatem TLS/SSL 
- Generuj i Importuj kopie zapasowe 
- Demon usługi 
- Aktualizuj aktualizacje 

### <a name="next-steps"></a>Następne kroki
- Zapoznaj [się z tematami dotyczącymi środowiska Integration Runtime w Azure Data Factory](./concepts-integration-runtime.md).
- Dowiedz się [, jak utworzyć własne środowisko Integration Runtime w Azure Portal](./create-self-hosted-integration-runtime.md).