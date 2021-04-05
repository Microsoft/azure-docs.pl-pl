---
title: Konfigurowanie ustawień niestandardowych
description: Skonfiguruj ustawienia, które mają zastosowanie do całego środowiska Azure App Service. Dowiedz się, jak to zrobić za pomocą szablonów Azure Resource Manager.
author: ccompy
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 5c1e81d02aa35a40a296f04e456be09eeed10331
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226392"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Niestandardowe ustawienia konfiguracji dla środowisk App Service Environment
## <a name="overview"></a>Omówienie
Ponieważ środowiska App Service Environment (ASE) są izolowane do jednego klienta, istnieją pewne ustawienia konfiguracji, które można stosować wyłącznie do środowisk App Service Environment. W tym artykule opisano różne dostosowania specjalne, które są dostępne w przypadku środowisk App Service Environment.

Jeśli nie masz środowiska App Service Environment, zobacz, [jak utworzyć środowisko App Service Environment](app-service-web-how-to-create-an-app-service-environment.md).

Dostosowania środowiska App Service Environment można przechowywać przy użyciu tablicy w nowym atrybucie **clusterSettings**. Ten atrybut można znaleźć w słowniku „Właściwości” jednostki *hostingEnvironments* usługi Azure Resource Manager.

Poniższy skrócony fragment kodu szablonu usługi Resource Manager przedstawia atrybut **clusterSettings**:

```json
"resources": [
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/hostingEnvironments",
    "name": ...,
    "location": ...,
    "properties": {
        "clusterSettings": [
            {
                "name": "nameOfCustomSetting",
                "value": "valueOfCustomSetting"
            }
        ],
        "workerPools": [ ...],
        etc...
    }
}
```

Atrybut **clusterSettings** można uwzględnić w szablonie usługi Resource Manager, aby zaktualizować usługę App Service Environment.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Aktualizowanie usługi App Service Environment za pomocą witryny Azure Resource Explorer
Usługę App Service Environment można również aktualizować przy użyciu witryny [Azure Resource Explorer](https://resources.azure.com).  

1. W Eksplorator zasobów przejdź do węzła App Service Environment (**subskrypcje**  >  **resourceGroups**  >  **dostawcy**  >  **Microsoft. Web**  >  **hostingEnvironments**). Następnie kliknij wybrane środowisko App Service Environment, które chcesz zaktualizować.
2. W okienku po prawej stronie kliknij pozycję **Do odczytu/zapisu** na górnym pasku narzędzi, aby zezwolić na interakcyjne edytowanie w usłudze Resource Explorer.  
3. Kliknij niebieski przycisk **Edytuj**, aby umożliwić edytowanie szablonu usługi Resource Manager.
4. Przewiń okienko po prawej stronie w dół. Atrybut **clusterSettings** znajduje się na samym dole strony, gdzie można wprowadzić lub zaktualizować jego wartość.
5. Wpisz (lub skopiuj i wklej) wybraną tablicę wartości konfiguracji w obszarze atrybutu **clusterSettings**.  
6. Kliknij zielony przycisk **PUT** w górnej części okienka po prawej stronie, aby zatwierdzić zmianę środowiska App Service Environment.

Bez względu na to, jak prześlesz zmianę, jej zastosowanie potrwa około 30 minut pomnożonych przez liczbę frontonów w środowisku App Service Environment.
Jeśli na przykład środowisko App Service Environment ma cztery frontony, kończenie aktualizacji konfiguracji potrwa około dwie godziny. W trakcie wdrażania żadne inne operacje skalowania ani zmian konfiguracji nie mogą być wykonywane w środowisku App Service Environment.

## <a name="enable-internal-encryption"></a>Włącz szyfrowanie wewnętrzne

App Service Environment działa jako system czarnego pudełka, w którym nie są widoczne wewnętrzne składniki lub komunikacja w systemie. Aby zapewnić większą przepływność, szyfrowanie nie jest domyślnie włączone między składnikami wewnętrznymi. System jest zabezpieczony, ponieważ ruch jest niedostępny do monitorowania lub dostępu. Jeśli istnieje wymóg zgodności, który wymaga pełnego szyfrowania ścieżki danych od końca do końca, istnieje możliwość włączenia szyfrowania pełnej ścieżki danych za pomocą clusterSetting.  

```json
"clusterSettings": [
    {
        "name": "InternalEncryption",
        "value": "true"
    }
],
```
Ustawienie InternalEncryption na true szyfruje wewnętrzny ruch sieciowy w środowisku ASE między frontonami i pracownikami, szyfruje plik stronicowania, a także szyfruje dyski procesów roboczych. Po włączeniu InternalEncryption clusterSetting może mieć wpływ na wydajność systemu. Po wprowadzeniu zmiany w celu włączenia InternalEncryption środowisko ASE będzie w stanie niestabilnym, dopóki zmiana nie zostanie w pełni rozpropagowana. Zakończenie propagacji zmiany może potrwać kilka godzin, w zależności od liczby wystąpień w środowisku ASE. Zdecydowanie zalecamy, aby nie włączać InternalEncryption w środowisku ASE, gdy jest on używany. Jeśli konieczne jest włączenie InternalEncryption na aktywnie używanym środowisku ASE, zdecydowanie zalecamy przekierowanie ruchu do środowiska kopii zapasowej do momentu zakończenia operacji. 


## <a name="disable-tls-10-and-tls-11"></a>Wyłączanie protokołów TLS 1.0 i TLS 1.1

Jeśli chcesz zarządzać ustawieniami protokołu TLS dla każdej aplikacji z osobna, możesz skorzystać ze wskazówek zawartych w dokumentacji [Wymuszanie ustawień protokołu TLS](../configure-ssl-bindings.md#enforce-tls-versions). 

Jeśli chcesz wyłączyć całych ruch przychodzący protokołów TLS 1.0 i TLS 1.1 dla wszystkich aplikacji w środowisku ASE, możesz ustawić następujący wpis **clusterSettings**:

```json
"clusterSettings": [
    {
        "name": "DisableTls1.0",
        "value": "1"
    }
],
```

W nazwie ustawienia znajduje się numer wersji 1.0, ale po skonfigurowaniu wyłączane są protokoły TLS 1.0 i TLS 1.1.

## <a name="change-tls-cipher-suite-order"></a>Zmienianie kolejności zestawów szyfrowania protokołu TLS
Środowisko ASE obsługuje zmianę pakietu szyfrowania z domyślnego. Domyślny zestaw szyfrów jest tym samym zestawem, który jest używany w usłudze wielu dzierżawców. Zmiana mechanizmów szyfrowania ma wpływ na całe wdrożenie App Service, co jest możliwe tylko w przypadku środowiska ASE o pojedynczej dzierżawie. Istnieją dwa mechanizmy szyfrowania wymagane przez środowisko ASE; TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 i TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256. Jeśli chcesz pracować ze środowiskiem ASE z najsilniejszym i najbardziej minimalnym zestawem mechanizmów szyfrowania, użyj tylko dwóch wymaganych szyfrów. Aby skonfigurować środowisko ASE do korzystania tylko z wymaganych szyfrów, zmodyfikuj **clusterSettings** , jak pokazano poniżej. 

```json
"clusterSettings": [
    {
        "name": "FrontEndSSLCipherSuiteOrder",
        "value": "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256"
    }
],
```

> [!WARNING]
> Jeśli dla zestawów szyfrowania zostaną skonfigurowane nieprawidłowe wartości, których nie będzie mógł rozpoznać kanał SChannel, cała komunikacja protokołu TLS z serwerem może przestać działać. W takim przypadku trzeba będzie usunąć wpis *FrontEndSSLCipherSuiteOrder* z atrybutu **clusterSettings** i przesłać zaktualizowany szablon usługi Resource Manager, aby przywrócić domyślne ustawienia zestawu szyfrowania.  Tej funkcji należy używać ostrożnie.

## <a name="get-started"></a>Rozpoczęcie pracy
W witrynie szablonów szybkiego startu Azure Resource Manager można znaleźć szablon z podstawową definicją umożliwiającą [tworzenie środowiska App Service Environment](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
