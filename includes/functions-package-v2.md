---
title: plik dołączany
description: plik dołączany
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 2eb82eb8b4a64cbbfef91539f46ba084aa73f207
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77205746"
---
Dodaj obsługę w preferowanym środowisku programistycznym, korzystając z następujących metod.

| Środowisko deweloperskie  | Typ aplikacji      | Aby dodać obsługę |
|--------------------------|-----------------------|----------------|
| Visual Studio            | Biblioteka klas języka C#      | [Zainstaluj pakiet NuGet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Na podstawie [podstawowych narzędzi](../articles/azure-functions/functions-run-local.md) | [Rejestrowanie pakietu rozszerzeń](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Zalecane jest zainstalowanie [rozszerzenia narzędzi platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) . |
| Każdy inny edytor/środowisko IDE     | Na podstawie [podstawowych narzędzi](../articles/azure-functions/functions-run-local.md) | [Rejestrowanie pakietu rozszerzeń](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure Portal             | Tylko w trybie online w portalu | Instalowane podczas dodawania powiązania<br /><br /> Zobacz [Aktualizowanie rozszerzeń](../articles/azure-functions/install-update-binding-extensions-manual.md) , aby zaktualizować istniejące rozszerzenia powiązań bez konieczności ponownego publikowania aplikacji funkcji. |
