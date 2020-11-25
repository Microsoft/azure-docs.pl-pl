---
title: Optymalizuj Azure CDN dla typu dostarczania zawartości
description: Dowiedz się, jak usługa Azure Content Delivery Network może zoptymalizować dostarczanie na podstawie typu zawartości. Najlepsze rozwiązania w zakresie optymalizacji zwiększają wydajność i odciążanie pochodzenia.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 7c7e4ff05307452da67c37b23bf492db8855e2ef
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004504"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Optymalizuj Azure CDN dla typu dostarczania zawartości

W przypadku dostarczania zawartości do dużych odbiorców globalnych niezwykle ważne jest zapewnienie zoptymalizowanego dostarczania zawartości. [Usługa Azure Content Delivery Network (CDN)](cdn-overview.md) może zoptymalizować środowisko dostarczania w oparciu o typ posiadanej zawartości. Zawartość może być witryną internetową, strumieniem na żywo, klipem wideo lub dużym plikiem do pobrania. Podczas tworzenia punktu końcowego usługi CDN należy określić scenariusz w opcji **zoptymalizowane dla** . Wybór określa, która Optymalizacja jest stosowana do zawartości dostarczonej z punktu końcowego usługi CDN.

Opcje optymalizacji są przeznaczone do używania zachowań najlepszych rozwiązań w celu zwiększenia wydajności dostarczania zawartości i lepszego odciążania pochodzenia. Wybór scenariusza ma wpływ na wydajność przez modyfikację konfiguracji dla buforowania częściowego, fragmentacji obiektów i zasad ponawiania nieudanych prób źródła. 

Ten artykuł zawiera omówienie różnych funkcji optymalizacji i korzystania z nich. Aby uzyskać więcej informacji o funkcjach i ograniczeniach, zapoznaj się z odpowiednimi artykułami dotyczącymi poszczególnych typów optymalizacji.

> [!NOTE]
> Podczas tworzenia punktu końcowego usługi CDN **zoptymalizowane pod kątem** opcji może się różnić w zależności od typu profilu, w którym jest tworzony punkt końcowy. Dostawcy Azure CDN stosują usprawnienia na różne sposoby, w zależności od scenariusza. 

## <a name="provider-options"></a>Opcje dostawcy

**Standard Azure CDN z profilów Microsoft** obsługuje następujące optymalizacje:

* [Ogólne dostarczanie w sieci Web](#general-web-delivery). Ta optymalizacja służy również do przesyłania strumieniowego multimediów i dużego pobierania plików.

> [!NOTE]
> Przyspieszanie witryn dynamicznych od firmy Microsoft jest oferowane przez [usługę Azure front-drzwi](../frontdoor/front-door-overview.md).

**Azure CDN Standard from Verizon** i **Azure CDN Premium z profilów Verizon** obsługują następujące optymalizacje:

* [Ogólne dostarczanie w sieci Web](#general-web-delivery). Ta optymalizacja służy również do przesyłania strumieniowego multimediów i dużego pobierania plików.

* [Przyspieszanie witryn dynamicznych](#dynamic-site-acceleration) 


**Standard Azure CDN z profilów Akamai** obsługują następujące optymalizacje:

* [Ogólne dostarczanie w sieci Web](#general-web-delivery) 

* [Ogólne przesyłanie strumieniowe multimediów](#general-media-streaming)

* [Przesyłanie strumieniowe multimediów wideo na żądanie](#video-on-demand-media-streaming)

* [Pobieranie dużych plików](#large-file-download)

* [Przyspieszanie witryn dynamicznych](#dynamic-site-acceleration) 

Firma Microsoft zaleca przetestowanie różnic wydajności między różnymi dostawcami w celu wybrania optymalnego dostawcy dla dostawy.

## <a name="select-and-configure-optimization-types"></a>Wybierz i skonfiguruj typy optymalizacji

Podczas tworzenia punktu końcowego usługi CDN wybierz typ optymalizacji, który najlepiej pasuje do scenariusza i typu zawartości, która ma być dostarczany przez punkt końcowy. Domyślnym ustawieniem jest **dostarczanie w sieci Web** . W przypadku istniejącego **standardu Azure CDN tylko z** punktów końcowych Akamai można w dowolnym momencie zaktualizować opcję optymalizacji. Ta zmiana nie przerywa dostarczania z Azure CDN. 

1. W **standardzie Azure CDN z profilu Akamai** wybierz punkt końcowy.

    ![Wybór punktu końcowego](./media/cdn-optimization-overview/01_Akamai.png)

2. W obszarze Ustawienia wybierz pozycję **Optymalizacja**. Następnie wybierz typ z listy rozwijanej **zoptymalizowane dla** .

    ![Optymalizacja i wybór typu](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optymalizacja dla konkretnych scenariuszy

Punkt końcowy usługi CDN można zoptymalizować dla jednego z tych scenariuszy. 

### <a name="general-web-delivery"></a>Ogólne dostarczanie w sieci Web

Ogólne dostarczanie w sieci Web to najbardziej typowa opcja optymalizacji. Jest ona przeznaczona do ogólnej optymalizacji zawartości sieci Web, takiej jak Webpages i Web Applications. Ta optymalizacja może być również używana do pobierania plików i wideo.

Typowa witryna sieci Web zawiera zawartość statyczną i dynamiczną. Zawartość statyczna zawiera obrazy, biblioteki JavaScript i arkusze stylów, które mogą być buforowane i dostarczane do różnych użytkowników. Zawartość dynamiczna jest spersonalizowana dla pojedynczego użytkownika, na przykład elementów wiadomości, które są dostosowane do profilu użytkownika. Zawartość dynamiczna, na przykład zawartość koszyka, nie jest buforowana, ponieważ jest unikatowa dla każdego użytkownika. Ogólne dostarczanie w sieci Web może zoptymalizować całą witrynę sieci Web. 

> [!NOTE]
> Jeśli używasz **standardu Azure CDN z profilu Akamai** , wybierz ten typ optymalizacji, jeśli średni rozmiar pliku jest mniejszy niż 10 MB. W przeciwnym razie, jeśli średni rozmiar pliku jest większy niż 10 MB, wybierz opcję **pobieranie dużych plików** z listy rozwijanej **zoptymalizowane dla** .

### <a name="general-media-streaming"></a>Ogólne przesyłanie strumieniowe multimediów

Jeśli musisz użyć punktu końcowego do przesyłania strumieniowego na żywo i przesyłania strumieniowego wideo na żądanie, wybierz ogólny typ optymalizacji przesyłania strumieniowego multimediów.

Przesyłanie strumieniowe multimediów jest zależne od czasu, ponieważ pakiety, które docierają do klienta, na przykład częste buforowanie zawartości wideo, mogą spowodować spadek wydajności. Optymalizacja przesyłania strumieniowego multimediów zmniejsza opóźnienia dostarczania zawartości multimedialnej i zapewnia bezproblemowe środowisko przesyłania strumieniowego dla użytkowników. 

Ten scenariusz jest typowy dla klientów usługi Azure Media Service. Gdy korzystasz z usługi Azure Media Services, uzyskasz pojedynczy punkt końcowy przesyłania strumieniowego, który może być używany zarówno w przypadku przesyłania strumieniowego na żywo, jak i na żądanie. W tym scenariuszu klienci nie muszą przełączać się do innego punktu końcowego, gdy zmieniają się z żywo na przesyłanie strumieniowe na żądanie. Ogólna Optymalizacja przesyłania strumieniowego multimediów obsługuje ten typ scenariusza.

W przypadku **standardu Azure CDN firmy Microsoft**, **Azure CDN Standard from Verizon** i **Azure CDN Premium z Verizon**, użyj ogólnego typu optymalizacji dostarczania w sieci Web, aby dostarczyć ogólną zawartość multimediów strumieniowych.

Aby uzyskać więcej informacji na temat optymalizacji przesyłania strumieniowego multimediów, zobacz [Optymalizacja przesyłania strumieniowego multimediów](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Przesyłanie strumieniowe multimediów wideo na żądanie

Optymalizacja przesyłania strumieniowego multimediów wideo na żądanie usprawnia przesyłanie strumieniowe zawartości wideo na żądanie. Jeśli używasz punktu końcowego do przesyłania strumieniowego wideo na żądanie, Użyj tej opcji.

W przypadku **Azure CDN standard firmy Microsoft**, **Azure CDN Standard from Verizon** i **Azure CDN Premium z profilów Verizon** , użyj ogólnego typu optymalizacji dostarczania w sieci Web, aby dostarczać zawartość multimediów przesyłanych strumieniowo na żądanie wideo.

Aby uzyskać więcej informacji na temat optymalizacji przesyłania strumieniowego multimediów, zobacz [Optymalizacja przesyłania strumieniowego multimediów](cdn-media-streaming-optimization.md).

> [!NOTE]
> Jeśli punkt końcowy usługi CDN obsługuje głównie zawartość wideo na żądanie, użyj tego typu optymalizacji. Główną różnicą między tym typem optymalizacji a ogólnym typem optymalizacji przesyłania strumieniowego multimediów jest limit czasu połączenia. Limit czasu jest znacznie krótszy do pracy ze scenariuszami przesyłania strumieniowego na żywo.
>

### <a name="large-file-download"></a>Pobieranie dużych plików

W przypadku **Azure CDN standardowych z profilów Akamai** pliki o dużym rozmiarze są zoptymalizowane pod kątem zawartości większej niż 10 MB. Jeśli średni rozmiar pliku jest mniejszy niż 10 MB, użyj ogólnego dostarczania w sieci Web. Jeśli średnie rozmiary plików są stale większe niż 10 MB, może być bardziej wydajne, aby utworzyć oddzielny punkt końcowy dla dużych plików. Na przykład oprogramowanie układowe lub aktualizacje oprogramowania zwykle są dużymi plikami. Aby dostarczać pliki o rozmiarze większym niż 1,8 GB, wymagana jest optymalizacja pobierania plików o dużym rozmiarze.

W przypadku **standardu Azure CDN firmy Microsoft**, **Azure CDN Standard from Verizon** i **Azure CDN Premium z profilów Verizon** , użyj ogólnego typu optymalizacji dostarczania w sieci Web, aby dostarczyć zawartość pobierania dużych plików. Nie ma ograniczenia dotyczącego rozmiaru pobierania plików.

Aby uzyskać więcej informacji na temat optymalizacji dużych plików, zobacz [Optymalizacja dużych plików](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Przyspieszanie witryn dynamicznych

 Przyspieszanie witryn dynamicznych (DSA) jest dostępne dla **Azure CDN Standard od Akamai**, **Azure CDN Standard z Verizon** i **Azure CDN Premium z profilów Verizon** . Ta optymalizacja obejmuje dodatkową opłatę za korzystanie z programu; Aby uzyskać więcej informacji, zobacz [Cennik usługi Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

> [!NOTE]
> Przyspieszanie witryn dynamicznych od firmy Microsoft jest oferowane przez [usługę Azure Predrzwiczki](../frontdoor/front-door-overview.md) , która jest globalną usługą [emisji](https://en.wikipedia.org/wiki/Anycast) pojedynczej korzystającej z prywatnej sieci globalnej firmy Microsoft do dostarczania obciążeń aplikacji.

Agent DSA zawiera różne techniki, które korzystają z opóźnienia i wydajności zawartości dynamicznej. Techniki obejmują optymalizację tras i sieci, optymalizację protokołu TCP i wiele więcej. 

Ta optymalizacja umożliwia przyspieszenie aplikacji sieci Web, która zawiera wiele odpowiedzi, które nie są buforowane. Przykładami są wyniki wyszukiwania, transakcje wyewidencjonowania lub dane w czasie rzeczywistym. Można nadal używać podstawowych możliwości buforowania Azure CDN w przypadku danych statycznych. 

Aby uzyskać więcej informacji na temat przyspieszania witryn dynamicznych, zobacz [przyspieszanie witryn dynamicznych](cdn-dynamic-site-acceleration.md).