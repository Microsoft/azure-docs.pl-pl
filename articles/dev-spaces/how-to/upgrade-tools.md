---
title: Jak uaktualnić narzędzia Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: Dowiedz się, jak uaktualnić Azure Dev Spaces narzędzia wiersza polecenia, rozszerzenia Visual Studio Code i rozszerzenia programu Visual Studio
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontenery
ms.openlocfilehash: 748ed4699591a21a5a09da924e093011b0813b46
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79265235"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Jak uaktualnić narzędzia Azure Dev Spaces

Jeśli istnieje nowa wersja i używasz już Azure Dev Spaces, może być konieczne uaktualnienie narzędzi klienta Azure Dev Spaces.

## <a name="update-the-azure-cli"></a>Aktualizowanie interfejsu wiersza polecenia platformy Azure

Podczas aktualizowania najnowszego interfejsu wiersza polecenia platformy Azure można również uzyskać najnowszą wersję rozszerzenia interfejsu wiersza polecenia dev Spaces.

Nie musisz odinstalować poprzedniej wersji. wystarczy znaleźć odpowiednie pobieranie w [interfejsie wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Aktualizowanie rozszerzenia i narzędzi wiersza polecenia funkcji Spaces dla deweloperów

Uruchom następujące polecenie:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Aktualizowanie rozszerzenia VS Code

Po zainstalowaniu rozszerzenia aktualizacje są aktualizowane automatycznie. Może być konieczne ponowne załadowanie rozszerzenia w celu korzystania z nowych funkcji. W VS Code Otwórz okienko **rozszerzenia** , wybierz rozszerzenia **Azure dev Spaces** i wybierz pozycję **Załaduj ponownie**.

## <a name="update-the-visual-studio-extension"></a>Aktualizowanie rozszerzenia programu Visual Studio

Podobnie jak w przypadku innych rozszerzeń i aktualizacji, program Visual Studio wyświetli powiadomienie, gdy dla Visual Studio Tools for Kubernetes jest dostępna aktualizacja obejmująca Azure Dev Spaces. Wyszukaj ikonę flagi w prawym górnym rogu ekranu.

Aby zaktualizować narzędzia w programie Visual Studio, wybierz element menu **narzędzia > rozszerzenia i aktualizacje** , a następnie po lewej stronie wybierz pozycję **aktualizacje**. Znajdź **Visual Studio Tools for Kubernetes** i kliknij przycisk **Aktualizuj** .

## <a name="next-steps"></a>Następne kroki

Przetestuj nowe narzędzia, tworząc nowy klaster. Wypróbuj Przewodniki Szybki Start i samouczki w [Azure dev Spaces](/azure/dev-spaces).
