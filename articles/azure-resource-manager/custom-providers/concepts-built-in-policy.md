---
title: Wdróż skojarzenia przy użyciu zasad
description: Dowiedz się więcej o wdrażaniu skojarzeń dla dostawcy niestandardowego przy użyciu usługi Azure Policy.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 00cd1d39c0110aac9ea96f73127e83197976c95a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "82190133"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Wdróż skojarzenia dla dostawcy niestandardowego przy użyciu Azure Policy

Za pomocą zasad platformy Azure można wdrażać skojarzenia w celu kojarzenia zasobów z dostawcą niestandardowym. W tym artykule opisano wbudowane zasady, które wdrażają skojarzenia i jak można korzystać z tych zasad.

## <a name="built-in-policy-to-deploy-associations"></a>Wbudowane zasady wdrażania skojarzeń

Wdróż skojarzenia dla dostawcy niestandardowego to wbudowane zasady, których można użyć do wdrożenia skojarzenia w celu skojarzenia zasobu z dostawcą niestandardowym. Zasady akceptują trzy parametry:

- Niestandardowy identyfikator dostawcy — ten identyfikator jest IDENTYFIKATORem zasobu niestandardowego dostawcy, z którym należy skojarzyć zasoby.
- Typy zasobów do skojarzenia — te typy zasobów są listami typów zasobów, które mają być skojarzone z dostawcą niestandardowym. Do niestandardowego dostawcy można skojarzyć wiele typów zasobów przy użyciu tych samych zasad.
- Prefiks nazwy skojarzenia — ten ciąg jest prefiksem, który ma zostać dodany do nazwy tworzonego zasobu skojarzenia. Wartość domyślna to "DeployedByPolicy".

Zasady korzystają z oceny DeployIfNotExists. Jest on uruchamiany po obsłudze żądania utworzenia lub aktualizacji zasobu przez dostawcę zasobów, a Ocena zwróci kod stanu sukcesu. Następnie zasób skojarzenia zostanie wdrożony przy użyciu wdrożenia szablonu.
Aby uzyskać więcej informacji na temat skojarzeń, zobacz Dołączanie [zasobów niestandardowych dostawców platformy Azure](./concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Jak używać wbudowanych zasad skojarzeń 

### <a name="prerequisites"></a>Wymagania wstępne
Jeśli niestandardowy dostawca wymaga uprawnień do zakresu zasad w celu wykonania akcji, wdrożenie zasad zasobu skojarzenia nie będzie działać bez udzielania uprawnień.

### <a name="policy-assignment"></a>Przypisanie zasad
Aby użyć wbudowanych zasad, Utwórz przypisanie zasad i przypisz skojarzenia wdrażania dla niestandardowych zasad dostawcy. Zasady będą identyfikować niezgodne zasoby i wdrażać skojarzenia dla tych zasobów.

![Przypisywanie wbudowanych zasad](media/concepts-built-in-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>Uzyskiwanie pomocy

Jeśli masz pytania dotyczące programowania niestandardowych dostawców zasobów platformy Azure, spróbuj zadawać je na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Na podobnej pytanie mogły już zostać odebrane odpowiedzi, więc najpierw należy sprawdzić przed opublikowaniem. Dodaj tag, ```azure-custom-providers``` Aby uzyskać szybką odpowiedź!

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o używaniu wbudowanych zasad do wdrażania skojarzeń. Zobacz następujące artykuły, aby dowiedzieć się więcej:

- [Pojęcia: dołączanie zasobów niestandardowych dostawców platformy Azure](./concepts-resource-onboarding.md)
- [Samouczek: dołączanie zasobów z dostawcami niestandardowymi](./tutorial-resource-onboarding.md)
- [Samouczek: Tworzenie niestandardowych akcji i zasobów na platformie Azure](./tutorial-get-started-with-custom-providers.md)
- [Szybki Start: Tworzenie niestandardowego dostawcy zasobów i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Instrukcje: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Instrukcje: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)
