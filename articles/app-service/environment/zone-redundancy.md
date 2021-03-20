---
title: Obsługa stref dostępności dla środowisk App Service
description: Dowiedz się, jak wdrożyć środowiska App Service, aby aplikacje były nadmiarowe względem strefy.
author: ccompy
ms.assetid: 24e3e7eb-c160-49ff-8d46-e947818ef186
ms.topic: article
ms.date: 07/15/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 383b5bb5c7295fe54efda883e47b9b2338286de5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624729"
---
# <a name="availability-zone-support-for-app-service-environments"></a>Obsługa stref dostępności dla środowisk App Service

Środowiska App Service (ASE) można wdrożyć do Strefy dostępności (AZ).  Klienci mogą wdrożyć wewnętrzny moduł równoważenia obciążenia (ILB) środowisk ASE w określonym AZ w regionie świadczenia usługi Azure. Jeśli przypinasz ILB ASE do określonego AZ, zasoby używane przez ILB ASE zostaną przypięte do określonego AZ lub wdrożone w strefie nadmiarowej.  

ILB ASE, który jest jawnie wdrożony w AZ jest traktowany jako zasób strefy, ponieważ ILB ASE jest przypięty do określonej strefy. Następujące zależności ILB ASE zostaną przypięte do określonej strefy:

- adres IP wewnętrznego modułu równoważenia obciążenia środowiska ASE
- zasoby obliczeniowe używane przez środowisko ASE do zarządzania i uruchamiania aplikacji sieci Web

Zdalny magazyn plików dla aplikacji sieci Web wdrożonych w ILB ASE korzysta z magazynu Strefowo nadmiarowego (ZRS).

O ile nie są stosowane kroki opisane w tym artykule, ILB środowisk ASE nie są automatycznie wdrażane w sposób wielostrefowy. Nie można przypiąć zewnętrznego środowiska ASE z publicznym adresem IP do określonej strefy dostępności. 

Strefę środowisk ASE ILB można utworzyć w jednym z następujących regionów:

- Australia Wschodnia
- Kanada Środkowa
- Central US
- East US
- Wschodnie stany USA 2
- Wschodnie stany USA 2 (— EUAP)
- Francja Środkowa 
- Japonia Wschodnia
- Europa Północna
- West Europe
- Southeast Asia
- Południowe Zjednoczone Królestwo
- Zachodnie stany USA 2

Aplikacje wdrożone na zona ILB ASE będą nadal działać i obsługują ruch w tym środowisku, nawet jeśli inne strefy w tym samym regionie pozostaną nieprzerwane.  Możliwe jest zachowanie nieśrodowiska uruchomieniowego, w tym: w przypadku awarii w innych strefach dostępności może nadal wpływać na skalowanie planu usługi aplikacji, tworzenie aplikacji, konfigurację aplikacji oraz publikowanie aplikacji. Przypięty do strefy wdrożenie zona ILB ASE zapewnia tylko ciągły czas pracy dla już wdrożonych aplikacji.

## <a name="how-to-deploy-an-app-service-environment-in-an-availability-zone"></a>Jak wdrożyć App Service Environment w strefie dostępności ##

ILB środowisk ASE należy utworzyć przy użyciu szablonów usługi ARM. Gdy strefa ILB ASE zostanie utworzona za pomocą szablonu ARM, można ją wyświetlać i współdziałać za pośrednictwem Azure Portal i interfejsu wiersza polecenia.  Szablon ARM jest wymagany tylko w przypadku początkowego tworzenia stref ILB ASE.

Jedyną zmianą potrzebną w szablonie ARM, aby określić strefę ILB ASE, jest nowa właściwość ***Zones** _. Właściwość _ *_stref_** powinna mieć ustawioną wartość "1", "2" lub "3" w zależności od strefy dostępności logicznej, do której ma zostać przypięty ILB ASE.

Poniższy fragment kodu szablonu ARM przedstawia nową właściwość ***Zones*** określającą, że ILB ASE ma być przypięty do strefy 2.

```
   "resources": [
      {
         "type": "Microsoft.Web/hostingEnvironments",
         "kind": "ASEV2",
         "name": "yourASENameHere",
         "apiVersion": "2015-08-01",
         "location": "your location here",
         "zones": [
            "2"
         ],
         "properties": {
         "name": "yourASENameHere",
         "location": "your location here",
         "ipSslAddressCount": 0,
         "internalLoadBalancingMode": "3",
         "dnsSuffix": "contoso-internal.com",
         "virtualNetwork": {
             "Id": "/subscriptions/your-subscription-id-here/resourceGroups/your-resource-group-here/providers/Microsoft.Network/virtualNetworks/your-vnet-name-here",
             "Subnet": "yourSubnetNameHere"
          }
         }
      }
    ]
```

Aby zapewnić nadmiarową strefę aplikacji, należy wdrożyć dwa strefy ILB środowisk ASE. Dwa strefy ILB środowisk ASE muszą znajdować się w osobnych strefach dostępności. Następnie należy wdrożyć aplikacje w każdym z ILB środowisk ASE. Po utworzeniu aplikacji należy skonfigurować rozwiązanie do równoważenia obciążenia. Zalecanym rozwiązaniem jest wdrożenie [strefy nadmiarowe Application Gateway](../../application-gateway/application-gateway-autoscaling-zone-redundant.md) nadrzędnej ILB środowisk ASE. 

## <a name="in-region-data-residency"></a>W miejscu zamieszkania danych regionu ##

ILB środowisk ASE wdrożony w strefie dostępności będzie przechowywać tylko dane klienta w regionie, w którym wdrożono strefę ILB ASE. Zarówno zawartość pliku witryny sieci Web, jak i ustawienia wprowadzone przez klienta oraz wpisy tajne przechowywane w App Service pozostają w regionie, w którym wdrożono strefę ILB ASE.

Klienci zapewniają dostęp do danych w jednym regionie, wykonując czynności opisane wcześniej w sekcji "jak wdrożyć App Service Environment w strefie dostępności". Konfigurując App Service Environment zgodnie z tymi krokami, App Service Environment wdrożony w strefie dostępności spełnia wymagania dotyczące miejsca zamieszkania danych regionu, w tym te określone w [Centrum zaufania Azure](https://azuredatacentermap.azurewebsites.net/).

Klienci mogą sprawdzić, czy App Service Environment jest prawidłowo skonfigurowany do przechowywania danych w jednym regionie, wykonując następujące czynności: 

1. Przy użyciu [Eksplorator zasobów](https://resources.azure.com)przejdź do zasobu ARM dla App Service Environment.  Środowisk ASE są wymienione w obszarze *dostawcy/Microsoft. Web/hostingEnvironments*.
2. Jeśli właściwość *Zones* istnieje w widoku składni JSON w usłudze ARM i zawiera tablicę JSON o pojedynczej wartości z wartością "1", "2" lub "3", wówczas środowisko ASE jest zonally wdrożone i dane klienta pozostają w tym samym regionie.
2. Jeśli właściwość *Zones* nie istnieje lub właściwość nie ma prawidłowej wartości strefy określonej wcześniej, środowisko ASE nie zostanie zonally wdrożone i dane klienta nie są przechowywane wyłącznie w tym samym regionie.
