---
title: Jak wdrożyć moduł OPC bliźniaczy w istniejącym projekcie platformy Azure | Microsoft Docs
description: W tym artykule opisano sposób wdrażania sznurka OPC w istniejącym projekcie. Możesz również dowiedzieć się, jak rozwiązywać problemy z błędami wdrożenia.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 4535818898459f1eb183026629bc338524c1efd9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646412"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Wdróż bliźniaczy OPC do istniejącego projektu

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

Moduł OPC bliźniaczy działa na IoT Edge i oferuje kilka usług granicznych dla usług OPC i rejestrów.

Mikrousługa OPC sznurów ułatwia komunikację między operatorami fabryki i urządzeniami serwera OPC UA w obłodze fabryki za pośrednictwem modułu OPCe IoT Edge. Mikrousługa udostępnia usługi OPC UA (przeglądanie, odczytywanie, zapisywanie i wykonywanie) za pośrednictwem interfejsu API REST. 

Mikrousługa rejestru urządzeń OPC UA zapewnia dostęp do zarejestrowanych aplikacji OPC UA i ich punktów końcowych. Operatorzy i Administratorzy mogą rejestrować i wyrejestrować nowe aplikacje OPC UA oraz przeglądać istniejące, łącznie z ich punktami końcowymi. Oprócz zarządzania aplikacjami i punktami końcowymi usługa rejestru również kataloguje zarejestrowane OPCy bliźniaczy IoT Edge moduły. Interfejs API usługi zapewnia kontrolę funkcjonalności modułu brzegowego, na przykład uruchamianie lub zatrzymywanie odnajdowania serwerów (usług skanowania) lub aktywowanie nowego punktu końcowego bliźniaczych reprezentacji, do którego można uzyskać dostęp za pomocą mikrousługi OPC.

Rdzeń modułu jest tożsamością kierownika. Nadzorca zarządza sznurem końcowym, który odnosi się do punktów końcowych serwera OPC UA, które są aktywowane przy użyciu odpowiedniego interfejsu API OPC UA. Ten punkt końcowy bliźniaczych reprezentacji tłumaczy dane JSON OPC UA odebrane z mikrousługi OPC z sieci w chmurze do komunikatów binarnych programu OPC UA, które są wysyłane za pośrednictwem bezpiecznego kanału do zarządzanego punktu końcowego. Nadzorca udostępnia również usługi odnajdywania, które wysyłają zdarzenia odnajdywania urządzeń do usługi OPC UA Device dołączania do przetwarzania, gdzie te zdarzenia powodują aktualizacje rejestru OPC UA.  W tym artykule pokazano, jak wdrożyć moduł OPC bliźniaczy w istniejącym projekcie.

> [!NOTE]
> Aby uzyskać więcej informacji na temat szczegółów wdrożenia i instrukcje, zobacz [repozytorium](https://github.com/Azure/azure-iiot-opc-twin-module)GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zainstalowano rozszerzenia PowerShell i [AzureRM PowerShell](/powershell/azure/azurerm/install-azurerm-ps) . Jeśli jeszcze tego nie zrobiono, Sklonuj to repozytorium GitHub. Uruchom następujące polecenia w programie PowerShell:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Wdrażanie przemysłowych usług IoT na platformie Azure

1. W sesji programu PowerShell uruchom polecenie:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. Postępuj zgodnie z monitami, aby przypisać nazwę do grupy zasobów wdrożenia i nazwę witryny sieci Web.   Skrypt wdraża mikrousługi i zależności platformy Azure w grupie zasobów w subskrypcji platformy Azure.  Skrypt rejestruje również aplikację w dzierżawie usługi Azure Active Directory (AAD), aby obsługiwała uwierzytelnianie przy użyciu protokołu OAUTH.  Wdrożenie potrwa kilka minut.  Przykład tego, co widzisz po pomyślnym wdrożeniu rozwiązania:

   ![Przemysłowe IoT OPC do wdrożenia w istniejącym projekcie](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   Dane wyjściowe zawierają adres URL publicznego punktu końcowego. 

3. Po pomyślnym zakończeniu działania skryptu wybierz, czy chcesz zapisać `.env` plik.  Aby `.env` nawiązać połączenie z punktem końcowym w chmurze za pomocą narzędzi takich jak konsola lub wdrożenie modułów do programowania i debugowania, potrzebny jest plik środowiska.

## <a name="troubleshooting-deployment-failures"></a>Rozwiązywanie problemów z błędami wdrożenia

### <a name="resource-group-name"></a>Nazwa grupy zasobów

Upewnij się, że używana jest krótka i prosta Nazwa grupy zasobów.  Nazwa jest używana również do nazywania zasobów w taki sposób, aby musiała być zgodna z wymaganiami dotyczącymi nazw zasobów.  

### <a name="website-name-already-in-use"></a>Nazwa witryny sieci Web jest już używana

Istnieje możliwość, że nazwa witryny sieci Web jest już używana.  W przypadku uruchomienia tego błędu należy użyć innej nazwy aplikacji.

### <a name="azure-active-directory-aad-registration"></a>Rejestracja w usłudze Azure Active Directory (AAD)

Skrypt wdrożenia próbuje zarejestrować dwie aplikacje usługi AAD w Azure Active Directory.  W zależności od uprawnień wybranej dzierżawy usługi AAD wdrożenie może się nie powieść. Dostępne są dwie opcje:

* Jeśli wybrano dzierżawę usługi AAD z listy dzierżawców, uruchom ponownie skrypt i wybierz inny z listy.
* Alternatywnie można wdrożyć prywatną dzierżawę usługi AAD w innej subskrypcji, uruchomić ponownie skrypt i wybrać opcję użycia.

> [!WARNING]
> NIGDY nie Kontynuuj bez uwierzytelniania.  Jeśli zdecydujesz się to zrobić, każdy będzie mógł uzyskać dostęp do OPCych punktów końcowych z Internetu z nieuwierzytelnionym.   Zawsze możesz wybrać [opcję wdrożenia "lokalna"](howto-opc-twin-deploy-dependencies.md) , aby uruchomić opony.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Wdróż wszystko w jednym z firmowych demonstracji usług IoT

Zamiast tylko usługi i zależności można także wdrożyć demonstrację wszystkie w jednym miejscu.  Wszystko w jednej wersji demonstracyjnej zawiera trzy serwery OPC UA, moduł OPCe, wszystkie mikrousługi i przykładową aplikację sieci Web.  Jest ona przeznaczona do celów demonstracyjnych.

1. Upewnij się, że masz klon repozytorium (Zobacz powyżej). Otwórz wiersz polecenia programu PowerShell w folderze głównym repozytorium i uruchom:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. Postępuj zgodnie z monitami, aby przypisać nową nazwę do grupy zasobów i nazwy do witryny sieci Web.  Po pomyślnym wdrożeniu skrypt będzie wyświetlał adres URL punktu końcowego aplikacji sieci Web.

## <a name="deployment-script-options"></a>Opcje skryptu wdrożenia

Skrypt przyjmuje następujące parametry:

```powershell
-type
```

Typ wdrożenia (maszyna wirtualna, lokalna, demonstracja)

```powershell
-resourceGroupName
```

Może to być nazwa istniejącej lub nowej grupy zasobów.

```powershell
-subscriptionId
```

Opcjonalne, Identyfikator subskrypcji, w której zostaną wdrożone zasoby.

```powershell
-subscriptionName
```

Lub nazwa subskrypcji.

```powershell
-resourceGroupLocation
```

Opcjonalna — lokalizacja grupy zasobów. Jeśli ta wartość jest określona, program podejmie próbę utworzenia nowej grupy zasobów w tej lokalizacji.

```powershell
-aadApplicationName
```

Nazwa aplikacji usługi AAD do zarejestrowania.

```powershell
-tenantId
```

Dzierżawa usługi AAD do użycia.

```powershell
-credentials
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wdrożyć sznury OPC w istniejącym projekcie, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Bezpieczna komunikacja z OPC UA Client i OPC UA PLC](howto-opc-vault-secure.md)