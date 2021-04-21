---
title: Język bicep dla szablonów Azure Resource Manager bicep
description: Opisuje język Bicep do wdrażania infrastruktury na platformie Azure za pomocą Azure Resource Manager szablonów.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: af207e6ca88eab50fe6030883379c87c0ec05691
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773751"
---
# <a name="what-is-bicep-preview"></a>Co to jest bicep (wersja zapoznawcza)?

Bicep to język do deklaratywnego wdrażania zasobów platformy Azure. Do tworzenia szablonów aplikacji (szablonów arm) można używać Azure Resource Manager Bicep zamiast JSON. Bicep upraszcza tworzenie, zapewniając zwięzłą składnię, lepszą obsługę ponownego użycia kodu i ulepszone bezpieczeństwo typów. Bicep to język specyficzny dla domeny (DSL), co oznacza, że jest przeznaczony dla określonego scenariusza lub domeny. Nie jest on przeznaczony jako ogólny język programowania do pisania aplikacji.

Składnia JSON do tworzenia szablonu może być szczegółowa i wymagać skomplikowanego wyrażenia. Bicep poprawia to środowisko bez utraty żadnych możliwości szablonu JSON. Jest to przezroczysta abstrakcja dla danych JSON dla szablonów arm. Każdy plik Bicep jest kompilowany do standardowego szablonu usługi ARM. Typy zasobów, wersje interfejsu API i właściwości, które są prawidłowe w szablonie usługi ARM, są prawidłowe w pliku Bicep. W bieżącej [wersji istnieje](#known-limitations) kilka znanych ograniczeń.

Bicep jest obecnie w wersji zapoznawczej. Aby śledzić stan pracy, zobacz repozytorium [projektu Bicep](https://github.com/Azure/bicep).

Aby dowiedzieć się więcej na temat bicepu, zobacz poniższy film wideo.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>Rozpoczęcie pracy

Aby rozpocząć od narzędzia Bicep, [zainstaluj narzędzia](bicep-install.md).

Po zainstalowaniu narzędzi wypróbuj samouczek [bicep](./bicep-tutorial-create-first-bicep.md). W tej serii samouczków osłaniamy strukturę i możliwości aplikacji Bicep. Wdrażasz pliki Bicep i konwertujemy szablon usługi ARM na równoważny plik Bicep.

Aby wyświetlić obok siebie równoważne pliki JSON i Bicep, zobacz Temat [Bicep Playground](https://aka.ms/bicepdemo).

Jeśli masz istniejący szablon usługi ARM, który chcesz przekonwertować na bicep, zobacz Konwertowanie szablonów [arm między JSON i Bicep.](bicep-decompile.md)

## <a name="benefits-of-bicep-versus-other-tools"></a>Zalety bicep w porównaniu z innymi narzędziami

Bicep zapewnia następujące korzyści w związku z innymi opcjami:

* **Obsługa wszystkich typów zasobów i wersji interfejsu API:** platforma Bicep natychmiast obsługuje wszystkie wersje zapoznawcze i wersji gałęd dla usług platformy Azure. Gdy tylko dostawca zasobów wprowadzi nowe typy zasobów i wersje interfejsu API, możesz ich użyć w pliku Bicep. Nie musisz czekać na zaktualizowanie narzędzi przed rozpoczęciem korzystania z nowych usług.
* **Środowisko tworzenia:** gdy używasz VS Code do tworzenia plików Bicep, masz najwyższej klasy środowisko tworzenia. Edytor zapewnia bogaty poziom bezpieczeństwa typów, funkcji IntelliSense i weryfikacji składni.
* **Modułowość:** kod Bicep można rozbić na części, które można zarządzać, przy użyciu [modułów](bicep-modules.md). Moduł wdraża zestaw powiązanych zasobów. Moduły umożliwiają ponowne użycie kodu i uproszczenie opracowywania. Dodaj moduł do pliku Bicep w dowolnym momencie, gdy będzie konieczne wdrożenie tych zasobów.
* **Integracja z usługami platformy Azure:** platforma Bicep jest zintegrowana z usługami platformy Azure, takimi jak Azure Policy, specyfikacje szablonów i strategie.
* **Brak plików stanu lub stanu do zarządzania:** cały stan jest przechowywany na platformie Azure. Użytkownicy mogą współpracować i mieć pewność, że ich aktualizacje są obsługiwane zgodnie z oczekiwaniami. Użyj operacji [what-if,](template-deploy-what-if.md) aby wyświetlić podgląd zmian przed wdrożeniem szablonu.
* **Brak kosztów i open source:** bicep jest całkowicie bezpłatny. Nie musisz płacić za funkcje premium. Jest ona również obsługiwana przez pomoc techniczną firmy Microsoft.

## <a name="bicep-improvements"></a>Ulepszenia bicep

Bicep oferuje prostszą i bardziej zwięzłą składnię w porównaniu z równoważnym JSON. Nie używasz `[...]` wyrażeń. Zamiast tego można bezpośrednio wywołać funkcje i pobrać wartości z parametrów i zmiennych. Każdy wdrożony zasób ma nazwę symboliczną, co ułatwia odwołanie się do tego zasobu w szablonie.

Na przykład poniższy kod JSON zwraca wartość wyjściową z właściwości zasobu.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

Równoważne wyrażenie wyjściowe w bicepie jest łatwiejsze do napisania. Poniższy przykład zwraca tę samą właściwość przy użyciu symbolicznej nazwy **publicIP** dla zasobu zdefiniowanego w szablonie:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Aby uzyskać pełne porównanie składni, zobacz Porównanie danych [JSON](compare-template-syntax.md)i Bicep dla szablonów .

Bicep automatycznie zarządza zależnościami między zasobami. Można uniknąć ustawienia, gdy nazwa symboliczna zasobu jest używana `dependsOn` w innej deklaracji zasobu.

Struktura pliku Bicep jest bardziej elastyczna niż szablon JSON. Parametry, zmienne i dane wyjściowe można zadeklarować w dowolnym miejscu pliku. W pliku JSON należy zadeklarować wszystkie parametry, zmienne i dane wyjściowe w odpowiednich sekcjach szablonu.

## <a name="known-limitations"></a>Znane ograniczenia

Obecnie istnieją następujące limity:

* Nie można ustawić trybu ani rozmiaru partii w pętlach kopiowania.
* Nie można łączyć pętli i warunków.
* Obiekt jedno wierszowy i tablice, takie jak `['a', 'b', 'c']` , nie są obsługiwane.

## <a name="faq"></a>Często zadawane pytania

**Dlaczego warto utworzyć nowy język zamiast korzystać z istniejącego?**

O bicepie można myśleć jako o wersji istniejącego języka szablonu usługi ARM, a nie o nowym języku. Składnia uległa zmianie, ale podstawowe funkcje i środowisko uruchomieniowe pozostają takie same.

Przed opracowaniem aplikacji Bicep rozważyliśmy użycie istniejącego języka programowania. Postanowiliśmy, że odbiorcy docelowi będą łatwiej uczyć się bicepu, zamiast rozpoczynać pracę z innym językiem.

**Dlaczego nie skoncentrować się na programie Terraform lub ofercie infrastruktury jako kodu innej firmy?**

Różni użytkownicy preferują różne języki konfiguracji i narzędzia. Chcemy mieć pewność, że wszystkie te narzędzia zapewniają doskonałe środowisko na platformie Azure. Bicep jest częścią tego wysiłku.

Jeśli korzystanie z narzędzia Terraform jest dla Ciebie zadowolenie, nie ma powodu, aby się przełączać. Firma Microsoft dokłada starań, aby upewnić się, że narzędzie Terraform na platformie Azure jest najlepszym rozwiązaniem.

W przypadku klientów, którzy wybrali szablony usługi ARM, uważamy, że bicep usprawnia tworzenie. Bicep pomaga również w przejściu dla klientów, którzy nie uchwalili infrastruktury jako kodu.

**Czy bicep jest tylko dla platformy Azure?**

Bicep to DSL ukierunkowany na wdrażanie kompletnych rozwiązań na platformie Azure. Spełnianie tego celu wymaga pracy z niektórymi interfejsami API spoza platformy Azure. Oczekujemy zapewnienia punktów rozszerzalności dla tych scenariuszy.

**Co się stanie z istniejącymi szablonami arm?**

Nadal działają dokładnie tak, jak zawsze. Nie musisz wprowadzać żadnych zmian. Będziemy nadal obsługiwać podstawowy język JSON szablonu USŁUGI ARM. Pliki Bicep są kompilowane do pliku JSON, a ten kod JSON jest wysyłany na platformę Azure w celu wdrożenia.

Gdy wszystko będzie gotowe, możesz przekonwertować pliki [JSON na plik Bicep](bicep-decompile.md).

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do samouczka [bicep](./bicep-tutorial-create-first-bicep.md).
