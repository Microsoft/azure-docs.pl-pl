---
title: Wybieranie i wdrażanie agentów zabezpieczeń
description: Dowiedz się, jak wybierać i wdrażać agentów zabezpieczeń usługi Defender dla IoT na urządzeniach IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 8e18b79cc14fe98879ec97361f6e275d8fd918bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940927"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Wybieranie i wdrażanie agenta zabezpieczeń na urządzeniu IoT

Usługa Defender for IoT oferuje architektury referencyjne dla agentów zabezpieczeń, które monitorują i zbierają dane z urządzeń IoT.
Aby dowiedzieć się więcej, zobacz [Architektura referencyjna agenta zabezpieczeń](security-agent-architecture.md).

Agenci są opracowani jako projekty open source i są dostępne w dwóch wersjach: <br> [C](https://aka.ms/iot-security-github-c)i [C#](https://aka.ms/iot-security-github-cs).

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Porównanie wersji agentów zabezpieczeń
> * Odkryj obsługiwane platformy agentów
> * Wybieranie odpowiedniego elementu agenta dla rozwiązania

## <a name="understand-security-agent-options"></a>Omówienie opcji agenta zabezpieczeń

Każda wersja agenta zabezpieczeń usługi Defender for IoT oferuje ten sam zestaw funkcji i obsługuje podobne opcje konfiguracji.

Agent zabezpieczeń oparty na języku C ma mniejszą ilość pamięci i jest idealnym wyborem dla urządzeń z mniejszą liczbą dostępnych zasobów.

|     | Agent zabezpieczeń oparty na języku C | Agent zabezpieczeń oparty na języku C# |
| --- | ----------- | --------- |
| **Open source** | Dostępne w ramach [licencji MIT](https://en.wikipedia.org/wiki/MIT_License) w serwisie [GitHub](https://aka.ms/iot-security-github-c) | Dostępne w ramach [licencji MIT](https://en.wikipedia.org/wiki/MIT_License) w serwisie [GitHub](https://aka.ms/iot-security-github-cs) |
| **Język programowania**    | C | C# |
| **Obsługiwane platformy Windows?** | Nie | Tak |
| **Wymagania wstępne systemu Windows** | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| **Obsługiwane platformy Linux?** | Tak, x64 i x86 | Tak, tylko x64 |
| **Wymagania wstępne systemu Linux** | libunwind8, libcurl3, UUID-Runtime, auditd, audispd-plugins | libunwind8, libcurl3, UUID-Runtime, auditd, audispd-plugins, sudo, netstat, dołączenie iptables |
| **Miejsce na dysku** | 10,5 MB | 90 MB |
| **Rozmiar pamięci (średnio)** | 5,5 MB | 33 MB |
| **[Uwierzytelnianie](concept-security-agent-authentication-methods.md) do IoT Hub** | Tak | Tak |
| **[Zbieranie](how-to-agent-configuration.md#supported-security-events) danych zabezpieczeń** | Tak | Tak |
| **Agregacja zdarzeń** | Tak | Tak |
| **Konfiguracja zdalna za poorednictwem [sznurka modułu zabezpieczeń](concept-security-module.md)** | Tak | Tak |

## <a name="security-agent-installation-guidelines"></a>Wskazówki dotyczące instalacji agenta zabezpieczeń

Dla **systemu Windows**: skrypt instalacji SecurityAgent.ps1 musi zostać wykonany z okna programu PowerShell administratora.

Dla systemu **Linux**: InstallSecurityAgent.sh musi być uruchomiona jako administratora. Zalecamy prefiks polecenia instalacji z "sudo".

## <a name="choose-an-agent-flavor"></a>Wybierz wersję agenta

Aby wybrać odpowiedniego agenta, Odpowiedz na następujące pytania dotyczące urządzeń IoT:

- Czy używasz _systemu Windows Server_ lub _Windows IoT Core_?

    [Wdróż agenta zabezpieczeń opartego na języku C# dla systemu Windows](how-to-deploy-windows-cs.md).

- Czy używasz dystrybucji systemu Linux z architekturą x86?

    [Wdróż agenta zabezpieczeń opartego na języku C dla systemu Linux](how-to-deploy-linux-c.md).

- Czy używasz dystrybucji systemu Linux z architekturą x64?

    Można używać obu rodzajów agentów. <br>
    [Wdróż agenta zabezpieczeń opartego na języku C dla systemu Linux](how-to-deploy-linux-c.md) i/lub [Wdróż agenta zabezpieczeń opartego na C# dla systemu Linux](how-to-deploy-linux-cs.md).

Oba typy agentów oferują ten sam zestaw funkcji i obsługują podobne opcje konfiguracji.
Zobacz [porównanie agentów zabezpieczeń](how-to-deploy-agent.md#understand-security-agent-options) , aby dowiedzieć się więcej.

## <a name="supported-platforms"></a>Obsługiwane platformy

Poniższa lista zawiera wszystkie aktualnie obsługiwane platformy.

|Agent usługi Defender dla IoT |System operacyjny |Architektura |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    x64|
|C|Ubuntu 18.04 |    x64, architektury ARMv7|
|C|Debian 9 |    x64, x86|
|C#|Ubuntu 16.04     |x64|
|C#|Ubuntu 18.04    |x64, architektury ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core, kompilacja 17763    |x64|
|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o opcjach konfiguracji, przejdź do przewodnika krok po kroku dotyczący konfiguracji agenta.
> [!div class="nextstepaction"]
> [Konfiguracja agenta — Przewodnik](./how-to-agent-configuration.md)
