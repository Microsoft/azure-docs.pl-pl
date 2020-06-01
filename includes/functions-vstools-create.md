---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4e56063891a92ef06e3e14651e26f0b73d280e79
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84231496"
---
Szablon projektu Azure Functions w programie Visual Studio tworzy projekt, który można opublikować w aplikacji funkcji na platformie Azure. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie, skalowanie i udostępnianie zasobów.

1. Z menu programu Visual Studio wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.

1. W obszarze **Utwórz nowy projekt**wprowadź *funkcje* w polu wyszukiwania, wybierz szablon **Azure Functions** , a następnie wybierz przycisk **dalej**.

1. W obszarze **Konfigurowanie nowego projektu**wprowadź **nazwę projektu** dla projektu, a następnie wybierz pozycję **Utwórz**. Nazwa aplikacji funkcji musi być prawidłową nazwą przestrzeni nazw C#, dlatego nie należy używać znaków podkreślenia, łączników ani znaków innych niż alfanumeryczne.

1. Aby **utworzyć nowe ustawienia aplikacji funkcji platformy Azure** , użyj wartości z poniższej tabeli:

    | Ustawienie      | Wartość  | Opis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Środowisko uruchomieniowe funkcji** | **Azure Functions v3 <br /> (.NET Core)** | Ta wartość tworzy projekt funkcji wykorzystujący środowisko uruchomieniowe w wersji 3. x Azure Functions, który obsługuje program .NET Core 3. x. Usługa Azure Functions 1.x obsługuje program .NET Framework. Aby uzyskać więcej informacji, zobacz [Omówienie wersji środowiska uruchomieniowego Azure Functions](../articles/azure-functions/functions-versions.md).   |
    | **Szablon funkcji** | **Wyzwalacz HTTP** | Ta wartość tworzy funkcję wyzwalaną przez żądanie HTTP. |
    | **Konto magazynu**  | **Emulator magazynu** | Ponieważ funkcja platformy Azure wymaga konta magazynu, jeden jest przypisywany lub tworzony podczas publikowania projektu na platformie Azure. Wyzwalacz HTTP nie korzysta z parametrów połączenia konta usługi Azure Storage; wszystkie inne typy wyzwalaczy wymagają prawidłowych parametrów połączenia z kontem usługi Azure Storage.  |
    | **Prawa dostępu** | **Anonimowe** | Utworzona funkcja może zostać wyzwolona przez dowolnego klienta bez podawania klucza. To ustawienie autoryzacji ułatwia testowanie nowej funkcji. Aby uzyskać więcej informacji na temat kluczy i autoryzacji, zobacz temat [klucze autoryzacji](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) i [powiązania protokołu HTTP i elementu webhook](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    
    ![Ustawienia projektu Azure Functions](./media/functions-vs-tools-create/functions-project-settings.png)

    Upewnij się, że ustawiono **prawa dostępu** **anonimowe**. Jeśli wybierzesz domyślny poziom **funkcji**, musisz przedstawić [klucz funkcji](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) w żądaniach, aby uzyskać dostęp do punktu końcowego funkcji.

1. Wybierz pozycję **Utwórz** , aby utworzyć projekt funkcji i funkcję wyzwalacza http.
