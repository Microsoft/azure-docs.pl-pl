---
title: Jak uruchamiać aplikacje Self-Hosted Integration Runtime kontenerze systemu Windows
description: Dowiedz się, jak uruchamiać Self-Hosted Integration Runtime kontenerze systemu Windows.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: 2423d6bd29d893f9a27749dcc2b6d2af8a12e941
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478135"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Jak uruchamiać aplikacje Self-Hosted Integration Runtime kontenerze systemu Windows

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule wyjaśniono, jak uruchomić Self-Hosted Integration Runtime kontenerze systemu Windows.
Azure Data Factory są dostarczanie oficjalnej obsługi kontenerów systemu Windows dla Self-Hosted Integration Runtime. Możesz pobrać kod źródłowy kompilacji platformy Docker i połączyć proces kompilowania i uruchamiania we własnym potoku ciągłego dostarczania. 

## <a name="prerequisites"></a>Wymagania wstępne 
- [Wymagania dotyczące kontenera systemu Windows](/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker w wersji 2.3 lub nowszej 
- Self-Hosted Integration Runtime wersji 5.2.7713.1 lub nowszej 
## <a name="get-started"></a>Rozpoczęcie pracy 
1.  Instalowanie platformy Docker i włączanie kontenera systemu Windows 
2.  Pobieranie kodu źródłowego z lokalizacji https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container
3.  Pobierz najnowszą wersję standardu SHIR w folderze "SHIR" 
4.  Otwórz folder w powłoki: 
```console
cd "yourFolderPath"
```

5.  Skompilowanie obrazu platformy Docker systemu Windows: 
```console
docker build . -t "yourDockerImageName" 
```
6.  Uruchamianie kontenera platformy Docker: 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true -e HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY jest wymagany w przypadku tego polecenia. NODE_NAME, ENABLE_HA i HA_PORT opcjonalne. Jeśli nie ustawisz wartości , polecenie użyje wartości domyślnych. Wartość domyślna wartości ENABLE_HA to false, a HA_PORT 8060.

## <a name="container-health-check"></a>Sprawdzanie kondycji kontenera 
Po upływie 120 sekund uruchamiania narzędzie do sprawdzania kondycji będzie okresowo uruchamiane co 30 sekund. Zapewni kondycję ir ir dla aparatu kontenera. 

## <a name="limitations"></a>Ograniczenia
Obecnie nie obsługujemy poniższych funkcji podczas uruchamiania Self-Hosted Integration Runtime kontenerze systemu Windows:
- Serwer proxy HTTP 
- Zaszyfrowana komunikacja węzła z certyfikatem TLS/SSL 
- Generowanie i importowanie kopii zapasowej 
- Usługa demona 
- Automatyczna aktualizacja 

### <a name="next-steps"></a>Następne kroki
- Zapoznaj się [z pojęciami środowiska Integration Runtime w Azure Data Factory](./concepts-integration-runtime.md).
- Dowiedz się, [jak utworzyć własne środowisko Integration Runtime w Azure Portal](./create-self-hosted-integration-runtime.md).