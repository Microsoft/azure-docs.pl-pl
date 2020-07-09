---
title: Jak wdrożyć zależności w chmurze OPC bliźniaczy na platformie Azure | Microsoft Docs
description: W tym artykule opisano sposób wdrażania nieOPCych zależności platformy Azure wymaganych do lokalnego projektowania i debugowania.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 231d1efa02ec80e8ad56a8895d4262d774480111
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "73824097"
---
# <a name="deploying-dependencies-for-local-development"></a>Wdrażanie zależności dla lokalnego tworzenia

W tym artykule wyjaśniono, jak wdrażać tylko usługi platformy Azure, które są konieczne do lokalnego tworzenia i debugowania.   Na koniec zostanie wdrożona Grupa zasobów, która zawiera wszystko, czego potrzebujesz do lokalnego tworzenia i debugowania.

## <a name="deploy-azure-platform-services"></a>Wdrażanie usług platformy Azure

1. Upewnij się, że zainstalowano rozszerzenia PowerShell i [AzureRM PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) .  Otwórz wiersz polecenia lub terminal i uruchom polecenie:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Postępuj zgodnie z monitami, aby przypisać nazwę do grupy zasobów dla wdrożenia.  Skrypt wdraża tylko zależności od tej grupy zasobów w ramach subskrypcji platformy Azure, ale nie do mikrousług.  Skrypt rejestruje również aplikację w Azure Active Directory.  Jest to konieczne do obsługi uwierzytelniania OAUTH.  Wdrożenie może potrwać kilka minut.

3. Po zakończeniu działania skryptu możesz wybrać opcję zapisania pliku ENV.  Plik środowiska ENV to plik konfiguracji wszystkich usług i narzędzi, które mają być uruchamiane na komputerze deweloperskim.  

## <a name="troubleshooting-deployment-failures"></a>Rozwiązywanie problemów z błędami wdrożenia

### <a name="resource-group-name"></a>Nazwa grupy zasobów

Upewnij się, że używana jest krótka i prosta Nazwa grupy zasobów.  Nazwa jest używana również do nazywania zasobów w taki sposób, aby musiała być zgodna z wymaganiami dotyczącymi nazw zasobów.  

### <a name="azure-active-directory-aad-registration"></a>Rejestracja w usłudze Azure Active Directory (AAD)

Skrypt wdrażania próbuje zarejestrować aplikacje usługi AAD w Azure Active Directory.  W zależności od uprawnień do wybranej dzierżawy usługi AAD może to się nie powieść.   Dostępne są trzy opcje:

1. Jeśli wybrano dzierżawę usługi AAD z listy dzierżawców, uruchom ponownie skrypt i wybierz inny z listy.
2. Alternatywnie można wdrożyć prywatną dzierżawę usługi AAD, uruchomić ponownie skrypt i wybrać opcję użycia.
3. Kontynuuj bez uwierzytelniania.  Ponieważ używasz usługi Micro Services lokalnie, jest to akceptowalne, ale nie naśladuje środowiska produkcyjnego.  

## <a name="next-steps"></a>Następne kroki

Teraz, po pomyślnym wdrożeniu usług OPC bliźniaczych w istniejącym projekcie, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat wdrażania modułów OPC bliźniaczych](howto-opc-twin-deploy-modules.md)
