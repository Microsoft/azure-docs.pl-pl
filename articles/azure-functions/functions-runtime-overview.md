---
title: Przegląd środowisko uruchomieniowe usługi Azure Functions
description: Przegląd środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 61e3b82e497afcdc8239a9f4fda3e4f739166a1f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92108443"
---
# <a name="azure-functions-runtime-overview-preview"></a>Przegląd środowisko uruchomieniowe usługi Azure Functions (wersja zapoznawcza)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Środowisko uruchomieniowe usługi Azure Functions (wersja zapoznawcza) to nowy sposób, dzięki któremu można wykorzystać prostotę i elastyczność modelu programowania Azure Functions lokalnie. Utworzone na tych samych korzeniach typu "open source" jako Azure Functions, środowisko uruchomieniowe usługi Azure Functions wdrażane lokalnie, aby zapewnić niemal identyczne środowisko programistyczne jako usługę w chmurze.

![Portal środowisko uruchomieniowe usługi Azure Functions w wersji zapoznawczej][1]

Środowisko uruchomieniowe usługi Azure Functions zapewnia sposób Azure Functions przed zatwierdzeniem do chmury. W ten sposób skompilowane zasoby kodu można następnie wykonać w chmurze podczas migracji.  Środowisko uruchomieniowe otwiera również nowe opcje, takie jak użycie zapasowej mocy obliczeniowej na komputerach lokalnych do uruchamiania procesów wsadowych w nadporze. Możesz również użyć urządzeń w organizacji, aby warunkowo wysyłać dane do innych systemów, zarówno lokalnie, jak i w chmurze.

Środowisko uruchomieniowe usługi Azure Functions składa się z dwóch części:

* Rola zarządzania środowisko uruchomieniowe usługi Azure Functions
* środowisko uruchomieniowe usługi Azure Functions rolę procesu roboczego

## <a name="azure-functions-management-role"></a>Rola zarządzania Azure Functions

Rola zarządzanie Azure Functions zapewnia Host do zarządzania funkcjami lokalnymi. Ta rola wykonuje następujące zadania:

* Hosting portal zarządzania Azure Functions, który jest taki sam, jak w [Azure Portal](https://portal.azure.com). Portal zapewnia spójne środowisko, które umożliwia tworzenie funkcji w taki sam sposób jak w Azure Portal.
* Dystrybuowanie funkcji między pracownikami wielu funkcji.
* Udostępnianie punktu końcowego publikowania, aby można było opublikować funkcje bezpośrednio z Microsoft Visual Studio przez pobranie i zaimportowanie profilu publikowania.

## <a name="azure-functions-worker-role"></a>Azure Functions rolę procesu roboczego

Role procesu roboczego Azure Functions są wdrażane w kontenerach systemu Windows i są wykonywane w ramach kodu funkcji.  W całej organizacji można wdrożyć wiele ról procesów roboczych. Ta opcja stanowi kluczowy sposób, w jaki klienci mogą korzystać z mocy obliczeniowej zapasowej.  Przykładem, gdzie istnieją obliczenia zapasowe w wielu organizacjach, jest to, że maszyny są zasilane ciągle, ale nie są używane przez długi czas.

## <a name="minimum-requirements"></a>Minimalne wymagania

Aby rozpocząć pracę z środowisko uruchomieniowe usługi Azure Functions, musisz mieć komputer z systemem Windows Server 2016 lub Windows 10 Creators Update z dostępem do wystąpienia SQL Server.

## <a name="next-steps"></a>Następne kroki

Zainstaluj wersję [zapoznawczą środowisko uruchomieniowe usługi Azure Functions](./functions-runtime-install.md)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png