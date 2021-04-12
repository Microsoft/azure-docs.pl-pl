---
title: Wizualizowanie wzorców ruchu sieciowego za pomocą narzędzi open source
titleSuffix: Azure Network Watcher
description: Na tej stronie opisano, jak używać funkcji przechwytywania pakietów Network Watcher z Capanalysis do wizualizacji wzorców ruchu do i z maszyn wirtualnych.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 0503f6910186b42bf381c662c7942e37c28bfdf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709516"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Wizualizowanie wzorców ruchu sieciowego do i z maszyn wirtualnych za pomocą narzędzi open source

Przechwytywanie pakietów zawiera dane sieci, które umożliwiają przeprowadzenie szczegółowej inspekcji sieci i dowodowych. Istnieje wiele narzędzi do otwierania źródła, których można użyć do analizowania przechwytywania pakietów w celu uzyskania szczegółowych informacji o sieci. Jednym z tych narzędzi jest CapAnalysis, narzędzie do wizualizacji przechwytywania pakietów typu open source. Wizualizowanie danych przechwytywania pakietów jest cennym sposobem na szybkie uzyskiwanie szczegółowych informacji na temat wzorców i anomalii w sieci. Wizualizacje udostępniają również metody udostępniania takich szczegółowych informacji w łatwy w użyciu sposób.

Network Watcher platformy Azure umożliwia przechwytywanie danych przez umożliwienie przechwycenia pakietów w sieci. Ten artykuł zawiera omówienie sposobu wizualizacji i uzyskiwania szczegółowych informacji z przechwytywania pakietów przy użyciu CapAnalysis z Network Watcher.

## <a name="scenario"></a>Scenariusz

Prosta aplikacja internetowa wdrożona na maszynie wirtualnej na platformie Azure chce używać narzędzi typu "open source" do wizualizacji ruchu sieciowego, aby szybko identyfikować wzorce przepływu i ewentualne anomalie. Za pomocą Network Watcher można uzyskać przechwytywanie pakietów środowiska sieciowego i bezpośrednio je zapisać na koncie magazynu. CapAnalysis może następnie pozyskać przechwycenie pakietu bezpośrednio z obiektu blob magazynu i wizualizować jego zawartość.

![scenariusz][1]

## <a name="steps"></a>Kroki

### <a name="install-capanalysis"></a>Zainstaluj CapAnalysis

Aby zainstalować CapAnalysis na maszynie wirtualnej, możesz zapoznać się z oficjalnymi instrukcjami tutaj https://www.capanalysis.net/ca/how-to-install-capanalysis .
Aby zdalnie uzyskać dostęp do CapAnalysis, musisz otworzyć port 9877 na maszynie wirtualnej przez dodanie nowej reguły zabezpieczeń dla ruchu przychodzącego. Aby uzyskać więcej informacji na temat tworzenia reguł w sieciowych grupach zabezpieczeń, zapoznaj się z tematem [Tworzenie reguł w istniejącej sieciowej grupy zabezpieczeń](../virtual-network/manage-network-security-group.md#create-a-security-rule). Po pomyślnym dodaniu reguły powinno być możliwe uzyskanie dostępu do CapAnalysis z `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Użyj usługi Azure Network Watcher, aby rozpocząć sesję przechwytywania pakietów

Network Watcher umożliwia przechwytywanie pakietów do śledzenia ruchu do i z maszyny wirtualnej. Aby rozpocząć sesję przechwytywania pakietów, można zapoznać się z instrukcjami w obszarze [Zarządzanie przechwytywaniem pakietów przy użyciu Network Watcher](network-watcher-packet-capture-manage-portal.md) . Przechwytywanie pakietów może być przechowywane w magazynie obiektów blob, do którego można uzyskać dostęp za pomocą CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Przekazywanie przechwycenia pakietu do CapAnalysis
Przechwycenie pakietu wykonane przez obserwatora sieci można przekazać bezpośrednio przy użyciu karty "Importuj z adresu URL" i podać link do obiektu blob magazynu, w którym jest przechowywany przechwycenie pakietu.

Po udostępnieniu linku do CapAnalysis upewnij się, że w adresie URL obiektu blob magazynu zostanie dołączony token sygnatury dostępu współdzielonego.  W tym celu przejdź do sygnatury dostępu współdzielonego z konta magazynu, Wyznacz dozwolone uprawnienia i naciśnij przycisk Generuj SAS, aby utworzyć token. Następnie można dołączyć token sygnatury dostępu współdzielonego do adresu URL obiektu blob magazynu przechwytywania pakietów.

Otrzymany adres URL będzie wyglądać podobnie do następującego adresu URL: `http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere`


### <a name="analyzing-packet-captures"></a>Analizowanie przechwytywania pakietów

CapAnalysis oferuje różne opcje wizualizacji przechwytywania pakietów, z których każda dostarcza analizę z innej perspektywy. Dzięki tym podsumowaniem wizualizacji możesz zrozumieć trendy dotyczące ruchu sieciowego i szybko wykasować wszystkie nietypowe działania. Niektóre z tych funkcji przedstawiono na poniższej liście:

1. Tabele przepływu

    Ta tabela zawiera listę przepływów w danych pakietu, sygnaturę czasową skojarzoną z przepływami i różnych protokołów skojarzonych z przepływem, a także źródłowy i docelowy adres IP.

    ![Strona przepływu capanalysis][5]

1. Przegląd protokołu

    To okienko umożliwia szybkie wyświetlenie rozkładu ruchu sieciowego przez różne protokoły i lokalizacje geograficzne.

    ![Przegląd protokołu capanalysis][6]

1. Statystyki

    To okienko umożliwia wyświetlanie statystyk ruchu sieciowego — liczba bajtów wysłanych i odebranych z źródłowych i docelowych adresów IP, przepływy dla każdego źródłowego i docelowego adresu IP, protokół używany do różnych przepływów oraz czas trwania przepływów.

    ![Statystyka capanalysis][7]

1. Geomap

    To okienko zawiera widok mapy ruchu sieciowego, dzięki czemu kolory są skalowane do wielkości ruchu z każdego kraju/regionu. Można wybrać wyróżnione kraje/regiony, aby wyświetlić dodatkowe statystyki przepływu, takie jak część danych wysłanych i odebranych z adresów IP w tym kraju/regionie.

    ![geomap][8]

1. Filtry

    CapAnalysis zawiera zestaw filtrów do szybkiej analizy określonych pakietów. Na przykład możesz wybrać filtrowanie danych według protokołu, aby uzyskać szczegółowe informacje o tym podzbiorze ruchu.

    ![filtry][11]

    Odwiedź witrynę [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) , aby dowiedzieć się więcej o możliwościach wszystkich CapAnalysis.

## <a name="conclusion"></a>Podsumowanie

Funkcja przechwytywania pakietów Network Watcher umożliwia przechwytywanie danych niezbędnych do przeprowadzenia dowodowych sieci i lepszego zrozumienia ruchu sieciowego. W tym scenariuszu pokazano, jak przechwycenia pakietów z Network Watcher można łatwo zintegrować z narzędziami do wizualizacji Open Source. Za pomocą narzędzi typu "open source", takich jak CapAnalysis, aby wizualizować przechwycone pakiety, można przeprowadzać głębokie inspekcje pakietów i szybko identyfikować trendy w ramach ruchu sieciowego.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat dzienników przepływów sieciowej grupy zabezpieczeń, odwiedź [dzienniki przepływów sieciowej grupy zabezpieczeń](network-watcher-nsg-flow-logging-overview.md)

Dowiedz się, jak wizualizować dzienniki przepływu sieciowej grupy zabezpieczeń za pomocą Power BI, odwiedzając [wizualizacje sieciowej grupy zabezpieczeń przepływy dzienników z Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
