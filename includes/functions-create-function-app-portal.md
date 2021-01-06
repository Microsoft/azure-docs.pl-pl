---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 754ca10e72ca2274607e954748bfa8cb5286c6ab
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97954530"
---
1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

1. Na stronie **Nowy** wybierz pozycję **obliczeniowe**  >  **aplikacja funkcji**.

1. Na stronie **podstawowe** Użyj ustawień aplikacji funkcji, zgodnie z opisem w poniższej tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. |
    | **[Grupa zasobów](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. |
    | **Nazwa aplikacji funkcji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z` (bez uwzględniania wielkości liter), `0-9`i `-`.  |
    |**Publikowanie**| Kod | Opcja publikowania plików kodu lub kontenera Docker. |
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Wybierz pozycję **.NET Core** dla języków C# i F #. |
    |**Wersja**| Numer wersji | Wybierz wersję zainstalowanego środowiska uruchomieniowego.  |
    |**Region**| Preferowany region | Wybierz [region](https://azure.microsoft.com/regions/) znajdujący się w sąsiedztwie lub w najbliższej usłudze dostęp do funkcji. |

    ![Podstawy](./media/functions-create-function-app-portal/function-app-create-basics.png)

1. Wybierz pozycję **Dalej: Hosting**. Na stronie **hosting** wprowadź następujące ustawienia.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Konto magazynu](../articles/storage/common/storage-account-create.md)** |  Nazwa unikatowa w skali globalnej |  Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont magazynu muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również użyć istniejącego konta, które musi spełniać [wymagania dotyczące konta magazynu](../articles/azure-functions/storage-considerations.md#storage-account-requirements). |
    |**System operacyjny**| Preferowany system operacyjny | System operacyjny jest wstępnie wybrany na podstawie wybranego stosu środowiska uruchomieniowego, ale w razie potrzeby można zmienić to ustawienie. |
    | **[Planowanie](../articles/azure-functions/functions-scale.md)** | **Zużycie (bezserwerowe)** | Plan hostingu określający sposób przydzielania zasobów do aplikacji funkcji. W domyślnym planie **Zużycie** zasoby są dodawane dynamicznie zgodnie z wymaganiami funkcji. W tym przypadku hostingu [bezserwerowego](https://azure.microsoft.com/overview/serverless-computing/) płacisz tylko za czas wykonywania funkcji. W przypadku uruchomienia w ramach planu usługi App Service musisz zarządzać [skalowaniem aplikacji funkcji](../articles/azure-functions/functions-scale.md).  |

    ![Hosting](./media/functions-create-function-app-portal/function-app-create-hosting.png)

1. Wybierz kolejno pozycje **Dalej: monitorowanie**. Na stronie **monitorowanie** wprowadź następujące ustawienia.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Domyślne | Tworzy zasób Application Insights o tej samej *nazwie aplikacji* w najbliższym obsługiwanym regionie. Rozszerzając to ustawienie lub wybierając pozycję **Utwórz nowe**, możesz zmienić nazwę Application Insights lub wybrać inny region w obszarze [geograficznym platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , w którym chcesz przechowywać dane. |

    ![Monitorowanie](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć wybrane opcje konfiguracji aplikacji.

1. Na stronie **Recenzja i tworzenie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz** , aby udostępnić i wdrożyć aplikację funkcji.

1. Wybierz ikonę **powiadomienia** w prawym górnym rogu portalu i obejrzyj komunikat **wdrażanie zakończyło się pomyślnie** .

1. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji. Możesz również wybrać pozycję **Przypnij do pulpitu nawigacyjnego**. Przypinanie ułatwia powrót do tego zasobu aplikacji funkcji z pulpitu nawigacyjnego.

    ![Powiadomienie dotyczące wdrożenia](./media/functions-create-function-app-portal/function-app-create-notification2.png)