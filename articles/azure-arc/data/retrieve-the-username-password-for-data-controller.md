---
title: Pobieranie nazwy użytkownika i hasła w celu nawiązania połączenia z kontrolerem danych Arc
description: Pobieranie nazwy użytkownika i hasła w celu nawiązania połączenia z kontrolerem danych Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 1c33cf21be0b4b1ea39a568d6df9fd90507dd454
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938956"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>Pobieranie nazwy użytkownika i hasła w celu nawiązania połączenia z kontrolerem danych Arc

Może wystąpić sytuacja, w której należy pobrać nazwę użytkownika i hasło dla kontrolera danych. Są to polecenia, które są potrzebne podczas uruchamiania programu. 

```console
azdata login
```

Jeśli jesteś administratorem usługi Kubernetes dla klastra. W związku z tym masz uprawnienia do uruchamiania poleceń do pobrania z Kubernetes Secret zapisuje informacje, które usługa Azure Arc utrwala w tym miejscu.

> [!NOTE]
>  Jeśli użyto innej nazwy dla przestrzeni nazw, w której został utworzony kontroler danych, należy zmienić `-n arc` parametr w poniższych poleceniach, aby użyć nazwy przestrzeni nazw, w której został utworzony kontroler danych.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="linux"></a>Linux

Uruchom następujące polecenie, aby pobrać nazwę użytkownika:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

Uruchom następujące polecenie, aby pobrać hasło:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

Uruchom następujące polecenie, aby pobrać nazwę użytkownika:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

Uruchom następujące polecenie, aby pobrać hasło:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>Następne kroki

Wypróbuj inne [scenariusze](https://github.com/microsoft/Azure-data-services-on-Azure-Arc/tree/master/scenarios)
