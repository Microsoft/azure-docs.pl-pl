---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2020
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 4eaea46044f0da4766fda051655d57caee9d98b6
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937023"
---
1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

1. Na stronie **Nowy** wybierz pozycję **obliczeniowe**  >  **aplikacja funkcji**.

1. Na stronie **podstawowe** Użyj ustawień aplikacji funkcji, zgodnie z opisem w poniższej tabeli:

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. |
    | **[Grupa zasobów](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. |
    | **Nazwa aplikacji funkcji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z` (bez uwzględniania wielkości liter), `0-9`i `-`.  |
    |**Publikowanie**| Kod | Opcja publikowania plików kodu lub kontenera Docker. |
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Wybierz **.NET** dla funkcji w językach C# i F#. |
    |**Region**| Preferowany region | Wybierz [region](https://azure.microsoft.com/regions/) znajdujący się w sąsiedztwie lub w najbliższej usłudze dostęp do funkcji. |

    ![Strona Podstawy](./media/functions-premium-create/function-app-create-basics.png)

1. Wybierz pozycję **Dalej: hosting**. Na stronie **hosting** wprowadź następujące ustawienia:

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Konto magazynu](../articles/storage/common/storage-account-create.md)** |  Nazwa unikatowa w skali globalnej |  Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również użyć istniejącego konta, które musi spełniać [wymagania dotyczące konta magazynu](../articles/azure-functions/storage-considerations.md#storage-account-requirements). |
    |**System operacyjny**| Preferowany system operacyjny | System operacyjny jest wstępnie wybrany na podstawie wybranego stosu środowiska uruchomieniowego, ale w razie potrzeby można zmienić to ustawienie. Język Python jest obsługiwany tylko w systemie Linux. |
    | **[Planowanie](../articles/azure-functions/functions-scale.md)** | Premium | Plan hostingu określający sposób przydzielania zasobów do aplikacji funkcji. Wybierz pozycję **Premium**. Domyślnie tworzony jest nowy plan App Service. Domyślną jednostką **SKU i rozmiarem** jest **EP1**, gdzie EP oznacza _elastyczną usługę Premium_. Aby dowiedzieć się więcej, zobacz [listę jednostek SKU w warstwie Premium](../articles/azure-functions/functions-premium-plan.md#available-instance-skus).<br/>Podczas uruchamiania funkcji JavaScript w planie Premium należy wybrać wystąpienie, które ma mniej procesorów wirtualnych vCPU. Aby uzyskać więcej informacji, zobacz [Wybierz pojedyncze podstawowe plany Premium](../articles/azure-functions/functions-reference-node.md#considerations-for-javascript-functions).  |

    ![Strona hostingu](./media/functions-premium-create/function-app-premium-create-hosting.png)

1. Wybierz kolejno pozycje **Dalej: monitorowanie**. Na stronie **monitorowanie** wprowadź następujące ustawienia:

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Domyślne | Tworzy zasób Application Insights o tej samej *nazwie aplikacji* w najbliższym obsługiwanym regionie. Rozszerzając to ustawienie, można zmienić **nazwę nowego zasobu** lub wybrać inną **lokalizację w lokalizacji** [geograficznej platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) do przechowywania danych. |

    ![Strona monitorowania](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć wybrane opcje konfiguracji aplikacji.

1. Na stronie **Recenzja i tworzenie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz** , aby udostępnić i wdrożyć aplikację funkcji.

1. Wybierz ikonę **powiadomienia** w prawym górnym rogu portalu i obejrzyj komunikat **wdrażanie zakończyło się pomyślnie** .

1. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji. Możesz również wybrać pozycję **Przypnij do pulpitu nawigacyjnego**. Przypinanie ułatwia powrót do tego zasobu aplikacji funkcji z pulpitu nawigacyjnego.

    ![Powiadomienie dotyczące wdrożenia](./media/functions-premium-create/function-app-create-notification2.png)
