---
title: Używanie Visual Studio na maszynie wirtualnej platformy Azure
description: Używanie Visual Studio na maszynie wirtualnej platformy Azure.
author: andysterland
manager: andster
ms.service: virtual-machines
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: andster
keywords: visualstudio
ms.openlocfilehash: f26bec64012e0b0909b7df5422c57ff2cb1c347e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478560"
---
# <a name="visual-studio-images-on-azure"></a>Visual Studio obrazów na platformie Azure
Korzystanie Visual Studio na wstępnie skonfigurowanej maszynie wirtualnej platformy Azure to szybki i łatwy sposób na szybkie i łatwe zejścia z niczego do działającego środowiska projektowego. Obrazy systemu z różnymi Visual Studio konfiguracji są dostępne w Azure Marketplace [.](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure)

Dopiero zaczynasz korzystać z platformy Azure? [Utwórz bezpłatne konto platformy Azure.](https://azure.microsoft.com/free)

> [!NOTE]
> Nie wszystkie subskrypcje kwalifikują się do wdrażania Windows 10 obrazów. Aby uzyskać więcej informacji, zobacz Use Windows client in Azure for dev/test scenarios (Korzystanie z klienta [systemu Windows na platformie Azure w scenariuszach tworzenia i testowania)](./client-images.md)

## <a name="what-configurations-and-versions-are-available"></a>Jakie konfiguracje i wersje są dostępne?
Obrazy dla najnowszych wersji głównych, Visual Studio 2019, Visual Studio 2017 i Visual Studio 2015, można znaleźć w Azure Marketplace.  Dla każdej wydanej wersji głównej jest dostępna oryginalna wersja "wydana w Internecie" (RTW) i najnowsze zaktualizowane wersje.  Każda z tych wersji oferuje Visual Studio Enterprise i Visual Studio Community wersji.  Te obrazy są aktualizowane co najmniej raz w miesiącu w celu dołączyć najnowsze Visual Studio systemu Windows.  Chociaż nazwy obrazów pozostają takie same, opis każdego obrazu zawiera zainstalowaną wersję produktu i datę "od" obrazu.

| Wersja wydania                                                                                                                                                | Wersje              | Wersja produktu   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019 r.: najnowsza (wersja 16.8)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Enterprise, Community | Wersja 16.8.0    |
| Visual Studio 2019: RTW                         | Przedsiębiorstwa | Wersja 16.0.20    |
| Visual Studio 2017: Najnowsza (wersja 15.9)           | Enterprise, Community | Wersja 15.9.29   |
| Visual Studio 2017: RTW                             | Enterprise, Community | Wersja 15.0.28  |
| Visual Studio 2015: najnowsza (aktualizacja Update 3)               | Enterprise, Community | Wersja 14.0.25431.01 |

> [!NOTE]
> Zgodnie z zasadami obsługi firmy Microsoft pierwotnie wydana (RTW) wersja programu Visual Studio 2015 wygasła w celu obsługi. Visual Studio 2015 Update 3 jest jedyną oferowaną wersją produktu Visual Studio 2015.

Aby uzyskać więcej informacji, zobacz [zasady Visual Studio obsługi.](https://www.visualstudio.com/productinfo/vs-servicing-vs)

## <a name="what-features-are-installed"></a>Jakie funkcje są zainstalowane?
Każdy obraz zawiera zalecany zestaw funkcji dla tej Visual Studio wersji. Ogólnie rzecz biorąc, instalacja obejmuje:

* Wszystkie dostępne obciążenia, w tym zalecane składniki opcjonalne poszczególnych obciążeń. Więcej szczegółów na temat obciążeń, składników i zestawów SDK dołączonych Visual Studio można znaleźć w Visual Studio [dokumentacji](https://docs.microsoft.com/visualstudio/install/workload-and-component-ids)
* Zestawy SDK .NET 4.6.2 i .NET 4.7, pakiety docelowe i Narzędzia deweloperskie
* Visual F#
* Rozszerzenia GitHub dla programu Visual Studio
* LINQ to SQL narzędzi

Wiersz polecenia używany do instalowania Visual Studio podczas budowania obrazów jest następujący:

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

Jeśli obrazy nie zawierają wymaganej Visual Studio, możesz przekazać opinię za pomocą narzędzia do opinii w prawym górnym rogu strony.

## <a name="what-size-vm-should-i-choose"></a>Jaki rozmiar maszyny wirtualnej należy wybrać?
Platforma Azure oferuje pełny zakres rozmiarów maszyn wirtualnych. Ponieważ Visual Studio to zaawansowana aplikacja wielowątkowa, chcesz mieć rozmiar maszyny wirtualnej, który zawiera co najmniej dwa procesory i 7 GB pamięci. Zalecamy następujące rozmiary maszyn wirtualnych dla Visual Studio obrazów:

   * Standardowa_D2_v3
   * Standardowa_D2s_v3
   * Standardowa_D4_v3
   * Standardowa_D4s_v3
   * Standardowa_D2_v2
   * Standard_D2S_v2
   * Standardowa_D3_v2
    
Aby uzyskać więcej informacji na temat najnowszych rozmiarów maszyn, zobacz [Sizes for Windows virtual machines in Azure (Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure).](../sizes.md)

Na platformie Azure możesz ponownie zrównoważyć wybór początkowy, zmiana rozmiaru maszyny wirtualnej. Możesz aprowizować nową maszynę wirtualną o bardziej odpowiednim rozmiarze lub zmienić rozmiar istniejącej maszyny wirtualnej na inny podstawowy sprzęt. Aby uzyskać więcej informacji, zobacz [Zmienianie rozmiaru maszyny wirtualnej z systemem Windows.](./resize-vm.md)

## <a name="after-the-vm-is-running-whats-next"></a>Co dalej, po uruchomieniu maszyny wirtualnej?
Visual Studio modelu "bring your own license" na platformie Azure. Podobnie jak w przypadku instalacji na sprzęcie własnościowym, jednym z pierwszych kroków jest licencjonowanie Visual Studio instalacji. Aby odblokować Visual Studio, należy użyć jednej z tych Visual Studio:
- Zaloguj się przy użyciu konto Microsoft, która jest skojarzona z Visual Studio subskrypcji 
- Odblokuj Visual Studio przy użyciu klucza produktu, który jest dostępny podczas początkowego zakupu

Aby uzyskać więcej informacji, zobacz [Logowanie do Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) i Jak [odblokować Visual Studio](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Jak mogę zapisać maszynę wirtualną do użytku w przyszłości lub w zespole?

Spektrum środowisk deweloperskich jest ogromne, a tworzenie bardziej złożonych środowisk wiąże się z rzeczywistymi kosztami. Niezależnie od konfiguracji środowiska możesz zapisać lub przechwycić skonfigurowaną maszynę wirtualną jako "obraz podstawowy" do użytku w przyszłości lub dla innych członków zespołu. Następnie podczas rozruchu nowej maszyny wirtualnej aprowizuje się ją z obrazu podstawowego, a nie z Azure Marketplace wirtualnego.

Krótkie podsumowanie: użyj narzędzia przygotowywania systemu (Sysprep), zamknij uruchamianą maszynę wirtualną, a następnie przechwyć *(Rysunek 1)* maszynę wirtualną jako obraz za pomocą interfejsu użytkownika w Azure Portal. Platforma Azure `.vhd` zapisuje plik zawierający obraz na twoim koncie magazynu. Nowy obraz zostanie następnie wyświetlona jako zasób Obraz na liście zasobów subskrypcji.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Rysunek 1) Przechwyć obraz za pomocą Azure Portal użytkownika.*</center>

Aby uzyskać więcej informacji, zobacz Create a managed image of a generalized VM in Azure (Tworzenie obrazu zarządzanego [uogólnionych maszyn wirtualnych na platformie Azure).](./capture-image-resource.md)

> [!IMPORTANT]
> Nie zapomnij użyć narzędzia Sysprep do przygotowania maszyny wirtualnej. Jeśli pominiesz ten krok, platforma Azure nie będzie w stanie aprowizować maszyny wirtualnej z obrazu.

> [!NOTE]
> Nadal wiążesz się z pewnymi kosztami magazynowania obrazów, ale ten koszt przyrostowy można ponieść w porównaniu z kosztami narzutu na ponowne skompilowanie maszyny wirtualnej od podstaw dla każdego członka zespołu, który jej potrzebuje. Na przykład utworzenie i przechowywanie obrazu o rozmiarze 127 GB przez miesiąc, który będzie używany przez cały zespół, kosztuje kilka dolarów. Jednak te koszty są coraz ważne w porównaniu z godzinami, w które każdy pracownik inwestuje w tworzenie i weryfikowanie prawidłowo skonfigurowanego pola dewelopera do użytku indywidualnego.

Ponadto zadania programowe lub technologie mogą wymagać większej skali, na przykład różnych konfiguracji developmentowych i wielu konfiguracji maszyn. Możesz użyć Azure DevTest Labs, aby _utworzyć_ przepisy, które automatyzują tworzenie "złotego obrazu". Za pomocą laboratorium DevTest Labs można również zarządzać zasadami dla maszyn wirtualnych uruchomionych przez zespół. [Korzystanie Azure DevTest Labs dla deweloperów](../../devtest-labs/devtest-lab-developer-lab.md) jest najlepszym źródłem informacji na temat usługi DevTest Labs.

## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz o wstępnie skonfigurowanych obrazach Visual Studio, następnym krokiem jest utworzenie nowej maszyny wirtualnej:

* [Tworzenie maszyny wirtualnej za pośrednictwem Azure Portal](quick-create-portal.md)
* [Windows Virtual Machines omówienie](overview.md)
