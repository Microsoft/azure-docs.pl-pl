---
title: Osadź klienta analizatora internetowego | Microsoft Docs
description: W tym artykule dowiesz się, jak osadzić klienta w języku JavaScript analizatora Internetu w aplikacji.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 0d4b27b85ac7bc61e14a79f29e4e26ec4973ced1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84744055"
---
# <a name="embed-the-internet-analyzer-client"></a>Osadź klienta analizatora internetowego

W tym artykule pokazano, jak osadzić klienta JavaScript w aplikacji. Instalacja tego klienta jest konieczna do uruchamiania testów i otrzymywania analiz kart wyników. **Klient JavaScript specyficzny dla profilu jest dostarczany po skonfigurowaniu pierwszego testu.** W tym miejscu możesz nadal dodawać lub usuwać testy do tego profilu bez konieczności osadzania nowego skryptu. Aby uzyskać więcej informacji na temat programu Internet Analyzer, zobacz [Omówienie](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

Analizator Internetu wymaga dostępu do platformy Azure i innych usług firmy Microsoft w celu poprawnego działania. Przed osadzeniem klienta Zezwól na dostęp do sieci `fpc.msedge.net` i wszelkich wstępnie skonfigurowanych adresów URL punktów końcowych (widocznych za pomocą [interfejsu wiersza polecenia](internet-analyzer-cli.md)).

## <a name="find-the-client-script-url"></a>Znajdowanie adresu URL skryptu klienta

Adres URL skryptu można znaleźć za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure po skonfigurowaniu testu. Aby uzyskać więcej informacji, zobacz [Tworzenie zasobu analizatora internetowego](internet-analyzer-create-test-portal.md).

Sposób 1. W Azure Portal Użyj [tego linku](https://aka.ms/InternetAnalyzerPreviewPortal) , aby otworzyć stronę portalu w wersji zapoznawczej dla usługi Azure Internet Analyzer. Przejdź do swojego profilu analizatora Internetu, aby wyświetlić adres URL skryptu, przechodząc do **ustawień konfiguracja >**.

Sposób 2. Sprawdź właściwość przy użyciu interfejsu wiersza polecenia platformy Azure `scriptFileUri` .
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>Szczegóły klienta

Skrypt jest generowany dla profilu i testów. Po załadowaniu skrypt zostanie wykonany przy 2-sekundowym opóźnieniu. Najpierw kontaktuje się z usługą analizatora internetowego w celu pobrania listy punktów końcowych skonfigurowanych w testach. Następnie uruchamia pomiary i przekazuje wynikowe wyniki z powrotem do usługi analizatora internetowego.

## <a name="client-examples"></a>Przykłady klienta

W tych przykładach przedstawiono kilka podstawowych metod osadzania kodu JavaScript klienta na stronie sieci Web lub aplikacji. Używamy `0bfcb32638b44927935b9df86dcfe397` jako przykładowego identyfikatora profilu dla adresu URL skryptu.

### <a name="run-on-page-load"></a>Uruchom przy ładowaniu strony
Najprostszą metodą jest użycie znacznika skryptu wewnątrz bloku tagu Meta. Ten tag będzie wykonywał skrypt raz na ładowanie strony.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>Następne kroki

Przeczytaj [często zadawane pytania dotyczące narzędzia Internet Analyzer](internet-analyzer-faq.md)
