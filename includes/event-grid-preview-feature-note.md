---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 644669ea27938e385e11f3b1911a23ab30829a95
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026945"
---
Ta funkcja jest dostępna w wersji zapoznawczej. Aby go użyć, należy zainstalować rozszerzenie lub moduł podglądu.

### <a name="install-extension-for-azure-cli"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure

W przypadku interfejsu wiersza polecenia platformy Azure wymagane jest [rozszerzenie Event Grid](/cli/azure/azure-cli-extensions-list).

W [CloudShell](../articles/cloud-shell/quickstart.md):

* Jeśli wcześniej zainstalowano rozszerzenie, zaktualizuj je `az extension update -n eventgrid`
* Jeśli rozszerzenie nie zostało wcześniej zainstalowane, zainstaluj je `az extension add -n eventgrid`

W przypadku instalacji lokalnej:

1. [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Upewnij się, że masz najnowszą wersję, sprawdzając ją `az --version` .
1. Odinstaluj poprzednie wersje rozszerzenia `az extension remove -n eventgrid`
1. Zainstaluj `eventgrid` rozszerzenie za pomocą `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Zainstaluj moduł dla programu PowerShell

W przypadku programu PowerShell potrzebny jest [moduł AzureRM. EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

W [CloudShell](../articles/cloud-shell/quickstart-powershell.md):

* Instalowanie modułu `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

W przypadku instalacji lokalnej:

1. Otwórz konsolę programu PowerShell jako administrator
1. Instalowanie modułu `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Jeśli `-AllowPrerelease` parametr nie jest dostępny, wykonaj następujące czynności:

1. Uruchom polecenie `Install-Module PowerShellGet -Force`
1. Uruchom polecenie `Update-Module PowerShellGet`
1. Zamknij konsolę programu PowerShell
1. Uruchom ponownie program PowerShell jako administrator
1. Instalowanie modułu `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`