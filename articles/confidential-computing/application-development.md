---
title: Narzędzia deweloperskie do poufnego przetwarzania na platformie Azure
description: Tworzenie aplikacji do poufnego przetwarzania przy użyciu narzędzi i bibliotek
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 571c1a4ce545976db09f46a07d963d5344c02c29
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791017"
---
# <a name="application-development-on-intel-sgx"></a>Tworzenie aplikacji w technologii Intel SGX 


Infrastruktura poufnego przetwarzania wymaga określonych narzędzi i oprogramowania. Na tej stronie omówiono w szczególności pojęcia związane z tworzeniem aplikacji dla maszyn wirtualnych do poufnego przetwarzania na platformie Azure działających na platformie Intel SGX. Przed przeczytaniem tej strony przeczytaj wprowadzenie do maszyn wirtualnych [i enklaw Intel SGX.](confidential-computing-enclaves.md) 

Aby wykorzystać możliwości enklaw i środowisk izolowanych, należy użyć narzędzi, które obsługują poufne przetwarzanie. Istnieją różne narzędzia, które obsługują tworzenie aplikacji enklawy. Można na przykład użyć tych platform typu open source: 

- [Zestaw Open Enclave Software Development Kit (OE SDK)](#oe-sdk)
- [Zestaw EGo Software Development Kit](#ego)
- [Confidential Consortium Framework (CCF)](#ccf)

## <a name="overview"></a>Omówienie

Aplikacja sbudowaną z enklawami jest partycjonowana na dwa sposoby:

1. Składnik "niezaufany" (host)
1. Składnik "zaufany" (enklawa)


![Opracowywanie aplikacji](media/application-development/oe-sdk.png)


**Host to** miejsce, w którym aplikacja enklawy działa w oparciu o program i jest niezaufanym środowiskiem. Host nie może uzyskać dostępu do kodu enklawy wdrożonego na hoście. 

**Enklawa to** miejsce, w którym jest uruchamiany kod aplikacji i jej buforowane dane/pamięć. Bezpieczne obliczenia powinny mieć miejsce w enklawach, aby zapewnić bezpieczeństwo wpisów tajnych i poufnych danych. 


Podczas projektowania aplikacji ważne jest, aby zidentyfikować i określić, która część aplikacji musi być uruchamiana w enklawach. Kod, który chcesz umieścić w zaufanym składniku, jest odizolowany od pozostałej części aplikacji. Po zainicjowania enklawy i załadowaniu kodu do pamięci nie można odczytać ani zmienić tego kodu z niezaufanych składników. 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>Open Enclave Software Development Kit (OE SDK) <a id="oe-sdk"></a>

Jeśli chcesz napisać kod uruchamiany w enklawie, użyj biblioteki lub struktury obsługiwanej przez dostawcę. Zestaw [SDK open enclave](https://github.com/openenclave/openenclave) (OE SDK) to zestaw SDK typu open source, który umożliwia abstrakcję na różnych poufnych urządzeniach z obsługą obliczeń. 

Zestaw SDK OE jest zbudowany jako pojedyncza warstwa abstrakcji na dowolnym sprzęcie w dowolnym dostawcy CSP. Zestaw SDK OE może być używany na maszynach wirtualnych poufnego przetwarzania na platformie Azure do tworzenia i uruchamiania aplikacji na podstawie enklaw.

## <a name="ego-software-development-kit"></a>EGo Software Development Kit <a id="ego"></a>

[EGo](https://ego.dev/) to zestaw SDK typu open source, który umożliwia uruchamianie aplikacji napisanych w języku programowania Go w enklawach. Aplikacja EGo jest zbudowana na podstawie zestawu SDK OE i zawiera bibliotekę języka Go w enklawie do zaświadczenia i zapieczętowania. Wiele istniejących aplikacji go działa w ramach programu EGo bez żadnych modyfikacji.  

## <a name="confidential-consortium-framework-ccf"></a>Confidential Consortium Framework (CCF) <a id="ccf"></a>

CCF [to](https://github.com/Microsoft/CCF) rozproszona sieć węzłów, z których każdy ma własne enklawy. Sieć zaufanych węzłów umożliwia uruchamianie rejestru rozproszonego. Rejestr zapewnia bezpieczne, niezawodne składniki do użycia z protokołem. 

![Węzły CCF](media/application-development/ccf.png)

Ta struktura typu open source zapewnia wysoką poufność w całym zakresie, a także nadzór nad konsorcjum dla łańcucha bloków. Z każdym węzłem korzystającym z funkcji TEE można zapewnić bezpieczeństwo porozumienia i przetwarzania transakcji.


## <a name="next-steps"></a>Następne kroki 
- [Wdrażanie poufnego przetwarzania DCsv2-Series wirtualnej](quick-create-portal.md)
- [Pobierz i zainstaluj zestaw SDK OE i rozpocznij tworzenie aplikacji](https://github.com/openenclave/openenclave)
