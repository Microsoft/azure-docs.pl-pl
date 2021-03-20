---
title: Tworzenie fabryki obrazu w Azure DevTest Labs | Microsoft Docs
description: W tym artykule pokazano, jak skonfigurować niestandardową fabrykę obrazów przy użyciu przykładowych skryptów dostępnych w repozytorium git (Azure DevTest Labs).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 14c8338d6a5ae21847da3a9c774ea6dcdac33ae4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85482075"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Utwórz niestandardową fabrykę obrazów w Azure DevTest Labs
W tym artykule pokazano, jak skonfigurować niestandardową fabrykę obrazów przy użyciu przykładowych skryptów dostępnych w [repozytorium git](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>Co to jest fabryka obrazów?
Fabryka obrazów to rozwiązanie typu "Konfiguracja jako kod", które regularnie kompiluje i dystrybuuje obrazy wraz ze wszystkimi wymaganymi konfiguracjami. Obrazy w fabryce obrazu są zawsze aktualne, a aktualna konserwacja jest niemal zerowa, gdy cały proces jest zautomatyzowany. I, ponieważ wszystkie wymagane konfiguracje znajdują się już w obrazie, zapisuje czas od ręcznego skonfigurowania systemu po utworzeniu maszyny wirtualnej przy użyciu podstawowego systemu operacyjnego.

Znaczący akcelerator, który umożliwia deweloperom uzyskanie stanu gotowości w DevTest Labs. Minusem obrazów niestandardowych polega na tym, że coś dodatkowego do utrzymania w laboratorium. Na przykład wersje próbne produktów tracą ważność z upływem czasu (lub) nie są stosowane nowo wydane aktualizacje zabezpieczeń, które wymuszają okresowe odświeżanie obrazu niestandardowego. Przy użyciu fabryki obrazu masz definicję obrazu, który został zaewidencjonowany do kontroli kodu źródłowego i ma zautomatyzowany proces tworzenia obrazów niestandardowych na podstawie definicji.

Rozwiązanie pozwala na szybkie tworzenie maszyn wirtualnych na podstawie obrazów niestandardowych, eliminując jednocześnie dodatkowe bieżące koszty konserwacji. Dzięki temu rozwiązaniu można automatycznie tworzyć obrazy niestandardowe, rozpowszechniać je do innych laboratoriów DevTest i wycofać stare obrazy. W poniższym filmie wideo znajdziesz informacje o fabryce obrazu i sposobie ich implementacji za pomocą DevTest Labs.  Wszystkie skrypty programu Azure PowerShell są dostępne bezpłatnie i znajdują się tutaj:  [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory) .

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Ogólny widok rozwiązania
Rozwiązanie pozwala na szybkie tworzenie maszyn wirtualnych na podstawie obrazów niestandardowych, eliminując jednocześnie dodatkowe bieżące koszty konserwacji. Dzięki temu rozwiązaniu można automatycznie tworzyć obrazy niestandardowe i dystrybuować je do innych laboratoriów DevTest. Używasz usługi Azure DevOps (dawniej Visual Studio Team Services) jako aparatu aranżacji do automatyzowania wszystkich operacji w laboratoriach DevTest.

![Ogólny widok rozwiązania](./media/create-image-factory/high-level-view-of-solution.png)

Istnieje [rozszerzenie VSTS dla laboratoriów DevTest](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) , które umożliwia wykonywanie tych pojedynczych czynności:

- Tworzenie obrazu niestandardowego
- Tworzenie maszyny wirtualnej
- Usuwanie maszyny wirtualnej
- Utwórz środowisko
- Usuń środowisko
- Wypełnij środowisko

Korzystanie z rozszerzenia DevTest Labs to prosty sposób na rozpoczęcie pracy z automatycznym tworzeniem obrazów niestandardowych w usłudze DevTest Labs.

Istnieje alternatywna implementacja przy użyciu skryptu programu PowerShell dla bardziej złożonego scenariusza. Korzystając z programu PowerShell, można w pełni zautomatyzować fabrykę obrazów na podstawie DevTestych laboratoriów, które mogą być używane w przypadku ciągłej integracji i ciągłego dostarczania (CI/CD) łańcucha narzędzi. Zasady zastosowane w tym alternatywnym rozwiązaniu są następujące:

- Typowe aktualizacje nie powinny wymagać zmian w fabryce obrazu. (na przykład dodanie nowego typu obrazu niestandardowego, automatyczne wycofanie starych obrazów, dodanie nowego laboratorium DevTest "Endpoint" do odbierania obrazów niestandardowych itd.)
- Typowe zmiany są obsługiwane przez kontrolę kodu źródłowego (infrastruktura jako kod)
- DevTest laboratoria otrzymujące obrazy niestandardowe nie mogą znajdować się w tej samej subskrypcji platformy Azure (w ramach subskrypcji obejmujących laboratoria)
- Skrypty programu PowerShell muszą być wielokrotnego użytku, aby w razie potrzeby można było korzystać z dodatkowych fabryk

## <a name="next-steps"></a>Następne kroki
Przejdź do następnego artykułu w tej sekcji: [Uruchamianie fabryki obrazu z usługi Azure DevOps](image-factory-set-up-devops-lab.md)
