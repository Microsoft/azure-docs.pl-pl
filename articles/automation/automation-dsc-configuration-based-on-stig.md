---
title: Skonfiguruj dane na podstawie konfiguracji stanu Azure Automation STIG
description: W tym artykule opisano sposób konfigurowania danych na podstawie konfiguracji stanu Azure Automation STIG.
keywords: DSC, PowerShell, konfiguracja, instalacja
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 113a6a259f0c69bdcc3b1684803af54ed7ecbddf
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186490"
---
# <a name="configure-data-based-on-stig"></a>Konfigurowanie danych opartych na STIG

> Dotyczy: Windows PowerShell 5,1

Tworzenie zawartości konfiguracji po raz pierwszy może być trudne.
W wielu przypadkach celem jest zautomatyzowanie konfiguracji serwerów po "linii bazowej", które miejmy nadzieję wyrównany do rekomendacji branżowej.

> [!NOTE]
> Ten artykuł odnosi się do rozwiązania, które jest obsługiwane przez społeczność typu open source.
> Pomoc techniczna jest dostępna tylko w formie współpracy GitHub, a nie od firmy Microsoft.

## <a name="community-project-powerstig"></a>Projekt społeczności: PowerSTIG

Projekt społeczności o nazwie [PowerSTIG](https://github.com/microsoft/powerstig) ma na celu rozwiązanie tego problemu przez wygenerowanie zawartości DSC na podstawie [informacji publicznych](https://public.cyber.mil/stigs/) dostarczonych na temat Stig (Przewodnik po implementacji technicznej zabezpieczeń).

Zajmowanie się liniami bazowymi jest bardziej skomplikowane niż dźwięki IT.
Wiele organizacji musi [udokumentować wyjątki od](https://github.com/microsoft/powerstig#powerstigdata) zasad i zarządzać tymi danymi na dużą skalę.
PowerSTIG problem, dostarczając [zasoby złożone](https://github.com/microsoft/powerstig#powerstigdsc) , aby zająć się każdym obszarem konfiguracji, zamiast próbować rozwiązać cały zakres ustawień w jednym dużym pliku.

Po wygenerowaniu konfiguracji można użyć [skryptów konfiguracji DSC](/powershell/scripting/dsc/configurations/configurations) do wygenerowania plików MOF i [przekazania plików MOF do Azure Automation](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Następnie zarejestruj serwery [lokalnie](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) lub na [platformie Azure](./automation-dsc-onboarding.md#enable-azure-vms) , aby przeprowadzić konfigurację ściągania.

Aby wypróbować PowerSTIG, odwiedź [Galeria programu PowerShell](https://www.powershellgallery.com) i Pobierz rozwiązanie lub kliknij pozycję "Witryna projektu", aby wyświetlić [dokumentację](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Następne kroki

- Aby poznać program PowerShell DSC, zobacz [Omówienie konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Dowiedz się więcej o zasobach DSC programu PowerShell w [zasobach DSC](/powershell/scripting/dsc/resources/resources).
- Aby uzyskać szczegółowe informacje na temat konfiguracji lokalnego Configuration Manager, zobacz [konfigurowanie Configuration Manager lokalnego](/powershell/scripting/dsc/managing-nodes/metaconfig).
