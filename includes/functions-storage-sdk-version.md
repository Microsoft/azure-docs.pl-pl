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
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77198426"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Wersja zestawu SDK usługi Azure Storage w funkcjach 1. x

W funkcjach 1. x wyzwalacze magazynu i powiązania używają wersji 7.2.1 zestawu SDK usługi Azure Storage (pakiet NuGet[windowsazure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) ). Jeśli odwołujesz się do innej wersji zestawu SDK magazynu i utworzysz powiązanie z typem zestawu SDK magazynu w podpisie funkcji, środowisko uruchomieniowe funkcji może zgłosić, że nie można powiązać z tym typem. Rozwiązaniem jest upewnienie się, że projekt odwołuje się do [windowsazure. Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
