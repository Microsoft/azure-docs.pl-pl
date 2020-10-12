---
title: Konwertuj konfiguracje na zasoby złożone na potrzeby konfiguracji stanu Azure Automation
description: W tym artykule opisano sposób konwertowania konfiguracji na zasoby złożone na potrzeby konfiguracji stanu Azure Automation.
keywords: DSC, PowerShell, konfiguracja, instalacja
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8c834caa2285135b7d39c440489b42c366418042
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186473"
---
# <a name="convert-configurations-to-composite-resources"></a>Konwertowanie konfiguracji do zasobów złożonych

> Dotyczy: Windows PowerShell 5,1

Po rozpoczęciu tworzenia konfiguracji można szybko utworzyć "scenariusze" służące do zarządzania grupami ustawień.
Oto przykłady:

- Tworzenie serwera sieci Web
- Tworzenie serwera DNS
- Tworzenie serwera programu SharePoint
- Konfigurowanie klastra SQL
- Zarządzanie ustawieniami zapory
- Zarządzanie ustawieniami hasła

Jeśli interesuje Cię udostępnianie tej pracy innym osobom, najlepszym rozwiązaniem jest spakowanie konfiguracji jako [zasobu złożonego](/powershell/scripting/dsc/resources/authoringresourcecomposite).
Tworzenie zasobów złożonych po raz pierwszy może być przeciążone.

> [!NOTE]
> Ten artykuł odnosi się do rozwiązania, które jest obsługiwane przez społeczność typu open source.
> Pomoc techniczna jest dostępna tylko w formie współpracy GitHub, a nie od firmy Microsoft.

## <a name="community-project-compositeresource"></a>Projekt społeczności: CompositeResource

Zostało utworzone rozwiązanie obsługiwane przez społeczność o nazwie [CompositeResource](https://github.com/microsoft/compositeresource) , aby rozwiązać ten problem.

CompositeResource automatyzuje proces tworzenia nowego modułu na podstawie konfiguracji.
Rozpoczynając od [kropki](https://devblogs.microsoft.com/scripting/how-to-reuse-windows-powershell-functions-in-scripts/) pozyskuje skrypt konfiguracji na stacji roboczej (lub serwerze kompilacji), tak aby został załadowany w pamięci.
Następnie zamiast uruchamiać konfigurację w celu wygenerowania pliku MOF, użyj funkcji dostarczonej przez moduł CompositeResource w celu zautomatyzowania konwersji.
Polecenie cmdlet spowoduje załadowanie zawartości konfiguracji, uzyskanie listy parametrów i wygenerowanie nowego modułu ze wszystkimi potrzebnymi elementami.

Po wygenerowaniu modułu można zwiększyć wersję i dodać informacje o wersji za każdym razem, gdy wprowadzisz zmiany i opublikujesz je w twoim własnym [repozytorium PowerShellGet](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

Po utworzeniu złożonego modułu zasobów z konfiguracją (lub wieloma konfiguracjami) można używać ich w ramach [tworzenia](./compose-configurationwithcompositeresources.md) i przekazywania plików MOF do [skryptów konfiguracji DSC](/powershell/scripting/dsc/configurations/configurations) w celu wygenerowania plików MOF i [przekazania ich do Azure Automation](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Następnie zarejestruj serwery [lokalnie](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) lub na [platformie Azure](./automation-dsc-onboarding.md#enable-azure-vms) , aby przeprowadzić konfigurację ściągania.
Najnowsza aktualizacja projektu opublikowała również [elementy Runbook](https://www.powershellgallery.com/packages?q=DscGallerySamples) dla Azure Automation w celu zautomatyzowania procesu importowania konfiguracji z Galeria programu PowerShell.

Aby wypróbować automatyzację tworzenia zasobów złożonych dla DSC, odwiedź [Galeria programu PowerShell](https://www.powershellgallery.com/packages/compositeresource/) i Pobierz rozwiązanie lub kliknij pozycję "Witryna projektu", aby wyświetlić [dokumentację](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Następne kroki

- Aby poznać program PowerShell DSC, zobacz [Omówienie konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Dowiedz się więcej o zasobach DSC programu PowerShell w [zasobach DSC](/powershell/scripting/dsc/resources/resources).
- Aby uzyskać szczegółowe informacje na temat konfiguracji lokalnego Configuration Manager, zobacz [konfigurowanie Configuration Manager lokalnego](/powershell/scripting/dsc/managing-nodes/metaconfig).
