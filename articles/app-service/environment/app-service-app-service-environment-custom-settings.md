---
title: Konfigurowanie ustawień niestandardowych
description: Skonfiguruj ustawienia, które mają zastosowanie do całego środowiska Azure App Service. Dowiedz się, jak to zrobić za pomocą szablonów Azure Resource Manager.
author: stefsch
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 10/03/2020
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: f9dd63df6d743f12fa7addb3c814e763d37dc61f
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708768"
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

App Service Environment działa jako system czarnego pudełka, w którym nie są widoczne wewnętrzne składniki lub komunikacja w systemie. Aby zapewnić większą przepływność, szyfrowanie nie jest domyślnie włączone między składnikami wewnętrznymi. System jest zabezpieczony, ponieważ ruch jest całkowicie niedostępny do monitorowania lub dostępu. Jeśli istnieje wymóg zgodności, który wymaga pełnego szyfrowania ścieżki danych od końca do końca, istnieje możliwość włączenia tego elementu przy użyciu clusterSetting.  

```json
"clusterSettings": [
    {
        "name": "InternalEncryption",
        "value": "true"
    }
],
```

Po włączeniu InternalEncryption clusterSetting może mieć wpływ na wydajność systemu. Po wprowadzeniu zmiany w celu włączenia InternalEncryption środowisko ASE będzie w stanie niestabilnym, dopóki zmiana nie zostanie w pełni rozpropagowana. Zakończenie propagacji zmiany może potrwać kilka godzin, w zależności od liczby wystąpień w środowisku ASE. Zdecydowanie zalecamy, aby nie włączać tego na platformie ASE, gdy jest ona używana. Jeśli musisz włączyć tę funkcję w aktywnie używanym środowisku ASE, zdecydowanie zalecamy przekierowanie ruchu do środowiska kopii zapasowej do momentu zakończenia operacji. 

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
Kolejne pytanie klientów dotyczy tego, czy mogą oni modyfikować listę szyfrów wynegocjowaną przez serwer. Ten cel można osiągnąć, modyfikując atrybut **clusterSettings**, jak pokazano poniżej. Listę dostępnych zestawów szyfrowania można pobrać z [tego artykułu MSDN](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

```json
"clusterSettings": [
    {
        "name": "FrontEndSSLCipherSuiteOrder",
        "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
    }
],
```

> [!WARNING]
> Jeśli dla zestawów szyfrowania zostaną skonfigurowane nieprawidłowe wartości, których nie będzie mógł rozpoznać kanał SChannel, cała komunikacja protokołu TLS z serwerem może przestać działać. W takim przypadku trzeba będzie usunąć wpis *FrontEndSSLCipherSuiteOrder* z atrybutu **clusterSettings** i przesłać zaktualizowany szablon usługi Resource Manager, aby przywrócić domyślne ustawienia zestawu szyfrowania.  Tej funkcji należy używać ostrożnie.

## <a name="get-started"></a>Wprowadzenie
W witrynie szablonów szybkiego startu Azure Resource Manager można znaleźć szablon z podstawową definicją umożliwiającą [tworzenie środowiska App Service Environment](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
