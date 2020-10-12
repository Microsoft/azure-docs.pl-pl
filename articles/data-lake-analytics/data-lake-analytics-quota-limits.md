---
title: Dostosowywanie przydziałów i limitów w usłudze Azure Data Lake Analytics
description: Dowiedz się, jak dostosować i zwiększyć limity przydziałów i limitów na kontach Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: how-to
ms.date: 03/15/2018
ms.openlocfilehash: 0025e35f516543c8fe703daa647ca29ed3fb87e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87127591"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Dostosowywanie przydziałów i limitów w usłudze Azure Data Lake Analytics

Dowiedz się, jak dostosować i zwiększyć limit przydziału oraz limity na kontach Azure Data Lake Analytics (ADLA). Znajomość tych limitów pomoże Ci zrozumieć zachowanie zadania U-SQL. Wszystkie limity przydziałów są nietrwałe, dzięki czemu można zwiększyć maksymalną liczbę limitów, kontaktując się z pomocą techniczną platformy Azure.

## <a name="azure-subscriptions-limits"></a>Limity subskrypcji platformy Azure

**Maksymalna liczba kont ADLA na subskrypcję na region:**  5

Jeśli spróbujesz utworzyć szóste konto ADLA, zostanie wyświetlony komunikat o błędzie "Osiągnięto maksymalną dozwoloną liczbę kont Data Lake Analytics (5) w regionie pod nazwą subskrypcji".

Jeśli chcesz wykraczać poza ten limit, możesz wypróbować następujące opcje:
* Wybierz inny region, jeśli jest to odpowiednie
* Skontaktuj się z pomocą techniczną platformy Azure, [otwierając bilet pomocy technicznej](#increase-maximum-quota-limits) , aby zażądać zwiększenia limitu przydziału.

## <a name="default-adla-account-limits"></a>Domyślne limity kont ADLA

**Maksymalna liczba jednostek analizy (Australia) dla konta:** 250, domyślnie 32

Jest to maksymalna liczba jednostek, które mogą być uruchamiane współbieżnie na koncie. Jeśli łączna liczba uruchomionych jednostek w ramach wszystkich zadań przekracza ten limit, nowsze zadania są umieszczane w kolejce automatycznie. Na przykład:

* Jeśli masz tylko jedno zadanie działające z 32, podczas przesyłania drugiego zadania zostanie ono zaczekać w kolejce zadań do momentu zakończenia pierwszego zadania.
* Jeśli masz już cztery zadania, a każdy z nich korzysta z 8 jednostek zadanich, po przesłaniu piątego zadania wymagającego 8 jednostek, które zaczekają w kolejce zadań do momentu, gdy zostanie wydana 8 dostępnych jednostek.

    ![Strona limity Azure Data Lake Analytics i limit przydziału](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Maksymalna liczba jednostek analizy (Australia) na zadanie:** 250, domyślnie 32

Jest to maksymalna liczba jednostek, które mogą być przypisane do poszczególnych zadań w ramach konta. Zadania, do których przypisano więcej niż ten limit, będą odrzucane, chyba że ma to wpływ na zasadę obliczeniową (limit przesłania zadania), która daje im więcej informacji na temat poszczególnych zadań. Górna granica tej wartości jest limitem aktualizacji dla konta.

**Maksymalna liczba jednoczesnych zadań U-SQL na konto:** 20

Jest to maksymalna liczba zadań, które mogą być uruchamiane współbieżnie na koncie. Powyżej tej wartości nowsze zadania są umieszczane w kolejce automatycznie.

## <a name="adjust-adla-account-limits"></a>Dostosuj limity kont ADLA

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz istniejące konto ADLA.
3. Kliknij pozycję **Właściwości**.
4. Dostosuj wartości **maksymalnego**punktu, **maksymalnej liczby uruchomionych zadań**i **limitów przesłania zadań** do własnych potrzeb.

## <a name="increase-maximum-quota-limits"></a>Zwiększ maksymalne limity przydziału

Więcej informacji na temat limitów platformy Azure można znaleźć w [dokumentacji ograniczeń dotyczących usługi platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Otwórz żądanie obsługi w Azure Portal.

    ![Strona portalu Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Strona portalu Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Wybierz **limit przydziału**typu problemu.
3. Wybierz swoją **subskrypcję** (Upewnij się, że nie jest to subskrypcja wersji próbnej).
4. Wybierz typ przydziału **Data Lake Analytics**.

    ![Strona portalu Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Na stronie problem Wyjaśnij żądany limit wzrostu ze **szczegółowymi informacjami** o tym, dlaczego potrzebujesz dodatkowej pojemności.

    ![Strona portalu Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Zweryfikuj swoje informacje kontaktowe i Utwórz żądanie pomocy technicznej.

Firma Microsoft przegląda Twoje żądanie i próbuje odpowiednio dopasować potrzeby biznesowe.

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitorowanie zadań usługi Azure Data Lake Analytics i rozwiązywanie problemów przy użyciu witryny Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
