---
title: Tworzenie środowiska zorientowanego na aplikacje — Azure
description: W tym artykule przedstawiono sposób tworzenia środowiska zorientowanego na aplikacje z Cloud Shell Kolonia i Microsoft Azure.
ms.topic: how-to
ms.date: 11/26/2020
ms.openlocfilehash: 88244f268d5ed038e9bb7082d3d5cc1179e5ec4e
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918264"
---
# <a name="create-an-application-centric-environment"></a>Tworzenie środowiska zorientowanego na aplikacje

[Quali CloudShella](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) jest platformą SaaS do dostarczania automatyzacji infrastruktury na dużą skalę w przypadku złożonych środowisk zorientowanych na aplikacje na potrzeby technologii chmurowych, w tym platformy Azure i Kubernetes. CloudShell jest Azure DevTest Labs uzupełnieniem, aby pomóc zespołom deweloperów wdrażać złożone aplikacje w strumieniu wartości, aż do produkcji.

W tym artykule pokazano, jak utworzyć środowisko zorientowane na aplikacje z CloudShell Kolonia i Microsoft Azure.

## <a name="set-up-the-environment-with-cloudshell-colony-and-microsoft-azure"></a>Skonfiguruj środowisko z CloudShell Kolonia i Microsoft Azure

1. Zarejestruj się, aby skorzystać z bezpłatnej wersji próbnej z [kolonii](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview).

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="Zarejestruj się, aby skorzystać z bezpłatnej wersji próbnej":::    
1. Połącz swoje konto platformy Azure ([Wyświetl tutaj kroki](https://colonysupport.quali.com/hc/articles/360008222234)).

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="Witamy w Kolonii":::     
1. Zapraszanie użytkowników do obszaru.
1. Utwórz swój pierwszy plan przy użyciu pliku YAML ([Wyświetl kroki tutaj](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint)).
    1. Połącz repozytorium planów w serwisie GitHub lub BitBucket do Kolonii.
    1. Skorzystaj z przykładowego planu z Kolonii jako podstawy i zmodyfikuj go zgodnie z potrzebami.

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="Testy przeciążeniowe":::    
    1. Opublikuj swój plan, który będzie używany przez inne osoby.
1. Uruchom środowisko aplikacji w piaskownicy przy użyciu Kolonii.

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="Uruchamianie środowiska aplikacji w piaskownicy przy użyciu Kolonii":::    

> [!NOTE]
> Możesz także zintegrować plan w ramach przepływu pracy ciągłej integracji/ciągłego wdrażania w usłudze Azure DevOps ([Wyświetl tutaj kroki](https://colonysupport.quali.com/hc/articles/360008464234)).

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="Nawiązywanie połączenia z potokiem usługi Azure DevOps":::    

## <a name="next-steps"></a>Następne kroki

[Zażądaj demonstracyjnej kombinacji Kolonii](https://info.quali.com/cloudshell-colony-demo-request)
