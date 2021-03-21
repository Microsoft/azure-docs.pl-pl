---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 164620bdcee7ac546468354f999dcb3ad96ecf4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84731039"
---
Szablon projektu Azure Functions w programie Visual Studio tworzy projekt, który można opublikować w aplikacji funkcji na platformie Azure. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie, skalowanie i udostępnianie zasobów.

1. Z menu programu Visual Studio wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.

1. W obszarze **Utwórz nowy projekt** wprowadź *funkcje* w polu wyszukiwania, wybierz szablon **Azure Functions** , a następnie wybierz przycisk **dalej**.

1. W obszarze **Konfigurowanie nowego projektu** wprowadź **nazwę projektu** dla projektu, a następnie wybierz pozycję **Utwórz**. Nazwa aplikacji funkcji musi być prawidłową nazwą przestrzeni nazw C#, dlatego nie należy używać znaków podkreślenia, łączników ani znaków innych niż alfanumeryczne.

1. Aby **utworzyć nowe ustawienia aplikacji Azure Functions** , użyj wartości z poniższej tabeli:

    | Ustawienie      | Wartość  | Opis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Środowisko uruchomieniowe funkcji** | **Azure Functions v3 <br /> (.NET Core)** | Ta wartość tworzy projekt funkcji wykorzystujący środowisko uruchomieniowe w wersji 3. x Azure Functions, który obsługuje program .NET Core 3. x. Usługa Azure Functions 1.x obsługuje program .NET Framework. Aby uzyskać więcej informacji, zobacz [Omówienie wersji środowiska uruchomieniowego Azure Functions](../articles/azure-functions/functions-versions.md).   |
    | **Szablon funkcji** | **Wyzwalacz HTTP** | Ta wartość tworzy funkcję wyzwalaną przez żądanie HTTP. |
    | **Konto magazynu (AzureWebJobsStorage)**  | **Emulator magazynu** | Ponieważ funkcja platformy Azure wymaga konta magazynu, jeden jest przypisywany lub tworzony podczas publikowania projektu na platformie Azure. Wyzwalacz HTTP nie korzysta z parametrów połączenia konta usługi Azure Storage; wszystkie inne typy wyzwalaczy wymagają prawidłowych parametrów połączenia z kontem usługi Azure Storage.  |
    | **Poziom autoryzacji** | **Anonimowe** | Utworzona funkcja może zostać wyzwolona przez dowolnego klienta bez podawania klucza. To ustawienie autoryzacji ułatwia testowanie nowej funkcji. Aby uzyskać więcej informacji na temat kluczy i autoryzacji, zobacz temat [klucze autoryzacji](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) i [powiązania protokołu HTTP i elementu webhook](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    
    ![Ustawienia projektu Azure Functions](./media/functions-vs-tools-create/functions-project-settings.png)

    Upewnij się, że **poziom autoryzacji** został ustawiony na wartość **anonimowe**. Jeśli wybierzesz domyślny poziom **funkcji**, musisz przedstawić [klucz funkcji](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) w żądaniach, aby uzyskać dostęp do punktu końcowego funkcji.

1. Wybierz pozycję **Utwórz** , aby utworzyć projekt funkcji i funkcję wyzwalacza http.
