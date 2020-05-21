---
title: Konfigurowanie danych w odpowiedniej skali dla konfiguracji stanu Azure Automation
description: W tym artykule opisano sposób konfigurowania danych w odpowiedniej skali na potrzeby konfiguracji stanu Azure Automation.
keywords: DSC, PowerShell, konfiguracja, instalacja
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5f6773d9f2210fd1b0342c9586d89155082bf8ff
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711452"
---
# <a name="configure-data-at-scale-for-azure-automation-state-configuration"></a>Konfigurowanie danych w odpowiedniej skali dla konfiguracji stanu Azure Automation

> Dotyczy: Windows PowerShell 5,1

Zarządzanie setkami lub tysiącami serwerów może być wyzwaniem.
Klienci otrzymali opinię, że najbardziej trudnym aspektem jest zarządzanie [danymi konfiguracji](/powershell/scripting/dsc/configurations/configdata).
Organizowanie informacji w ramach konstrukcji logicznych, takich jak lokalizacja, typ i środowisko.

> [!NOTE]
> Ten artykuł odnosi się do rozwiązania, które jest obsługiwane przez społeczność typu open source.
> Pomoc techniczna jest dostępna tylko w formie współpracy GitHub, a nie od firmy Microsoft.

## <a name="community-project-datum"></a>Projekt społeczności: Datum

Utworzono rozwiązanie obsługiwane przez społeczność o nazwie [Datum](https://github.com/gaelcolas/Datum) , aby rozwiązać ten problem.
Datum tworzy doskonałe pomysły dotyczące innych platform zarządzania konfiguracją i implementuje ten sam typ rozwiązania dla DSC programu PowerShell.
Informacje są [zorganizowane w plikach tekstowych](https://github.com/gaelcolas/Datum#3-intended-usage) w oparciu o koncepcje logiczne.
Oto przykłady:

- Ustawienia, które powinny być stosowane globalnie
- Ustawienia, które powinny być stosowane do wszystkich serwerów w lokalizacji
- Ustawienia, które powinny być stosowane dla wszystkich serwerów baz danych
- Ustawienia poszczególnych serwerów

Te informacje są zorganizowane w preferowany format pliku (JSON, YAML lub PSD1).
Polecenia cmdlet są udostępniane do generowania plików danych konfiguracji przez [konsolidowanie informacji](https://github.com/gaelcolas/Datum#datum-tree) z każdego pliku w programie do pojedynczego widoku roli serwera lub serwera.

Po wygenerowaniu plików danych można użyć ich ze [skryptami konfiguracji DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) w celu wygenerowania plików MOF i [przekazania plików MOF do Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Następnie zarejestruj serwery [lokalnie](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) lub na [platformie Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) , aby przeprowadzić konfigurację ściągania.

Aby wypróbować podstawę, odwiedź [Galeria programu PowerShell](https://www.powershellgallery.com/packages/datum/) i Pobierz rozwiązanie lub kliknij pozycję "Witryna projektu", aby wyświetlić [dokumentację](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Następne kroki

- [Przegląd konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Zasoby DSC](/powershell/scripting/dsc/resources/resources)
- [Konfigurowanie Configuration Manager lokalnego](/powershell/scripting/dsc/managing-nodes/metaconfig)
