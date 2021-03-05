---
title: Tworzenie agenta Defender Micro z kodu źródłowego
titleSuffix: Azure Defender for IoT
description: Micro Agent obejmuje infrastrukturę, która może służyć do dostosowywania dystrybucji.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/18/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 57360d939ea0cda513732cfbcc35c1d22a899f6c
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124537"
---
# <a name="build-the-defender-micro-agent-from-source-code"></a>Tworzenie agenta Defender Micro z kodu źródłowego

Micro Agent obejmuje infrastrukturę, która może służyć do dostosowywania dystrybucji. Aby wyświetlić listę dostępnych parametrów konfiguracji, spójrz na `configs/LINUX_BASE.conf` plik.

W przypadku pojedynczego rozkładu należy zmodyfikować `.conf` plik podstawowy. 

Jeśli wymagana jest więcej niż jedna dystrybucja, można dziedziczyć z konfiguracji podstawowej i zastąpić jej wartości. 

Aby zastąpić wartości:

1. Utwórz nowy `.dist` plik.

1. Dodaj `CONF_DEFINE_BASE(${g_plat_config_path} LINUX_BASE.conf)` do góry.
 
1. Zdefiniuj nowe wartości do dowolnych potrzeb, na przykład: 

    `set(ASC_LOW_PRIORITY_INTERVAL 60*60*24)` 

1. Nadaj `.dist` plikowi odwołanie podczas kompilowania. Na przykład 

    `cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET=UBUNTU1804 ..` 

## <a name="baseline-configuration-signing"></a>Podpisywanie konfiguracji linii bazowej 

Agent weryfikuje autentyczność plików konfiguracyjnych, które są umieszczane na dysku w celu ograniczenia naruszenia, domyślnie.

Ten proces można zatrzymać przez zdefiniowanie flagi preprocesora `ASC_BASELINE_CONF_SIGN_CHECK_DISABLE` .

Nie zalecamy wyłączania sprawdzania podpisu dla środowisk produkcyjnych. 

Jeśli potrzebujesz innej konfiguracji w scenariuszach produkcyjnych, skontaktuj się z zespołem Defender for IoT. 

## <a name="prerequisites"></a>Wymagania wstępne 

1. Skontaktuj się z kierownikiem ds. klientów, aby uzyskać dostęp do usługi Defender dla kodu źródłowego IoT.
 
1. Sklonuj lub Wyodrębnij kod źródłowy do folderu na dysku.

1. Przejdź do tego katalogu.

1. Pobierz moduły podrzędne przy użyciu następującego kodu:

    ```bash
    git submodule update --init
    ```
    
1. Następnie Pobierz moduły podrzędne dla zestawu Azure IoT SDK przy użyciu następującego kodu: 

    ```bash
    git -C deps/azure-iot-sdk-c/ submodule update –init
    ```
 

1. Dodaj uprawnienie Wykonywanie i uruchom skrypt Instalatora środowiska deweloperskiego:

    ```bash
    chmod +x scripts/install_development_environment.sh && ./scripts/install_development_environment.sh 
    ```

1. Utwórz katalog dla danych wyjściowych kompilacji: 

    ```bash
    mkdir cmake 
    ```

1. Obowiązkowe Pobierz i zainstaluj [programu vscode](https://code.visualstudio.com/download ) 

1. Obowiązkowe Zainstaluj [rozszerzenie C/C++](https://code.visualstudio.com/docs/languages/cpp ) dla programu vscode.

## <a name="building-the-defender-iot-micro-agent"></a>Kompilowanie programu Defender IoT Micro Agent 

1. Otwórz katalog z programu vscode 

1. Przejdź do `cmake` katalogu. 

1. Uruchom następujące polecenie: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET<the appropriate distro configuration file name> .. 
    
    cmake --build . -- -j${env:NPROC}
    ```

    Na przykład: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGETUBUNTU1804 ..
    
    cmake --build . -- -j${env:NPROC}
    ```

## <a name="next-steps"></a>Następne kroki

[Skonfiguruj rozwiązanie Azure Defender for IoT](quickstart-configure-your-solution.md).
