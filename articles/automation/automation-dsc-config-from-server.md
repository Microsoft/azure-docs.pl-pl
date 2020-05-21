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
ms.openlocfilehash: d783ce7193faed4110d07988d3f3f05951c80a45
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711401"
---
# <a name="create-configurations-from-existing-servers-for-azure-automation-state-configuration"></a>Tworzenie konfiguracji z istniejących serwerów na potrzeby konfiguracji stanu Azure Automation

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

Po wygenerowaniu plików danych można użyć ich ze [skryptami konfiguracji DSC](/powershell/scripting/dsc/overview/overview) w celu wygenerowania plików MOF i [przekazania plików MOF do Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Następnie zarejestruj serwery [lokalnie](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) lub na [platformie Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) , aby przeprowadzić konfigurację ściągania.

Aby wypróbować ReverseDSC, odwiedź [Galeria programu PowerShell](https://www.powershellgallery.com/packages/ReverseDSC/) i Pobierz rozwiązanie lub kliknij pozycję "Witryna projektu", aby wyświetlić [dokumentację](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Następne kroki

- [Przegląd konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Zasoby DSC](/powershell/scripting/dsc/resources/resources)
- [Konfigurowanie Configuration Manager lokalnego](/powershell/scripting/dsc/managing-nodes/metaconfig)
