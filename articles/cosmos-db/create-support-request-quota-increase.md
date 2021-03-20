---
title: Jak zażądać zwiększenia przydziału dla zasobów Azure Cosmos DB
description: Dowiedz się, jak zażądać zwiększenia przydziału dla zasobów Azure Cosmos DB. Dowiesz się również, jak włączyć subskrypcję, aby uzyskać dostęp do regionu.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e7ec71220b75647e789508c760e50957b3b497fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090039"
---
# <a name="how-to-request-quota-increase-for-azure-cosmos-db-resources"></a>Jak zażądać zwiększenia przydziału dla zasobów Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Zasoby w Azure Cosmos DB mają [domyślne przydziały/limity](concepts-limits.md). Jednak może wystąpić sytuacja, w której obciążenie wymaga więcej przydziału niż wartość domyślna. W takim przypadku należy skontaktować się z zespołem Azure Cosmos DB, aby zażądać zwiększenia limitu przydziału. W tym artykule wyjaśniono, jak zażądać zwiększenia przydziału dla zasobów Azure Cosmos DB. Dowiesz się również, jak włączyć subskrypcję, aby uzyskać dostęp do regionu.

## <a name="create-a-new-support-request"></a>Utwórz nowe żądanie obsługi

Aby zażądać zwiększenia limitu przydziału, należy utworzyć nowe żądanie obsługi ze szczegółami obciążenia. Zespół Azure Cosmos DB będzie następnie oceni żądanie i zatwierdzić go. Wykonaj następujące kroki, aby utworzyć nowe żądanie obsługi z Azure Portal:

1. Zaloguj się do witryny Azure Portal.

1. W menu po lewej stronie wybierz pozycję **Pomoc i obsługa techniczna** , a następnie wybierz pozycję **nowe żądanie obsługi**.

1. Na karcie **podstawy** podaj następujące informacje:

   * W obszarze **Typ problemu** wybierz pozycję **Limity usługi i subskrypcji (limity przydziału)**
   * W polu **subskrypcja** wybierz subskrypcję, dla której chcesz zwiększyć przydział.
   * W obszarze **Typ limitu przydziału** wybierz pozycję **Cosmos DB**

   :::image type="content" source="./media/create-support-request-quota-increase/create-quota-increase-request.png" alt-text="Utwórz nowe żądanie obsługi Cosmos DB w celu zwiększenia limitu przydziału":::

1. Na karcie **szczegóły** wprowadź szczegóły dotyczące żądania limitu przydziału. Informacje podane na tej karcie będą służyć do dalszej oceny problemu i ułatwienia inżynierowi pomocy technicznej rozwiązywania problemu.

1. Wypełnij następujące szczegóły w tej postaci:

   * **Opis**: Podaj krótki opis żądania, taki jak obciążenie, dlaczego wartości domyślne nie są wystarczające. Na podstawie typu zasobu, dla którego ma zostać zwiększony przydział, wymagane jest podanie następujących informacji w polu **Opis** :

     **Żądania regionów** Jeśli żądanie jest zgodne z dodaniem regionu do listy dozwolonych, upewnij się, że zostały wprowadzone następujące wartości:

        * Nazwa regionu
        * Identyfikator subskrypcji

     **Żądania limitu przepływności** Jeśli Twoje żądanie jest zgodne z zwiększeniem przydziału przepływności, upewnij się, że zostały wprowadzone następujące wartości:

        * Nazwa bazy danych
        * Identyfikator subskrypcji
        * Nowy limit przepływności

     **Żądania limitu konta bazy danych** Jeśli Twoje żądanie odnosi się do zwiększenia limitu przydziału liczby kont bazy danych w ramach subskrypcji, należy podać następujące wartości:

       * Identyfikator subskrypcji
       * Limit konta nowej bazy danych

   * **Przekazywanie plików**: Przekaż pliki diagnostyczne lub wszystkie inne pliki, które Twoim zdaniem są odpowiednie dla żądania pomocy technicznej. Aby dowiedzieć się więcej na temat wskazówek dotyczących przekazywania plików, zobacz artykuł [pomocy technicznej systemu Azure]( ../azure-portal/supportability/how-to-manage-azure-support-request.md#upload-files) .

   * **Ważność**: Wybierz jeden z dostępnych poziomów ważności na podstawie wpływu na działalność biznesową.

   * **Preferowana metoda kontaktu**: możesz wybrać opcję komunikacji za pośrednictwem **poczty e-mail** lub **telefonicznie**.

1. Wypełnij pozostałe szczegóły, takie jak dostępność, język pomocy technicznej, informacje kontaktowe, adres e-mail i numer telefonu w formularzu.

1. Wybierz pozycję **Dalej: przegląd + Utwórz**. Sprawdź poprawność dostarczonych informacji i wybierz pozycję **Utwórz** , aby utworzyć żądanie obsługi.

W ciągu 24 godzin zespół pomocy technicznej Azure Cosmos DB oceni Twoje żądanie i skontaktuje się z nim.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [domyślnymi przydziałami usługi Azure Cosmos DB](concepts-limits.md)
