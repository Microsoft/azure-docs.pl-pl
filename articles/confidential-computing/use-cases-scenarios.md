---
title: Typowe scenariusze obliczeniowe i przypadki użycia na platformie Azure
description: Dowiedz się, jak używać poufnych obliczeń w danym scenariuszu.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.topic: overview
ms.date: 9/22/2020
ms.author: jencook
ms.openlocfilehash: e1280ac90032869616830ccb931b367ff22f8bfb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91001011"
---
# <a name="common-scenarios-for-azure-confidential-computing"></a>Typowe scenariusze dotyczące przetwarzania poufnego platformy Azure

Ten artykuł zawiera omówienie kilku typowych scenariuszy dotyczących przetwarzania danych poufnych platformy Azure. Zalecenia przedstawione w tym artykule stanowią punkt wyjścia podczas opracowywania aplikacji przy użyciu poufnych usług i struktur obliczeniowych. 

Po przeczytaniu tego artykułu będzie można odpowiedzieć na następujące pytania:

- Jakie są scenariusze związane z usługą Azure poufne Obliczanie?-
- Jakie korzyści wiążą się z korzystaniem z platformy Azure w przypadku scenariuszy obejmujących wiele firm?
- Jakie korzyści wynikają z używania poufnego rozwiązania do przetwarzania danych platformy Azure w sieci łańcucha bloków?


## <a name="secure-multi-party-computation"></a>Bezpieczne obliczenia wieloskładnikowe
Dane poufne platformy Azure umożliwiają przetwarzanie danych z wielu źródeł bez uwidaczniania danych wejściowych innym stronom. Ten typ bezpiecznego obliczenia umożliwia wiele scenariuszy, takich jak: pranie antypieniężne, wykrywanie oszustw i bezpieczna analiza danych dotyczących opieki zdrowotnej.

Wiele źródeł może przekazywać swoje dane do jednego enklawy na maszynie wirtualnej. Jedna ze stron informuje enklawy o konieczności obliczenia lub przetwarzania danych. Żadna ze stron (nawet jeden z wykonywania analizy) nie może zobaczyć danych innych jednostek, które zostały przekazane do enklawy. 

W przypadku zabezpieczania obliczeń szyfrowanych danych odbywa się w enklawy, enklawy odszyfrowuje dane przy użyciu klucza, wykonuje analizę, pobiera wynik i zwraca zaszyfrowany wynik, który strona może odszyfrować przy użyciu wydanego klucza. 

**Ochrona danych w użyciu**: 
- Użyj maszyny wirtualnej z serii DCsv2 (VM) na platformie Azure z włączoną obsługą technologii Intel SGX. Te maszyny wirtualne są włączone przy użyciu zaufanych środowisk wykonywania (TEEs), które zabezpieczają i izolują fragmenty danych i kodu aplikacji.
- Użyj zestawu SDK enklawy aware, aby utworzyć enklawy wewnątrz maszyny wirtualnej. W ramach enklawy dane nie będą widoczne dla żadnej z nich, nawet dostawcy maszyny wirtualnej. Dane w enklawy będą szyfrowane przez obsługę sprzętu.
    - Na przykład można użyć [zestawu SDK programu OE](https://github.com/openenclave/openenclave) do przetwarzania po stronie serwera. 

**Ochrona danych podczas przesyłania** 
- Używanie zaświadczonego protokołu TLS jako bezpiecznego kanału w celu zagwarantowania bezpieczeństwa danych podczas przesyłania
- Klient zapewnia, że dane są wysyłane tylko na ten sam serwer, który jest chroniony przez enklawy. 

**Ochrona danych magazynowanych**
- Używanie chronionych i zabezpieczonych magazynów danych w celu zapewnienia bezpieczeństwa danych w czasie spoczynku 

### <a name="anti-money-laundering"></a>Pranie pieniędzy antypieniężnych
W tym przykładowym zabezpieczeniu wieloskładnikowym wiele banków dzieli dane ze sobą bez ujawniania danych osobowych klientów. Uzgodnienie w bankach są uruchamiane — po analizie połączonego poufnego zestawu danych. Analiza zagregowanego zestawu danych może wykryć przenoszenie pieniędzy przez jednego użytkownika między wieloma bankami, bez konieczności uzyskiwania dostępu do danych przez banki.

Dzięki wykorzystaniu poufnego rozwiązania te instytucje finansowe mogą zwiększyć stawki za wykrywanie oszustw, rozwiązać pieniądze, ograniczyć liczbę fałszywych pozytywnych i kontynuować uczenie się z większych zestawów danych. 

### <a name="drug-development-in-healthcare"></a>Rozwój narkotyków w dziedzinie opieki zdrowotnej
Współpracujące zakłady kondycji współpracują z prywatnymi zestawami danych o kondycji, aby szkolić model ML. Każdy obiekt może zobaczyć tylko własny zestaw danych. Nie ma żadnej innej funkcji, a nawet dostawcy chmury, może zobaczyć model danych lub szkoleń. 

![Analiza kondycji pacjenta](./media/use-cases-scenarios/patient-data.png)

Wszystkie funkcje korzystają z używania przeszkolonego modelu. Tworząc model o większej ilości danych, model staje się bardziej precyzyjny. Każda z tych funkcji, które przyczyniają się do szkolenia modelu, może go używać i otrzymywać przydatne wyniki. 

## <a name="blockchain"></a>Łańcuch bloków

Sieć łańcucha bloków to zdecentralizowana sieć węzłów. Te węzły są uruchamiane i obsługiwane przez operatorów lub walidatorów, którzy chcą zapewnić integralność i osiągnąć konsensus w stosunku do stanu sieci. Same węzły są replikami ksiąg i są używane do śledzenia transakcji łańcucha bloków. Każdy węzeł ma pełną kopię historii transakcji, zapewniając integralność i dostępność w sieci rozproszonej.

Technologie łańcucha bloków stworzone na podstawie poufnego przetwarzania mogą korzystać z prywatności sprzętowej, aby zapewnić poufność danych i bezpieczne obliczenia. W niektórych przypadkach cały finanse są szyfrowane w celu zabezpieczenia dostępu do danych. Czasami sama transakcja może wystąpić w module obliczeniowym wewnątrz enklawy w węźle.

### <a name="confidential-consortium-framework-ccf"></a>Struktura konsorcjum poufnego (CCF)
[CCF](https://www.microsoft.com/research/project/confidential-consortium-framework/) to jeden przykład rozproszonej platformy łańcucha bloków utworzonej w oparciu o dane poufne platformy Azure. Spearheaded przez Microsoft Research to środowisko wykorzystuje możliwości zaufanych środowisk wykonywania (TEEs) w celu utworzenia sieci zdalnej enclaves na potrzeby zaświadczania. Węzły można uruchamiać na maszynach wirtualnych platformy Azure ([DCsv2 z serii](confidential-computing-enclaves.md)) i korzystać z infrastruktury enklawy. Za pomocą protokołów zaświadczania użytkownicy łańcucha bloków mogą weryfikować integralność jednego węzła CCF i skutecznie sprawdzać całą sieć. 

![Sieć węzłów](./media/use-cases-scenarios/ccf.png)

W CCF, zdecentralizowane księgi składają się ze rejestrowanych zmian w magazynie klucz-wartość replikowanym między wszystkimi węzłami sieci. Każdy z tych węzłów uruchamia aparat transakcji, który może zostać wyzwolony przez użytkowników łańcucha bloków za pośrednictwem protokołu TLS. W przypadku wyzwolenia punktu końcowego należy zmodyfikować magazyn klucz-wartość. Przed zarejestrowaniem zmiany zaszyfrowanej w zdecentralizowanej księdze musi ona zostać uzgodniona przez określoną liczbę węzłów do osiągnięcia konsensusu. 

## <a name="next-steps"></a>Następne kroki
[Wdróż](quick-create-marketplace.md) maszynę wirtualną z serii DCsv2.


