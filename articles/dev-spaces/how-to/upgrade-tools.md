---
title: Jak uaktualnić narzędzia Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
ms.custom: devx-track-azurecli
description: Dowiedz się, jak uaktualnić Azure Dev Spaces narzędzia wiersza polecenia, rozszerzenia Visual Studio Code i rozszerzenia programu Visual Studio
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontenery
ms.openlocfilehash: aebe81d58a1bcd44f5766d368dbafa53c81ed2b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87504334"
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

## <a name="update-visual-studio"></a>Aktualizowanie programu Visual Studio

Azure Dev Spaces jest częścią obciążeń programistycznych platformy Azure i jest uwzględniony we wszystkich aktualizacjach programu Visual Studio.

## <a name="next-steps"></a>Następne kroki

Przetestuj nowe narzędzia, tworząc nowy klaster. Wypróbuj Przewodniki Szybki Start i samouczki w [Azure dev Spaces](../index.yml).
