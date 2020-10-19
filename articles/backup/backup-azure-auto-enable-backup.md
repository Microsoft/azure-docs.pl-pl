---
title: Automatyczne włączanie kopii zapasowych podczas tworzenia maszyny wirtualnej za pomocą usługi Azure Policy
description: Artykuł opisujący sposób użycia Azure Policy do samodzielnego włączania kopii zapasowych dla wszystkich maszyn wirtualnych utworzonych w danym zakresie
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 78fe0ccdbf6f1cc3498d14530d7492a86e8bf730
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92174077"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Automatyczne włączanie kopii zapasowych podczas tworzenia maszyny wirtualnej za pomocą usługi Azure Policy

Jednym z kluczowych obowiązków administratora kopii zapasowej i zgodności w organizacji jest upewnienie się, że kopie zapasowe wszystkich maszyn o krytycznym znaczeniu dla firmy zostały uwzględnione w odpowiednim przechowywaniu.

Obecnie Azure Backup udostępnia wbudowane zasady (przy użyciu Azure Policy), które można przypisać do **wszystkich maszyn wirtualnych platformy Azure w określonej lokalizacji w ramach subskrypcji lub grupy zasobów**. Gdy te zasady są przypisane do danego zakresu, wszystkie nowe maszyny wirtualne utworzone w tym zakresie są automatycznie konfigurowane na potrzeby tworzenia kopii zapasowych **w istniejącym magazynie w tej samej lokalizacji i subskrypcji**. Użytkownik może określić magazyn i zasady przechowywania, do których mają być skojarzone maszyny wirtualne z kopią zapasową.

## <a name="supported-scenarios"></a>Scenariusze obsługiwane

* Wbudowane zasady są obecnie obsługiwane tylko dla maszyn wirtualnych platformy Azure. Użytkownicy muszą mieć ostrożność, aby upewnić się, że zasady przechowywania określone podczas przypisywania są zasadami przechowywania maszyn wirtualnych. Zapoznaj się z [tym](./backup-azure-policy-supported-skus.md) dokumentem, aby wyświetlić wszystkie jednostki SKU maszyny wirtualnej obsługiwane przez te zasady.

* Zasady można przypisywać jednocześnie do jednej lokalizacji i subskrypcji. Aby włączyć tworzenie kopii zapasowych maszyn wirtualnych w różnych lokalizacjach i subskrypcjach, należy utworzyć wiele wystąpień przypisywania zasad, po jednym dla każdej kombinacji lokalizacji i subskrypcji.

* Określony magazyn i maszyny wirtualne skonfigurowane do tworzenia kopii zapasowych mogą znajdować się w różnych grupach zasobów.

* Zakres grupy zarządzania nie jest obecnie obsługiwany.

* Wbudowane zasady nie są obecnie dostępne w chmurach krajowych.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policy"></a>Korzystanie z zasad wbudowanych

Aby przypisać zasady do wymaganego zakresu, wykonaj następujące czynności:

1. Zaloguj się do Azure Portal i przejdź do pulpitu nawigacyjnego **zasad** .
1. Wybierz pozycję **definicje** w menu po lewej stronie, aby wyświetlić listę wszystkich wbudowanych zasad w ramach zasobów platformy Azure.
1. Filtrowanie listy dla **kategorii = kopia zapasowa**. Zostanie wyświetlona lista przefiltrowana w dół do jednej zasady o nazwie "Konfiguruj kopię zapasową na maszynach wirtualnych lokalizacji do istniejącego magazynu centralnego w tej samej lokalizacji".
![Pulpit nawigacyjny zasad](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
1. Wybierz nazwę zasad. Nastąpi przekierowanie do szczegółowej definicji dla tych zasad.
![Okienko definicji zasad](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
1. Wybierz przycisk **Przypisz** w górnej części okienka. Spowoduje to przekierowanie do okienka **przypisywanie zasad** .
1. W obszarze **podstawy**wybierz trzy kropki obok pola **zakres** . Spowoduje to otwarcie prawego okienka kontekstowego, w którym można wybrać subskrypcję zasad, która ma zostać zastosowana. Opcjonalnie można również wybrać grupę zasobów, aby zasady były stosowane tylko w przypadku maszyn wirtualnych w określonej grupie zasobów.
![Podstawowe informacje dotyczące przypisywania zasad](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
1. Na karcie **Parametry** wybierz lokalizację z listy rozwijanej, a następnie wybierz magazyn i zasady tworzenia kopii zapasowych, do których należy skojarzyć maszyny wirtualne w zakresie.
![Parametry przypisania zasad](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
1. Upewnij się, że dla **efektu** ustawiono wartość deployIfNotExists.
1. Przejdź do opcji **Recenzja + Utwórz** i wybierz pozycję **Utwórz**.

> [!NOTE]
>
> Azure Policy można również używać na istniejących maszynach wirtualnych przy użyciu [korygowania](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> Zaleca się, aby te zasady nie były przypisane do ponad 200 maszyn wirtualnych jednocześnie. Jeśli zasady są przypisane do ponad 200 maszyn wirtualnych, może to spowodować, że wykonywanie kopii zapasowej zostanie wyzwolone kilka godzin później niż określono w harmonogramie.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o Azure Policy](../governance/policy/overview.md)
