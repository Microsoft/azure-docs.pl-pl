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
ms.openlocfilehash: ae0e18a2dfbce52e50b4a8b58d478a46dff12922
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91282106"
---
# <a name="deploying-dependencies-for-local-development"></a>Wdrażanie zależności dla lokalnego tworzenia

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

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

2. Postępuj zgodnie z monitami, aby przypisać nazwę do grupy zasobów dla wdrożenia.  Skrypt wdraża tylko zależności od tej grupy zasobów w ramach subskrypcji platformy Azure, ale nie do mikrousług.  Skrypt rejestruje również aplikację w usłudze Azure AD.  Jest to konieczne do obsługi uwierzytelniania OAUTH.  Wdrożenie może potrwać kilka minut.

3. Po zakończeniu działania skryptu możesz wybrać opcję zapisania pliku ENV.  Plik środowiska ENV to plik konfiguracji wszystkich usług i narzędzi, które mają być uruchamiane na komputerze deweloperskim.  

## <a name="troubleshooting-deployment-failures"></a>Rozwiązywanie problemów z błędami wdrożenia

### <a name="resource-group-name"></a>Nazwa grupy zasobów

Upewnij się, że używana jest krótka i prosta Nazwa grupy zasobów.  Nazwa jest używana również do nazywania zasobów w taki sposób, aby musiała być zgodna z wymaganiami dotyczącymi nazw zasobów.  

### <a name="azure-active-directory-ad-registration"></a>Rejestracja w usłudze Azure Active Directory (AD)

Skrypt wdrażania próbuje zarejestrować aplikacje usługi Azure AD w usłudze Azure AD.  W zależności od uprawnień do wybranej dzierżawy usługi Azure AD może to się nie powieść. Dostępne są trzy opcje:

1. W przypadku wybrania dzierżawy usługi Azure AD z listy dzierżawców Uruchom ponownie skrypt i wybierz inny z listy.
2. Alternatywnie można wdrożyć prywatną dzierżawę usługi Azure AD, ponownie uruchomić skrypt i wybrać go do użycia.
3. Kontynuuj bez uwierzytelniania.  Ponieważ używasz usługi Micro Services lokalnie, jest to akceptowalne, ale nie naśladuje środowiska produkcyjnego.  

## <a name="next-steps"></a>Następne kroki

Teraz, po pomyślnym wdrożeniu usług OPC bliźniaczych w istniejącym projekcie, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat wdrażania modułów OPC bliźniaczych](howto-opc-twin-deploy-modules.md)
