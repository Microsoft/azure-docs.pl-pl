---
title: Jak utworzyć żądanie pomocy technicznej dla platformy Azure
description: Klienci, którzy potrzebują pomocy, mogą korzystać z Azure Portal, aby znaleźć rozwiązania samoobsługowe i tworzyć żądania pomocy technicznej i zarządzać nimi.
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.topic: how-to
ms.date: 06/25/2020
ms.openlocfilehash: 71aba8f2769a75805f47d90b71a8db4eeb26a97a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652398"
---
# <a name="create-an-azure-support-request"></a>Tworzenie wniosku o pomoc techniczną platformy Azure

System Azure umożliwia tworzenie i zarządzanie żądaniami pomocy technicznej, nazywanymi również biletami pomocy technicznej. Można tworzyć i zarządzać żądaniami w [Azure Portal](https://portal.azure.com), które zostały omówione w tym artykule. Żądania można także tworzyć programowo i zarządzać nimi przy użyciu [interfejsu API REST biletów pomocy technicznej platformy Azure](/rest/api/support)lub przy użyciu [wiersza polecenia platformy Azure](/cli/azure/azure-cli-support-request).

> [!NOTE]
> Adres URL Azure Portal jest specyficzny dla chmury platformy Azure, w której wdrożono organizację.
>
>* Azure Portal do użytku komercyjnego: [https://portal.azure.com](https://portal.azure.com)
>* Azure Portal dla Niemiec: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Azure Portal dla instytucji rządowych Stany Zjednoczone: [https://portal.azure.us](https://portal.azure.us)

Środowisko żądania pomocy technicznej koncentruje się na trzech głównych celach:

* **Usprawnione**: zapewnianie pomocy technicznej i rozwiązywania problemów łatwo znaleźć i Uprość sposób przesyłania żądania pomocy technicznej.
* **Zintegrowane**: można łatwo otworzyć żądanie pomocy technicznej w przypadku rozwiązywania problemu z zasobem platformy Azure bez przełączania kontekstu.
* **Wydajne**: Zbierz najważniejsze informacje potrzebne inżynierowi pomocy technicznej w celu efektywnego rozwiązania problemu.

Platforma Azure zapewnia nieograniczoną obsługę zarządzania subskrypcjami, w tym rozliczeniami, korektami przydziałów i transferami kont. Aby uzyskać pomoc techniczną, musisz mieć plan pomocy technicznej. Aby uzyskać więcej informacji, zobacz [Porównanie planów pomocy technicznej](https://azure.microsoft.com/support/plans).

## <a name="getting-started"></a>Wprowadzenie

Możesz uzyskać **Pomoc techniczną i wsparcie** w Azure Portal. Jest ona dostępna z menu Azure Portal, nagłówka globalnego lub menu zasobów dla usługi. Aby można było wysłać żądanie pomocy technicznej, musisz mieć odpowiednie uprawnienia.

### <a name="azure-role-based-access-control"></a>Kontrola dostępu na podstawie ról na platformie Azure

Aby utworzyć żądanie pomocy technicznej, musisz być [właścicielem](../../role-based-access-control/built-in-roles.md#owner), [współautorem](../../role-based-access-control/built-in-roles.md#contributor) lub przypisać do roli [współautor żądania pomocy technicznej](../../role-based-access-control/built-in-roles.md#support-request-contributor) na poziomie subskrypcji. Aby utworzyć żądanie pomocy technicznej bez subskrypcji, na przykład w scenariuszu Azure Active Directory, musisz być [administratorem](../../active-directory/roles/permissions-reference.md).

### <a name="go-to-help--support-from-the-global-header"></a>Przejdź do pomocy i obsługi technicznej z nagłówka globalnego

Aby uruchomić żądanie pomocy technicznej z dowolnego miejsca w Azure Portal:

1. Wybierz pozycję **?** w nagłówku globalnym. Następnie wybierz pozycję **Pomoc i obsługa techniczna**.

   ![Pomoc i obsługa techniczna](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

1. Wybierz pozycję **Nowe żądanie obsługi**. Postępuj zgodnie z monitami, aby podać informacje o problemie. Zasugerujemy niektóre możliwe rozwiązania, zebranie szczegółowych informacji o problemie i przesłanie i śledzenie żądania pomocy technicznej.

   ![Nowe żądanie pomocy technicznej](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Przejdź do pomocy i obsługi technicznej z menu zasobów

Aby uruchomić żądanie obsługi w kontekście zasobu, obecnie pracujesz z:

1. Z menu zasób w sekcji **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **nowe żądanie obsługi**.

   ![W kontekście](./media/how-to-create-azure-support-request/incontext2lower.png)

1. Postępuj zgodnie z monitami, aby podać nam informacje o występującym problemie. Po uruchomieniu procesu żądania obsługi z zasobu niektóre opcje są wstępnie wybrane dla Ciebie.

## <a name="create-a-support-request"></a>Tworzenie żądania obsługi

Przeprowadzimy Cię przez kilka kroków, aby zebrać informacje o problemie i pomóc w jego rozwiązaniu. Każdy krok został opisany w poniższych sekcjach.

### <a name="basics"></a>Podstawy

Pierwszy krok procesu żądania pomocy technicznej gromadzi podstawowe informacje o problemie i planie pomocy technicznej.

Na karcie **podstawy** **nowego żądania obsługi** Użyj selektorów, aby zacząć powiedzieć nam o problemie. Najpierw określisz niektóre ogólne kategorie dla typu problemu i wybierz powiązaną subskrypcję. Wybierz usługę, na przykład **maszynę wirtualną z systemem Windows**. Wybierz zasób, taki jak nazwa maszyny wirtualnej. Opisz problem we własnych słowach, a następnie wybierz **typ problemu** i **podtyp problemu** , aby uzyskać bardziej szczegółowe informacje.

![Blok Podstawowe](./media/how-to-create-azure-support-request/basics2lower.png)

### <a name="solutions"></a>Rozwiązania

Po zebraniu podstawowych informacji w następnej kolejności pokazująmy rozwiązania do samodzielnego wypróbowania. W niektórych przypadkach może nawet uruchomić szybką diagnostykę. Rozwiązania są tworzone przez inżynierów platformy Azure i rozwiązują najczęstsze problemy.

### <a name="details"></a>Szczegóły

Następnie zbieramy dodatkowe informacje o problemie. Dokładne i szczegółowe informacje w tym kroku ułatwiają kierowanie żądania pomocy technicznej do właściwego inżyniera.

1. Jeśli to możliwe, powiedz nam, gdy problem zostanie uruchomiony, i wykonaj wszystkie kroki, aby je odtworzyć. Można przekazać plik, taki jak plik dziennika lub dane wyjściowe diagnostyki. Więcej informacji o przekazywaniu plików znajduje się w temacie [wskazówki dotyczące przekazywania plików](how-to-manage-azure-support-request.md#file-upload-guidelines).

1. Po uzyskaniu wszystkich informacji o problemie wybierz sposób uzyskania pomocy technicznej. W sekcji **Metoda pomocy technicznej** w obszarze **szczegóły** wybierz ważność wpływu. Maksymalny poziom ważności zależy od Twojego [planu pomocy technicznej](https://azure.microsoft.com/support/plans).

    Domyślnie wybrana jest opcja **Udostępnij informacje diagnostyczne** . Dzięki temu platforma Azure może zbierać [informacje diagnostyczne](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) z zasobów platformy Azure. W niektórych przypadkach istnieje drugie pytanie, które nie jest wybrane domyślnie, takie jak żądanie dostępu do pamięci maszyny wirtualnej.

1. Podaj preferowaną metodę kontaktu, dobry czas, aby skontaktować się z Tobą i językiem pomocy technicznej.

1. Następnie wypełnij sekcję **informacje kontaktowe** , aby dowiedzieć się, jak skontaktować się z Tobą.

### <a name="review--create"></a>Przeglądanie i tworzenie

Wykonaj wszystkie wymagane informacje na każdej karcie, a następnie wybierz pozycję **Przegląd + Utwórz**. Sprawdź szczegóły, które zostaną wysłane do pomocy technicznej. Wróć do dowolnej karty, aby wprowadzić zmianę w razie potrzeby. Gdy zostanie spełnione żądanie pomocy technicznej, wybierz pozycję **Utwórz**.

Inżynier pomocy technicznej skontaktuje się z Tobą przy użyciu wskazanej metody. Aby uzyskać informacje na temat czasów wstępnej reakcji, zobacz [zakres pomocy technicznej i czas odpowiedzi](https://azure.microsoft.com/support/plans/response/).


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o opcjach samoobsługowego rozwiązywania problemów na platformie Azure, Obejrzyj ten klip wideo:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

Skorzystaj z poniższych linków, aby dowiedzieć się więcej:

* [Jak zarządzać żądaniem pomocy technicznej platformy Azure](how-to-manage-azure-support-request.md)
* [Interfejs API REST biletu pomocy technicznej platformy Azure](/rest/api/support)
* [Wyślij nam swoją opinię i sugestie](https://feedback.azure.com/forums/266794-support-feedback)
* Zaangażuj nas w serwisie [Twitter](https://twitter.com/azuresupport)
* Uzyskaj pomoc od swoich elementów równorzędnych w [&stronie pytań](/answers/products/azure)
* Dowiedz się więcej na temat [często zadawanych pytań](https://azure.microsoft.com/support/faq)