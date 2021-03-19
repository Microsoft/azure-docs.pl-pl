---
title: Język Bicep dla szablonów Azure Resource Manager
description: Opisuje język Bicep na potrzeby wdrażania infrastruktury na platformie Azure za pomocą szablonów Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 6f706081d04c5337bb3def8088fe97b08c814ea2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594227"
---
# <a name="what-is-bicep-preview"></a>Co to jest Bicep (wersja zapoznawcza)?

Bicep to język służący do deklaratywnego wdrażania zasobów platformy Azure. Możesz użyć Bicep zamiast JSON do tworzenia szablonów Azure Resource Manager (szablony ARM). Bicep upraszcza proces tworzenia, dostarczając zwięzłą składnię, lepszą obsługę kodu i zwiększa bezpieczeństwo. Bicep to język specyficzny dla domeny (DSL), który oznacza, że jest przeznaczony dla określonego scenariusza lub domeny. Nie jest ona przeznaczona do ogólnego języka programowania do pisania aplikacji.

Składnia JSON dla tworzenia szablonu może być pełna i wymaga wyrażenia złożonego. Bicep Ulepsza ten proces bez utraty możliwości szablonu JSON. Jest to przezroczyste streszczenie w formacie JSON dla szablonów ARM. Każdy plik Bicep kompiluje się do standardowego szablonu ARM. Typy zasobów, wersje interfejsu API i właściwości, które są prawidłowe w szablonie ARM, są prawidłowe w pliku Bicep. Bieżąca wersja zawiera pewne [znane ograniczenia](#known-limitations) .

Aby dowiedzieć się więcej na temat Bicep, zobacz następujące wideo.

> [!VIDEO https://mediusprodstatic.studios.ms/asset-cccfdaf2-cdbe-49dd-9c58-91a4fe5ff0fd/OD340_1920x1080_AACAudio_5429.mp4?sv=2018-03-28&sr=b&sig=N3DuBaTrK3nt5TGwIagTbCqjVrzgwiJ9at80MXQJFwg%3D&st=2021-03-02T01%3A22%3A57Z&se=2026-03-02T01%3A27%3A57Z&sp=r&rscd=filename%3DIGFY21Q3-OD340-Learn%2Beverything%2Babout%2Bthe%2Bnext%2Bgeneration%2Bof%2BARM.mp4]

## <a name="get-started"></a>Rozpoczęcie pracy

Aby rozpocząć od Bicep, [Zainstaluj narzędzia](https://github.com/Azure/bicep/blob/main/docs/installing.md).

Po zainstalowaniu narzędzi Wypróbuj [samouczek Bicep](./bicep-tutorial-create-first-bicep.md). Seria samouczków przeprowadzi Cię przez strukturę i możliwości Bicep. Wdrażasz pliki Bicep i konwertujesz szablon ARM na odpowiedni plik Bicep.

Aby wyświetlić równoważne pliki JSON i Bicep obok siebie, zobacz [Bicep plac zabaw](https://aka.ms/bicepdemo).

Jeśli masz istniejący szablon ARM do przekonwertowania na Bicep, zobacz [konwertowanie szablonów ARM między elementami JSON i Bicep](bicep-decompile.md).

## <a name="bicep-improvements"></a>Udoskonalenia Bicep

Bicep oferuje łatwiejszy i bardziej zwięzłą składnię w porównaniu z równoważnym formatem JSON. Nie można używać `[...]` wyrażeń. Zamiast tego należy bezpośrednio wywołać funkcje i uzyskać wartości z parametrów i zmiennych. Każdy wdrożony zasób należy nadać nazwie symbolicznej, co ułatwia odwoływanie się do tego zasobu w szablonie.

Na przykład poniższy kod JSON zwraca wartość wyjściową z właściwości zasobu.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

Równoważne wyrażenie wyjściowe w Bicep jest łatwiejsze do zapisu. Poniższy przykład zwraca tę samą właściwość przy użyciu nazwy symbolicznej **przywołującym element publicip** dla zasobu, który jest zdefiniowany w ramach szablonu:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Aby zapoznać się z pełnym porównaniem składni, zobacz [porównanie JSON i Bicep dla szablonów](compare-template-syntax.md).

Bicep automatycznie zarządza zależnościami między zasobami. Można uniknąć ustawienia `dependsOn` , gdy Nazwa symboliczna zasobu jest używana w innej deklaracji zasobu.

Za pomocą Bicep można przerwać projekt w wielu modułach.

Struktura pliku Bicep jest bardziej elastyczna niż szablon JSON. Można zadeklarować parametry, zmienne i dane wyjściowe w dowolnym miejscu w pliku. W formacie JSON należy zadeklarować wszystkie parametry, zmienne i dane wyjściowe w odpowiednich sekcjach szablonu.

VS Code rozszerzenie Bicep oferuje rozbudowaną weryfikację i funkcję IntelliSense. Na przykład można użyć funkcji IntelliSense rozszerzenia do pobierania właściwości zasobu.

## <a name="known-limitations"></a>Znane ograniczenia

Obecnie istnieją następujące limity:

* Nie można ustawić rozmiaru trybu lub partii w pętlach kopiowania.
* Nie można połączyć pętli i warunków.
* Jednowierszowe obiekty i tablice, takie jak `['a', 'b', 'c']` , nie są obsługiwane.

## <a name="faq"></a>Często zadawane pytania

**Dlaczego warto utworzyć nowy język zamiast korzystać z istniejącego?**

Możesz traktować Bicep jako poprawkę do istniejącego języka szablonów ARM, a nie nowego języka. Składnia została zmieniona, ale podstawowe funkcje i środowisko uruchomieniowe pozostają takie same.

Przed rozpoczęciem opracowywania Bicep uważamy, że korzystasz z istniejącego języka programowania. Mamy zdecydowaną, że docelowi odbiorcy będą łatwiej poznać Bicep zamiast zacząć pracę z innym językiem.

**Dlaczego nie należy skoncentrować się na Terraform lub innej infrastrukturze innej firmy jako oferty kodu?**

Różni użytkownicy preferują różne języki i narzędzia konfiguracyjne. Chcemy upewnić się, że wszystkie te narzędzia zapewniają doskonałe środowisko na platformie Azure. Bicep jest częścią tego nakładu pracy.

Jeśli jesteś zadowolony z używania Terraform, nie ma powodów do przełączenia. Firma Microsoft dokłada starań, aby upewnić się, że Terraform na platformie Azure są najlepszym rozwiązaniem.

W przypadku klientów, którzy mają wybrane szablony ARM, firma Microsoft uważa, że Bicep ulepsza proces tworzenia. Bicep również pomaga w przejściu klientów, którzy nie przyjęli infrastruktury jako kodu.

**Czy jest Bicep tylko na platformie Azure?**

Bicep to DSL ukierunkowana na wdrażanie kompletnych rozwiązań na platformie Azure. Spełnienie tego celu wymaga pracy z niektórymi interfejsami API, które znajdują się poza platformą Azure. Oczekujemy, że dla tych scenariuszy są używane punkty rozszerzalności.

**Co się stanie z moimi istniejącymi szablonami ARM?**

Nadal działają tak samo, jak zawsze. Nie musisz wprowadzać żadnych zmian. Będziemy nadal obsługiwać podstawowy język JSON szablonu ARM. Pliki Bicep kompilują do formatu JSON, a ten kod JSON jest wysyłany do platformy Azure w celu wdrożenia.

Gdy wszystko będzie gotowe, można [przekonwertować pliki JSON na Bicep](bicep-decompile.md).

## <a name="next-steps"></a>Następne kroki

Zacznij korzystać z [samouczka Bicep](./bicep-tutorial-create-first-bicep.md).
