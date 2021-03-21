---
title: Narzędzia programistyczne do przetwarzania poufnego platformy Azure
description: Korzystanie z narzędzi i bibliotek do tworzenia aplikacji do celów poufnych
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 0ba6ee92111da66a2118ba4c490b94e5bc9449e0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551389"
---
# <a name="application-development-on-intel-sgx"></a>Opracowywanie aplikacji na Intel SGX 


Poufna infrastruktura obliczeniowa wymaga określonych narzędzi i oprogramowania. Na tej stronie omówiono koncepcje związane z programowaniem aplikacji w przypadku maszyn wirtualnych, które działają na platformie Intel SGX. Przed przeczytaniem tej strony [zapoznaj się z wprowadzeniem maszyn wirtualnych Intel SGX i enclaves](confidential-computing-enclaves.md). 

Aby wykorzystać możliwości enclaves i izolowanych środowisk, musisz użyć narzędzi, które obsługują dane poufne. Istnieją różne narzędzia, które obsługują Programowanie aplikacji enklawy. Można na przykład użyć tych platform typu "open source": 

- [Otwarty zestaw enklawy Software Development Kit (SDK programu OE)](#oe-sdk)
- [Struktura konsorcjum poufnego (CCF)](#ccf)

## <a name="overview"></a>Omówienie

Aplikacja skompilowana za pomocą enclaves jest partycjonowana na dwa sposoby:

1. Składnik "niezaufany" (Host)
1. Składnik "zaufany" (enklawy)


![Opracowywanie aplikacji](media/application-development/oe-sdk.png)


**Host** jest miejscem, w którym aplikacja enklawy jest uruchomiona w systemie i jest środowiskiem niezaufanym. Na hoście nie można uzyskać dostępu do kodu enklawy wdrożonego na hoście. 

**Enklawy polega na** tym, że kod aplikacji i jego buforowane dane/pamięć są uruchamiane. Aby zapewnić tajne i poufne dane, ochrona powinna być wykonywana w enclaves. 


Podczas projektowania aplikacji ważne jest, aby identyfikować i określić, jaka część aplikacji musi być uruchamiana w enclaves. Kod wybrany do umieszczenia w zaufanym składniku jest odizolowany od reszty aplikacji. Po zainicjowaniu enklawy i załadowaniu kodu do pamięci kod nie może zostać odczytany lub zmieniony z niezaufanych składników. 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>Otwórz zestaw enklawy Software Development Kit (zestaw SDK programu OE) <a id="oe-sdk"></a>

Użyj biblioteki lub platformy obsługiwanej przez dostawcę, jeśli chcesz napisać kod, który jest uruchamiany w enklawy. [Open ENKLAWY SDK](https://github.com/openenclave/openenclave) (zestaw SDK programu OE) to zestaw SDK typu open source, który umożliwia abstrakcję wielu różnych sprzętowych urządzeń z obsługą informacji. 

Zestaw SDK programu OE jest oparty na jednej warstwie abstrakcji na dowolnym sprzęcie dowolnego dostawcy usług kryptograficznych. Zestaw OE SDK może być używany w odniesieniu do poufnych maszyn wirtualnych platformy Azure do tworzenia i uruchamiania aplikacji w oparciu o enclaves.

## <a name="confidential-consortium-framework-ccf"></a>Struktura konsorcjum poufnego (CCF) <a id="ccf"></a>

[CCF](https://github.com/Microsoft/CCF) to rozproszona sieć węzłów, z których każdy korzysta z własnych enclavesów. Sieć zaufanego węzła umożliwia uruchamianie księgi rozproszonej. W księdze znajdują się bezpieczne, niezawodne składniki używane przez protokół. 

![CCF węzły](media/application-development/ccf.png)

Ta struktura "open source" umożliwia wysoką i szczegółową poufność oraz zarządzanie konsorcjum dla łańcucha bloków. W każdym węźle przy użyciu TEEs można zapewnić bezpieczny konsensus i przetwarzanie transakcji.


## <a name="next-steps"></a>Następne kroki 
- [Wdróż maszynę wirtualną w ramach obliczeń poufnych DCsv2-Series](quick-create-portal.md)
- [Pobierz i Zainstaluj zestaw SDK programu OE i zacznij opracowywać aplikacje](https://github.com/openenclave/openenclave)