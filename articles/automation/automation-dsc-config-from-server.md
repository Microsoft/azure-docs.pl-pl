---
title: Tworzenie konfiguracji z istniejących serwerów na potrzeby konfiguracji stanu Azure Automation
description: W tym artykule opisano sposób tworzenia konfiguracji z istniejących serwerów na potrzeby konfiguracji stanu Azure Automation.
keywords: DSC, PowerShell, konfiguracja, instalacja
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8f376fb383e50a39f0f12d45cf9b5ae47ad6fcbb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86186507"
---
# <a name="create-configurations-from-existing-servers"></a>Tworzenie konfiguracji z istniejących serwerów

> Dotyczy: Windows PowerShell 5,1

Tworzenie konfiguracji z istniejących serwerów może stanowić wyzwanie.
Możesz nie chcieć dowiedzieć się, jakie są *wszystkie* ustawienia, tylko te, które Cię interesują.
Nawet należy wiedzieć, w jakiej kolejności należy zastosować ustawienia, aby konfiguracja została pomyślnie zastosowana.

> [!NOTE]
> Ten artykuł odnosi się do rozwiązania, które jest obsługiwane przez społeczność typu open source.
> Pomoc techniczna jest dostępna tylko w formie współpracy GitHub, a nie od firmy Microsoft.

## <a name="community-project-reversedsc"></a>Projekt społeczności: ReverseDSC

Rozwiązanie obsługiwane przez społeczność o nazwie [ReverseDSC](https://github.com/microsoft/reversedsc) zostało utworzone do pracy w tym obszarze, w którym jest uruchamiany program SharePoint.

Rozwiązanie jest oparte na [zasobie SharePointDSC](https://github.com/powershell/sharepointdsc) i rozszerza je, aby organizować [zbieranie informacji](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) z istniejących serwerów programu SharePoint.
Najnowsza wersja ma wiele [trybów wyodrębniania](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) , aby określić poziom informacji do uwzględnienia.

Wynik użycia rozwiązania generuje [dane konfiguracji](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) , które mają być używane ze skryptami konfiguracji SharePointDSC.

Po wygenerowaniu plików danych można użyć ich ze [skryptami konfiguracji DSC](/powershell/scripting/dsc/overview/overview) w celu wygenerowania plików MOF i [przekazania plików MOF do Azure Automation](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Następnie zarejestruj serwery [lokalnie](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) lub na [platformie Azure](./automation-dsc-onboarding.md#enable-azure-vms) , aby przeprowadzić konfigurację ściągania.

Aby wypróbować ReverseDSC, odwiedź [Galeria programu PowerShell](https://www.powershellgallery.com/packages/ReverseDSC/) i Pobierz rozwiązanie lub kliknij pozycję "Witryna projektu", aby wyświetlić [dokumentację](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Następne kroki

- Aby poznać program PowerShell DSC, zobacz [Omówienie konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Dowiedz się więcej o zasobach DSC programu PowerShell w [zasobach DSC](/powershell/scripting/dsc/resources/resources).
- Aby uzyskać szczegółowe informacje na temat konfiguracji lokalnego Configuration Manager, zobacz [konfigurowanie Configuration Manager lokalnego](/powershell/scripting/dsc/managing-nodes/metaconfig).
