---
title: Korzystanie z programu Visual Studio na maszynie wirtualnej platformy Azure
description: Korzystanie z programu Visual Studio na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cathysull
manager: cathys
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.date: 12/04/2019
ms.author: cathys
keywords: visualstudio
ms.openlocfilehash: 76c8ec8f3d691a897ec924e06b76beec746ad14a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451588"
---
# <a name="visual-studio-images-on-azure"></a>Obrazy programu Visual Studio na platformie Azure
Korzystanie z programu Visual Studio na wstępnie skonfigurowanej maszynie wirtualnej platformy Azure (VM) to szybki i łatwy sposób przejścia od zera do up-and-running środowiska programistycznego. Obrazy systemu z różnymi konfiguracjami programu Visual Studio są dostępne w portalu [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure)

Dopiero zaczynasz korzystać z platformy Azure? [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free).

> [!NOTE]
> Nie wszystkie subskrypcje kwalifikują się do wdrożenia obrazów systemu Windows 10. Aby uzyskać więcej informacji, zobacz [Używanie klienta systemu Windows na platformie Azure w scenariuszach deweloperskich/testowych](https://docs.microsoft.com/azure/virtual-machines/windows/client-images)

## <a name="what-configurations-and-versions-are-available"></a>Jakie konfiguracje i wersje są dostępne?
Obrazy najnowszych wersji głównych, Visual Studio 2019, Visual Studio 2017 i Visual Studio 2015, można znaleźć w portalu Azure Marketplace.  Dla każdej wydanej wersji głównej, widzisz oryginalnie "wydany do sieci" (RTW) wersja i najnowsze zaktualizowane wersje.  Każda z tych wersji oferuje wersje Visual Studio Enterprise i Visual Studio Community.  Te obrazy są aktualizowane co najmniej co miesiąc, aby uwzględnić najnowsze aktualizacje programu Visual Studio i systemu Windows.  Chociaż nazwy obrazów pozostają takie same, opis każdego obrazu zawiera zainstalowaną wersję produktu i datę "na dzień".

| Wersja wydania                                                                                                                                                | Wersje              | Wersja produktu   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: Najnowsze (wersja 16.4)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Przedsiębiorstwo, Wspólnota | Wersja 16.4.0    |
| [Visual Studio 2019: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019?tab=Overview)                         | Przedsiębiorstwo, Wspólnota | Wersja 16.0.9    |
| [Visual Studio 2017: Najnowsze (wersja 15.9)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)           | Przedsiębiorstwo, Wspólnota | Wersja 15.9.17   |
| [Visual Studio 2017: RTW](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)                             | Przedsiębiorstwo, Wspólnota | Wersja 15.0.27  |
| [Visual Studio 2015: Najnowsze (Aktualizacja 3)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio?tab=Overview)               | Przedsiębiorstwo, Wspólnota | Wersja 14.0.25431.01 |

> [!NOTE]
> Zgodnie z zasadami obsługi firmy Microsoft pierwotnie wydana wersja programu Visual Studio 2015 wygasła w celu obsługi. Visual Studio 2015 Update 3 jest jedyną pozostałą wersją oferowaną dla linii produktów programu Visual Studio 2015.

Aby uzyskać więcej informacji, zobacz [zasady obsługi programu Visual Studio](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Jakie funkcje są zainstalowane?
Każdy obraz zawiera zestaw funkcji zalecane dla tej wersji programu Visual Studio. Ogólnie rzecz biorąc, instalacja obejmuje:

* Wszystkie dostępne obciążenia, w tym zalecane opcjonalne składniki każdego obciążenia
* Zestawów SDK .NET 4.6.2 i .NET 4.7, pakietów targetowania i narzędzi programistycznych
* Visual F#
* Rozszerzenia GitHub dla programu Visual Studio
* Linq do narzędzi SQL

Wiersz polecenia używany do instalowania programu Visual Studio podczas tworzenia obrazów jest następujący:

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

Jeśli obrazy nie zawierają funkcji programu Visual Studio, która jest wymagana, prześlij opinię za pośrednictwem narzędzia opinii w prawym górnym rogu strony.

## <a name="what-size-vm-should-i-choose"></a>Jaki rozmiar maszyny Wirtualnej wybrać?
Platforma Azure oferuje pełny zakres rozmiarów maszyn wirtualnych. Ponieważ visual studio jest zaawansowana, wielowątkowa aplikacja, chcesz rozmiar maszyny Wirtualnej, który zawiera co najmniej dwa procesory i 7 GB pamięci. Dla obrazów programu Visual Studio zaleca się następujące rozmiary maszyn wirtualnych:

   * Standardowa_D2_v3
   * Standardowa_D2s_v3
   * Standardowa_D4_v3
   * Standardowa_D4s_v3
   * Standardowa_D2_v2
   * Standard_D2S_v2
   * Standardowa_D3_v2
    
Aby uzyskać więcej informacji na temat najnowszych rozmiarów maszyn, zobacz [Rozmiary maszyn wirtualnych systemu Windows na platformie Azure](/azure/virtual-machines/windows/sizes).

Za pomocą platformy Azure można zrównoważyć swój początkowy wybór, zmiana rozmiaru maszyny Wirtualnej. Można aprowizować nową maszynę wirtualną o bardziej odpowiednim rozmiarze lub zmienić rozmiar istniejącej maszyny Wirtualnej na inny podstawowy sprzęt. Aby uzyskać więcej informacji, zobacz [Ponowne rozmiary maszyny Wirtualnej systemu Windows](/azure/virtual-machines/windows/resize-vm).

## <a name="after-the-vm-is-running-whats-next"></a>Po uruchomieniu maszyny Wirtualnej, co dalej?
Visual Studio jest zgodny z modelem "przynieś własną licencję" na platformie Azure. Podobnie jak w instalacji na zastrzeżonym sprzęcie, jednym z pierwszych kroków jest licencjonowanie instalacji programu Visual Studio. Aby odblokować program Visual Studio:
- Logowanie się za pomocą konta Microsoft skojarzonego z subskrypcją programu Visual Studio 
- Odblokuj program Visual Studio za pomocą klucza produktu, który został doszła do pierwszego zakupu

Aby uzyskać więcej informacji, zobacz [Logowanie się do programu Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) i Jak [odblokować program Visual Studio](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Jak zapisać maszynę wirtualną dewelopera do wykorzystania w przyszłości lub zespołowej?

Spektrum środowisk programistów jest ogromne, a budowa bardziej złożonych środowisk wiąże się z rzeczywistymi kosztami związanymi z budową bardziej złożonych środowisk. Niezależnie od konfiguracji środowiska można zapisać lub przechwycić skonfigurowaną maszynę wirtualną jako "obraz podstawowy" do wykorzystania w przyszłości lub dla innych członków zespołu. Następnie podczas uruchamiania nowej maszyny Wirtualnej, aprowizować go z obrazu podstawowego, a nie obrazu portalu Azure Marketplace.

Krótkie podsumowanie: Użyj narzędzia przygotowania systemu (Sysprep) i zamknij działającą maszynę wirtualną, a następnie przechwyć *(rysunek 1)* maszynę wirtualną jako obraz za pośrednictwem interfejsu użytkownika w witrynie Azure portal. Platforma Azure `.vhd` zapisuje plik zawierający obraz na wybranym koncie magazynu. Nowy obraz jest następnie wyświetlany jako zasób obrazu na liście zasobów subskrypcji.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Rysunek 1) Przechwytywanie obrazu za pośrednictwem interfejsu użytkownika portalu azure.*</center>

Aby uzyskać więcej informacji, zobacz [Tworzenie zarządzanego obrazu uogólnionej maszyny Wirtualnej na platformie Azure](/azure/virtual-machines/windows/capture-image-resource).

> [!IMPORTANT]
> Nie zapomnij użyć Sysprep do przygotowania maszyny Wirtualnej. Jeśli przegapisz ten krok, platforma Azure nie może aprowizować maszyny Wirtualnej z obrazu.

> [!NOTE]
> Nadal ponosisz pewne koszty przechowywania obrazów, ale ten koszt przyrostowy może być nieznaczny w porównaniu do kosztów ogólnych, aby odbudować maszynę wirtualną od podstaw dla każdego członka zespołu, który jej potrzebuje. Na przykład utworzenie i przechowywanie obrazu o masie 127 GB przez miesiąc, który jest wielokrotnego użytku przez cały zespół, kosztuje kilka dolarów. Jednak koszty te są nieistotne w porównaniu do godzin, w których każdy pracownik inwestuje w tworzenie i sprawdzanie poprawności odpowiednio skonfigurowanego pola deweloperów do indywidualnego użytku.

Ponadto zadania programistyczne lub technologie mogą wymagać większej skali, takich jak odmiany konfiguracji programistycznych i wiele konfiguracji maszyn. Za pomocą usługi Azure DevTest Labs można tworzyć _przepisy,_ które automatyzują budowę "złotego obrazu". Można również użyć DevTest Labs do zarządzania zasadami dla zespołu uruchomionych maszyn wirtualnych. [Korzystanie z usługi Azure DevTest Labs dla deweloperów](/azure/devtest-lab/devtest-lab-developer-lab) jest najlepszym źródłem, aby uzyskać więcej informacji na temat DevTest Labs.

## <a name="next-steps"></a>Następne kroki
Teraz, gdy wiesz o wstępnie skonfigurowanych obrazów programu Visual Studio, następnym krokiem jest utworzenie nowej maszyny Wirtualnej:

* [Tworzenie maszyny wirtualnej za pośrednictwem witryny Azure portal](quick-create-portal.md)
* [Omówienie maszyn wirtualnych systemu Windows](overview.md)
