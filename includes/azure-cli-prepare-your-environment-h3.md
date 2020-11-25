---
ms.topic: include
ms.date: 09/10/2020
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.technology: azure-cli
ms.service: azure-cli
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 01e1f956270ebbc9b9d5d0ebdfdff52875cafedf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000060"
---
### <a name="prepare-your-environment-for-the-azure-cli"></a>Przygotowywanie środowiska dla interfejsu wiersza polecenia platformy Azure

- Skorzystaj z usługi [Azure Cloud Shell](../articles/cloud-shell/quickstart.md) za pośrednictwem środowiska bash.

   [![Uruchamianie osadzane](https://shell.azure.com/images/launchcloudshell.png "Uruchamianie usługi Azure Cloud Shell")](https://shell.azure.com)   
- Jeśli chcesz, [zainstaluj](/cli/azure/install-azure-cli) interfejs wiersza polecenia platformy Azure, aby móc uruchamiać polecenia referencyjne interfejsu CLI.
   - Jeśli korzystasz z instalacji lokalnej, zaloguj się za pomocą polecenia [az login](/cli/azure/reference-index#az-login) interfejsu wiersza polecenia platformy Azure.  Aby ukończyć proces uwierzytelniania, wykonaj kroki wyświetlane w terminalu.  Dodatkowe opcje logowania opisano w sekcji [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).
  - Po wyświetleniu monitu przy pierwszym użyciu zainstaluj rozszerzenia interfejsu wiersza polecenia platformy Azure.  Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [Korzystanie z rozszerzeń w interfejsie wiersza polecenia platformy Azure](/cli/azure/azure-cli-extensions-overview).
  - Uruchom polecenie [az version](/cli/azure/reference-index?#az_version), aby znaleźć zainstalowane wersje i biblioteki zależne. Aby uaktualnić do najnowszej wersji, uruchom polecenie [az upgrade](/cli/azure/reference-index?#az_upgrade).