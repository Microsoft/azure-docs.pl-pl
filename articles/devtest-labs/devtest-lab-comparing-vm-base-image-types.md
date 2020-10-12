---
title: Porównanie niestandardowych obrazów i formuł w DevTest Labs | Microsoft Docs
description: Dowiedz się więcej o różnicach między obrazami niestandardowymi i formułami w formie baz maszyn wirtualnych, aby określić, który z nich najlepiej odpowiada Twojemu środowisku.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: df7a8e6209f8033eb5a29c65079e9c2f4cbbe544
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87287534"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Porównanie niestandardowych obrazów i formuł w DevTest Labs
Zarówno [obrazy niestandardowe](devtest-lab-create-template.md) , jak i [formuły](devtest-lab-manage-formulas.md) mogą być używane jako podstawy dla [utworzonych nowych maszyn wirtualnych](devtest-lab-add-vm.md). Jednak różnica między obrazami niestandardowymi i formułami polega na tym, że obraz niestandardowy jest po prostu obrazem opartym na wirtualnym dysku twardym, podczas gdy formuła jest obrazem opartym na wirtualnym dysku twardym, a *także ze* wstępnie skonfigurowanymi ustawieniami, takimi jak rozmiar maszyny wirtualnej, Sieć wirtualna, podsieć i artefakty. Te wstępnie skonfigurowane ustawienia są konfigurowane z wartościami domyślnymi, które mogą zostać zastąpione podczas tworzenia maszyny wirtualnej. W tym artykule wyjaśniono niektóre zalety (specjalistów) i wady (wady) korzystania z obrazów niestandardowych zamiast formuł.

## <a name="custom-image-pros-and-cons"></a>Niestandardowe zalety i wady obrazu
Obrazy niestandardowe stanowią statyczny, niezmienny sposób tworzenia maszyn wirtualnych z poziomu odpowiedniego środowiska. 

**Zalety**

* Inicjowanie obsługi maszyn wirtualnych z obrazu niestandardowego jest szybkie, ponieważ nie ma żadnych zmian po zainstalowaniu maszyny wirtualnej z obrazu. Innymi słowy, nie ma żadnych ustawień do zastosowania, ponieważ obraz niestandardowy jest tylko obrazem bez ustawień. 
* Maszyny wirtualne utworzone na podstawie pojedynczego obrazu niestandardowego są identyczne.

**Wady**

* Jeśli konieczne jest zaktualizowanie pewnego aspektu obrazu niestandardowego, należy ponownie utworzyć obraz.  

## <a name="formula-pros-and-cons"></a>Specjaliści i wady dotyczące formuł
Formuły zapewniają dynamiczny sposób tworzenia maszyn wirtualnych z poziomu żądanej konfiguracji/ustawień.

**Zalety**

* Zmiany w środowisku można przechwytywać na bieżąco za pośrednictwem artefaktów. Na przykład jeśli chcesz, aby maszyna wirtualna była zainstalowana z najnowszymi bitami z potoku wydania lub zarejestrowana w najnowszym kodzie z repozytorium, możesz po prostu określić artefakt, który wdraża najnowsze bity, lub zarejestrować najnowszy kod w formule wraz z docelowym obrazem bazowym. Za każdym razem, gdy ta formuła jest używana do tworzenia maszyn wirtualnych, najnowsze bity/kod są wdrażane/rejestrowane na maszynie wirtualnej. 
* Formuły mogą definiować domyślne ustawienia, które nie mogą zapewnić obrazów niestandardowych, takich jak rozmiary maszyn wirtualnych i ustawienia sieci wirtualnej. 
* Ustawienia zapisane w formule są wyświetlane jako wartości domyślne, ale można je modyfikować podczas tworzenia maszyny wirtualnej. 

**Wady**

* Tworzenie maszyny wirtualnej na podstawie formuły może zająć więcej czasu niż utworzenie maszyny wirtualnej na podstawie obrazu niestandardowego.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Powiązane wpisy w blogu
* [Obrazy niestandardowe lub formuły?](./devtest-lab-faq.md#blog-post)

## <a name="next-steps"></a>Następne kroki
- [DevTest Labs — często zadawane pytania](devtest-lab-faq.md)
