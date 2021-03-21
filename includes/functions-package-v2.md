---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: bcc55156ca1d03614a4ff9767d6cf3f2603c06ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96027065"
---
Dodaj obsługę w preferowanym środowisku programistycznym, korzystając z następujących metod.

| Środowisko deweloperskie  | Typ aplikacji      | Aby dodać obsługę |
|--------------------------|-----------------------|----------------|
| Visual Studio            | Biblioteka klas języka C#      | [Zainstaluj pakiet NuGet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Na podstawie [podstawowych narzędzi](../articles/azure-functions/functions-run-local.md) | [Rejestrowanie pakietu rozszerzeń](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Zalecane jest zainstalowanie [rozszerzenia narzędzi platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) . |
| Każdy inny edytor/środowisko IDE     | Na podstawie [podstawowych narzędzi](../articles/azure-functions/functions-run-local.md) | [Rejestrowanie pakietu rozszerzeń](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure Portal             | Tylko w trybie online w portalu | Instalowane podczas dodawania powiązania<br /><br /> Zobacz [Aktualizowanie rozszerzeń](../articles/azure-functions/functions-bindings-register.md) , aby zaktualizować istniejące rozszerzenia powiązań bez konieczności ponownego publikowania aplikacji funkcji. |