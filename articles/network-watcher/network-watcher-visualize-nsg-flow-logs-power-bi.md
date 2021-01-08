---
title: Wizualizacja dzienników usługi Azure sieciowej grupy zabezpieczeń Flow — Power BI
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak za pomocą Power BI wizualizować dzienniki przepływu sieciowych grup zabezpieczeń, aby umożliwić wyświetlanie informacji o ruchu IP w usłudze Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 50f08ffc304962cc66f6eed07adcb5b7e4aa6692
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011156"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Wizualizacja dzienników przepływu sieciowych grup zabezpieczeń przy użyciu Power BI

Dzienniki przepływu sieciowych grup zabezpieczeń umożliwiają wyświetlanie informacji dotyczących ruchu przychodzącego i wychodzącego IP w sieciowych grupach zabezpieczeń. Te dzienniki przepływu przedstawiają przepływy wychodzące i przychodzące dla każdej reguły, karta sieciowa przepływu ma zastosowanie do, 5-informacje o spójnej kolekcji przepływu (źródłowy/docelowy adres IP, port źródłowy/docelowy, protokół) i jeśli ruch był dozwolony lub zabroniony.

Uzyskanie wglądu w dane rejestrowania przepływu może być trudne, ręczne przeszukiwanie plików dziennika. W tym artykule udostępniamy rozwiązanie do wizualizacji najnowszych dzienników przepływów i informacje o ruchu w sieci.

> [!Warning]  
> Poniższe kroki współpracują z dziennikami przepływów w wersji 1. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do rejestrowania przepływów dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md). Poniższe instrukcje nie będą działały z wersją 2 plików dziennika bez modyfikacji.

## <a name="scenario"></a>Scenariusz

W poniższym scenariuszu firma Microsoft łączy Power BI Desktop z kontem magazynu skonfigurowanym jako ujścia dla danych rejestrowania przepływu sieciowej grupy zabezpieczeń. Po nawiązaniu połączenia z naszym kontem magazynu Power BI pobiera i analizuje dzienniki, aby zapewnić wizualną reprezentację ruchu rejestrowanego przez sieciowe grupy zabezpieczeń.

Korzystając z wizualizacji dostarczonych w szablonie, można przeanalizować:

* Najpopularniejsze rozmowy
* Dane przepływu szeregów czasowych zgodnie z kierunkiem i decyzją zasady
* Przepływy według adresu MAC interfejsu sieciowego
* Przepływy według sieciowej grupy zabezpieczeń i reguły
* Przepływy według portu docelowego

Udostępniony szablon jest edytowalny, więc można go zmodyfikować, aby dodać nowe dane, wizualizacje lub edytować zapytania zgodnie z potrzebami.

## <a name="setup"></a>Konfigurowanie

Przed rozpoczęciem należy włączyć rejestrowanie przepływu sieciowych grup zabezpieczeń dla jednej lub wielu sieciowych grup zabezpieczeń na Twoim koncie. Instrukcje dotyczące włączania dzienników przepływu zabezpieczeń sieci można znaleźć w następującym artykule: [wprowadzenie do rejestrowania przepływu dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md).

Na maszynie należy również zainstalować klienta Power BI Desktop oraz wystarczającą ilość wolnego miejsca na komputerze w celu pobrania i załadowania danych dziennika istniejących na koncie magazynu.

![Diagram programu Visio][1]

### <a name="steps"></a>Kroki

1. Pobierz i Otwórz następujący szablon Power BI w Network Watcher aplikacji Power BI Desktop [szablon dzienników przepływów usługi PowerBI](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Wprowadź wymagane parametry zapytania
   1. **StorageAccountName** — określa nazwę konta magazynu zawierającego dzienniki przepływów sieciowej grupy zabezpieczeń, które chcesz załadować i wizualizować.
   1. **NumberOfLogFiles** — określa liczbę plików dziennika, które chcesz pobrać i wizualizować w Power BI. Na przykład jeśli określono 50, najnowsze pliki dziennika 50. Jeśli mamy 2 sieciowych grup zabezpieczeń i skonfigurowano wysyłanie dzienników przepływów sieciowej grupy zabezpieczeń do tego konta, będzie można wyświetlić ostatnich 25 godzin dzienników.

      ![Główne usługi Power BI][2]

1. Wprowadź klucz dostępu dla konta magazynu. Prawidłowe klucze dostępu można znaleźć, przechodząc do konta magazynu w Azure Portal i wybierając pozycję **klucze dostępu** w menu Ustawienia. Kliknij przycisk **Połącz** , a następnie Zastosuj zmiany.

    ![klucze dostępu][3]

    ![dostęp do klucza 2][4]

4. Dzienniki są pobierane i przeanalizowane i można teraz korzystać z wstępnie utworzonych wizualizacji.

## <a name="understanding-the-visuals"></a>Informacje o wizualizacjach

Podane w szablonie są zestawem wizualizacji, które pomagają zrozumieć dane dziennika przepływu sieciowej grupy zabezpieczeń. Na poniższych ilustracjach przedstawiono przykład wyświetlania pulpitu nawigacyjnego po zapełnieniu danymi. Poniżej sprawdzimy szczegółowo każdą wizualizację 

![powerbi][5]
 
Wizualizacja najlepszych rozmów pokazuje adresy IP, w przypadku których zainicjowano większość połączeń w określonym okresie. Rozmiar pól odpowiada względnej liczbie połączeń. 

![toptalkers][6]

Poniższe wykresy szeregów czasowych przedstawiają liczbę przepływów w danym okresie. Górny wykres jest segmentem przetworzonym przez kierunek przepływu, a dolna jest segmentem poddanym decyzji (Zezwalaj lub Odmów). Korzystając z tej wizualizacji, można ocenić trendy ruchu w czasie i zajrzeć się wszelkim nietypowym wzrostom lub odrzucaniu ruchu lub segmentacji ruchu.

![flowsoverperiod][7]

Poniższe wykresy przedstawiają przepływy na interfejs sieciowy, z górnym segmentacją według kierunku przepływu i niższym segmentem podnoszonym przez podejmowane decyzje. Dzięki tym informacjom możesz uzyskać szczegółowe informacje o tym, do których maszyn wirtualnych komunikują się najbardziej względnie z innymi, i jeśli ruch do określonej maszyny wirtualnej jest dozwolony lub odrzucany.

![flowspernic][8]

Poniższy wykres kołowy pierścieniowego przedstawia podział przepływów według portu docelowego. Korzystając z tych informacji, można wyświetlić najczęściej używane porty docelowe używane w określonym przedziale czasu.

![pierścień][9]

Poniższy wykres słupkowy przedstawia przepływ według sieciowej grupy zabezpieczeń i reguły. Korzystając z tych informacji, można zobaczyć sieciowych grup zabezpieczeń odpowiedzialny za większość ruchu i rozkład ruchu na sieciowej grupy zabezpieczeń przez regułę.

![barchart][10]
 
Poniższe wykresy informacyjne zawierają informacje dotyczące sieciowych grup zabezpieczeń obecne w dziennikach, liczbę przepływów przechwytywanych w danym okresie oraz datę najwcześniejszego przechwycenia dziennika. Te informacje stanowią pomysł dotyczący tego, co sieciowych grup zabezpieczeń są rejestrowane, i zakres dat przepływów.

![infochart1][11]

![infochart2][12]

Ten szablon zawiera następujące fragmentatory umożliwiające wyświetlenie tylko interesujących Cię danych. Można filtrować według grup zasobów, sieciowych grup zabezpieczeń i reguł. Można również filtrować według 5-informacje o spójnej kolekcji, decyzję i godzinę zapisania dziennika.

![Fragmentatory][13]

## <a name="conclusion"></a>Podsumowanie

W tym scenariuszu pokazano, jak za pomocą dzienników przepływu sieciowych grup zabezpieczeń zapewnianych przez Network Watcher i Power BI można wizualizować i zrozumieć ruch. Za pomocą podanego szablonu Power BI pobiera dzienniki bezpośrednio z magazynu i przetwarza je lokalnie. Czas potrzebny na załadowanie szablonu różni się w zależności od liczby żądanych plików i łącznego rozmiaru pobranych plików.

Możesz dostosowywać ten szablon do swoich potrzeb. Istnieje wiele różnych sposobów używania Power BI z dziennikami przepływu sieciowych grup zabezpieczeń. 

## <a name="notes"></a>Uwagi

* Dzienniki są domyślnie przechowywane w `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Jeśli inne dane znajdują się w innym katalogu, muszą być modyfikowane przez zapytania ściągające i przetwarzające dane.

* Nie zaleca się używania podanego szablonu z więcej niż 1 GB dzienników.

* Jeśli masz dużą ilość dzienników, zalecamy zbadanie rozwiązania przy użyciu innego magazynu danych, takiego jak Data Lake lub program SQL Server.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wizualizować dzienniki przepływu usługi sieciowej grupy zabezpieczeń za pomocą elastycznego stosu, odwiedzając [wizualizacje w usłudze Azure Network Watcher sieciowej grupy zabezpieczeńing Logs przy użyciu narzędzi open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png
