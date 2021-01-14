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
ms.openlocfilehash: cdeba09c91bee72508632b8468126150017df7cd
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98186980"
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
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Edytowanie w portalu nie jest obecnie obsługiwane w przypadku programowania w języku [Python](../articles/azure-functions/functions-reference-python.md).|
    |**Region**| Preferowany region | Wybierz [region](https://azure.microsoft.com/regions/) znajdujący się w sąsiedztwie lub w najbliższej usłudze dostęp do funkcji. |

1. Wybierz pozycję **Dalej: hosting**. Na stronie **hosting** wprowadź następujące ustawienia:

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Konto magazynu](../articles/storage/common/storage-account-create.md)** |  Nazwa unikatowa w skali globalnej |  Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również użyć istniejącego konta, które musi spełniać [wymagania dotyczące konta magazynu](../articles/azure-functions/storage-considerations.md#storage-account-requirements). |
    |**System operacyjny**| Preferowany system operacyjny | System operacyjny jest wstępnie wybrany na podstawie wybranego stosu środowiska uruchomieniowego, ale w razie potrzeby można zmienić to ustawienie. Język Python jest obsługiwany tylko w systemie Linux. Edycja w portalu jest obsługiwana tylko w systemie Windows.|
    | **[Planowanie](../articles/azure-functions/functions-scale.md)** | Premium | Plan hostingu określający sposób przydzielania zasobów do aplikacji funkcji. Wybierz pozycję **Premium**. Domyślnie tworzony jest nowy plan App Service. Domyślną jednostką **SKU i rozmiarem** jest **EP1**, gdzie EP oznacza _elastyczną usługę Premium_. Aby dowiedzieć się więcej, zobacz [listę jednostek SKU w warstwie Premium](../articles/azure-functions/functions-premium-plan.md#available-instance-skus).<br/>Podczas uruchamiania funkcji JavaScript w planie Premium należy wybrać wystąpienie, które ma mniej procesorów wirtualnych vCPU. Aby uzyskać więcej informacji, zobacz [Wybierz pojedyncze podstawowe plany Premium](../articles/azure-functions/functions-reference-node.md#considerations-for-javascript-functions).  |

1. Wybierz kolejno pozycje **Dalej: monitorowanie**. Na stronie **monitorowanie** wprowadź następujące ustawienia:

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Domyślny | Tworzy zasób Application Insights o tej samej *nazwie aplikacji* w najbliższym obsługiwanym regionie. Rozszerzając to ustawienie, można zmienić **nazwę nowego zasobu** lub wybrać inną **lokalizację w lokalizacji** [geograficznej platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) do przechowywania danych. |

1. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć wybrane opcje konfiguracji aplikacji.

1. Na stronie **Recenzja i tworzenie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz** , aby udostępnić i wdrożyć aplikację funkcji.

1. Wybierz ikonę **powiadomienia** w prawym górnym rogu portalu i obejrzyj komunikat **wdrażanie zakończyło się pomyślnie** .

1. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji. Możesz również wybrać pozycję **Przypnij do pulpitu nawigacyjnego**. Przypinanie ułatwia powrót do tego zasobu aplikacji funkcji z pulpitu nawigacyjnego.

    ![Powiadomienie dotyczące wdrożenia](./media/functions-premium-create/function-app-create-notification2.png)
