---
title: Tworzenie Apache Kafka dla rozwiązań partnerskich dla partnerów opartych na platformie Azure
description: W tym artykule opisano, jak za pomocą Azure Portal utworzyć wystąpienie Apache Kafka dla usługi dla chmury.
ms.service: partner-services
ms.topic: quickstart
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f4c6dacf63b1be44e826fe6841c87ccec4bf9b1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98253777"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud"></a>Szybki Start: Rozpoczynanie pracy z usługą Apache Kafka dla chmury

W tym przewodniku szybki start użyjesz Azure Portal, aby utworzyć wystąpienie Apache Kafka dla usługi w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli nie masz aktywnej subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- Musisz mieć rolę _właściciela_ lub _współautora_ dla Twojej subskrypcji platformy Azure. Integrację między platformą Azure i usługą prefluent można skonfigurować tylko przez użytkowników z dostępem _właściciela_ lub _współautora_ . Przed rozpoczęciem upewnij się, [że masz odpowiedni dostęp](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>Znajdź ofertę

Użyj Azure Portal, aby znaleźć Apache Kafka aplikacji w chmurze.

1. W przeglądarce internetowej przejdź do [Azure Portal](https://portal.azure.com/) i zaloguj się.

1. Jeśli w ostatniej sesji odwiedzono Portal **Marketplace** , wybierz ikonę z dostępnych opcji. W przeciwnym razie wyszukaj _witrynę Marketplace_.

    :::image type="content" source="media/marketplace.png" alt-text="Ikona witryny Marketplace.":::

1. Na stronie **portalu Marketplace** dostępne są dwie opcje oparte na żądanym typie planu. Możesz utworzyć konto w ramach planu płatność zgodnie z rzeczywistym użyciem lub planu zobowiązania. Płatność zgodnie z rzeczywistym użyciem jest ogólnie dostępna. Plan zobowiązania jest dostępny dla klientów, którzy zostali zazatwierdzeni do oferty prywatnej.

   - W przypadku klientów korzystających **z usługi płatność zgodnie z rzeczywistym** użyciem Wyszukaj _Apache Kafka w chmurze_. Wybierz ofertę Apache Kafka w chmurze z zaświadczeniem.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="Wyszukaj ofertę w witrynie Azure Marketplace.":::

   - W przypadku klientów z **zobowiązaniami** wybierz link, aby **wyświetlić oferty prywatne**. Zobowiązanie wymaga zarejestrowania się w celu uzyskania minimalnej ilości wydatków. Tej opcji należy używać tylko wtedy, gdy wiadomo, że usługa jest potrzebna przez dłuższy czas.

     :::image type="content" source="media/view-private-offers.png" alt-text="Wyświetlanie ofert prywatnych.":::

     Poszukaj _Apache Kafka w chmurze z_ systemem.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="Wybierz pozycję oferta prywatna.":::

## <a name="create-resource"></a>Tworzenie zasobu

Po wybraniu oferty Apache Kafka w chmurze z obsługą aplikacji możesz skonfigurować aplikację.

1. W przypadku wybrania opcji prywatne oferty w poprzedniej sekcji dostępne są dwie opcje typów planów:

    - Twoja chmura — płatność zgodnie z rzeczywistym użyciem
    - Zobowiązanie — w przypadku planu zatwierdzania

   Jeśli nie wybrano ofert prywatnych, dostępna będzie tylko opcja płatność zgodnie z rzeczywistym użyciem.

   Wybierz plan do użycia i wybierz pozycję **Skonfiguruj + Subskrybuj**.

    :::image type="content" source="media/setup-subscribe.png" alt-text="Skonfiguruj i Subskrybuj.":::

1. Na stronie Tworzenie podstawowe informacje o **zasobach w chmurze** podaj następujące wartości. Po zakończeniu wybierz pozycję **Dalej: Tagi**.

    :::image type="content" source="media/setup-basics.png" alt-text="Formularz umożliwiający skonfigurowanie zasobu chmury.":::

    | Właściwość | Opis |
    | ---- | ---- |
    | **Subskrypcja** | Z menu rozwijanego wybierz subskrypcję platformy Azure, która ma zostać wdrożona. Musisz mieć dostęp _właściciela_ lub _współautora_ . |
    | **Grupa zasobów** | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy zasobów. Grupa zasobów to kontener zawierający powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md). |
    | **Nazwa organizacji** | Nazwij zasób oprogramowanie jako usługa (SaaS). |
    | **Region** | Z menu rozwijanego wybierz jeden z następujących regionów: <br/><br/> Australia Wschodnia, Kanada środkowa, środkowe stany USA, Wschodnie stany USA, Wschodnie stany USA 2, Francja środkowa, Europa Północna, Azja Południowo-Wschodnia, Południowe Zjednoczone Królestwo, zachodnio-środkowe stany USA, Europa Zachodnia, zachodnie stany USA 2 |
    | **Planowanie** | Wybierz pozycję **płatność zgodnie z rzeczywistym** użyciem lub **zobowiązanie**. |
    | **Okres rozliczeniowy** | Wstępnie wypełnione na podstawie wybranego planu rozliczeniowego. |
    | **Cena** | Wstępnie wypełnione na podstawie wybranego planu. |

1. W obszarze **Tagi** Podaj **nazwę** i pary **wartości** dla tagów, które mają zostać zastosowane do zasobu. Po wprowadzeniu tagów wybierz pozycję **Recenzja + Utwórz**.

    :::image type="content" source="media/setup-tags.png" alt-text="Dodaj Tagi projektu.":::

1. Przejrzyj podane ustawienia. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Utworzenie zasobu może potrwać kilka minut. Stan wdrożenia można wyświetlić w obszarze **powiadomienia**. Po zakończeniu wdrażania wybierz zasób, aby wyświetlić stronę **Przegląd** .

    :::image type="content" source="media/deployment-status.png" alt-text="Stan wdrożenia.":::

   Jeśli wystąpi błąd, zobacz [Rozwiązywanie problemów Apache Kafka na potrzeby rozwiązań w chmurze](troubleshoot.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie zasobem chmury](manage.md)
