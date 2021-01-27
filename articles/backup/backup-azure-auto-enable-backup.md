---
title: Automatyczne włączanie kopii zapasowych podczas tworzenia maszyny wirtualnej za pomocą usługi Azure Policy
description: Artykuł opisujący sposób użycia Azure Policy do samodzielnego włączania kopii zapasowych dla wszystkich maszyn wirtualnych utworzonych w danym zakresie
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 7e8195d22f54f29b36549b966322623ed0987d72
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896871"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Automatyczne włączanie kopii zapasowych podczas tworzenia maszyny wirtualnej za pomocą usługi Azure Policy

Jednym z kluczowych obowiązków administratora kopii zapasowej i zgodności w organizacji jest upewnienie się, że kopie zapasowe wszystkich maszyn o krytycznym znaczeniu dla firmy zostały uwzględnione w odpowiednim przechowywaniu.

Obecnie Azure Backup udostępnia wiele wbudowanych zasad (przy użyciu [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)), które ułatwiają automatyczne zagwarantowanie, że maszyny wirtualne platformy Azure są skonfigurowane pod kątem tworzenia kopii zapasowych. W zależności od tego, jak zespoły i zasoby tworzenia kopii zapasowych są zorganizowane, można użyć jednej z poniższych zasad:

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Zasady 1 — Konfigurowanie kopii zapasowych na maszynach wirtualnych bez danego tagu do istniejącego magazynu usług Recovery Services w tej samej lokalizacji

Jeśli organizacja ma centralny zespół kopii zapasowych, który zarządza kopiami zapasowymi w zespołach aplikacji, można użyć tych zasad, aby skonfigurować kopię zapasową istniejącego magazynu centralnego Recovery Services w tej samej subskrypcji i lokalizacji co zarządzane maszyny wirtualne. Z zakresu tych zasad można **wykluczać** maszyny wirtualne zawierające określony tag.

## <a name="policy-2---preview-configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Zasady 2 — [wersja zapoznawcza] Skonfiguruj kopię zapasową na maszynach wirtualnych z danym tagiem do istniejącego magazynu usługi Recovery Services w tej samej lokalizacji
Te zasady działają tak samo jak zasady 1 powyżej, z jedyną różnicą, że można użyć tych zasad w celu **uwzględnienia** maszyn wirtualnych zawierających określony tag w zakresie tych zasad. 

## <a name="policy-3---preview-configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Zasady 3 — [wersja zapoznawcza] Skonfiguruj kopię zapasową na maszynach wirtualnych bez danego tagu do nowego magazynu usługi Recovery Services przy użyciu zasad domyślnych
Jeśli organizujesz aplikacje w dedykowanych grupach zasobów i chcesz utworzyć ich kopię zapasową w tym samym magazynie, te zasady umożliwiają automatyczne zarządzanie tą akcją. Z zakresu tych zasad można **wykluczać** maszyny wirtualne zawierające określony tag.

## <a name="policy-4---preview-configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Zasady 4 — [wersja zapoznawcza] Skonfiguruj kopię zapasową na maszynach wirtualnych z danym tagiem do nowego magazynu usługi Recovery Services przy użyciu zasad domyślnych
Te zasady działają tak samo jak zasady 3 powyżej, z jedyną różnicą, że można użyć tych zasad w celu **uwzględnienia** maszyn wirtualnych zawierających określony tag w zakresie tych zasad. 

Oprócz powyższych, Azure Backup również zawiera zasady [tylko do inspekcji](https://docs.microsoft.com/azure/governance/policy/concepts/effects#audit) — **Azure Backup należy włączyć dla Virtual Machines**. Te zasady identyfikują, które maszyny wirtualne nie mają włączonej kopii zapasowej, ale nie konfigurują automatycznie kopii zapasowych dla tych maszyn wirtualnych. Jest to przydatne, gdy zamierzasz tylko oszacować ogólną zgodność maszyn wirtualnych, ale nie chcesz natychmiast podjąć działania.

## <a name="supported-scenarios"></a>Scenariusze obsługiwane

* Wbudowane zasady są obecnie obsługiwane tylko dla maszyn wirtualnych platformy Azure. Użytkownicy muszą mieć ostrożność, aby upewnić się, że zasady przechowywania określone podczas przypisywania są zasadami przechowywania maszyn wirtualnych. Zapoznaj się z [tym](./backup-azure-policy-supported-skus.md) dokumentem, aby wyświetlić wszystkie jednostki SKU maszyny wirtualnej obsługiwane przez te zasady.

* Zasady 1 i 2 można przypisywać jednocześnie do jednej lokalizacji i subskrypcji. Aby włączyć tworzenie kopii zapasowych maszyn wirtualnych w różnych lokalizacjach i subskrypcjach, należy utworzyć wiele wystąpień przypisywania zasad, po jednym dla każdej kombinacji lokalizacji i subskrypcji.

* W przypadku zasad 1 i 2 zakres grupy zarządzania nie jest obecnie obsługiwany.

* W przypadku zasad 1 i 2 określony magazyn i maszyny wirtualne skonfigurowane do tworzenia kopii zapasowych mogą znajdować się w różnych grupach zasobów.

* Zasady 1, 2, 3 i 4 nie są obecnie dostępne w chmurach krajowych.

* Zasady 3 i 4 mogą być przypisane do jednej subskrypcji na raz (lub w grupie zasobów w ramach subskrypcji).

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>Korzystanie z wbudowanych zasad

Poniższe kroki opisują kompleksowy proces przypisywania zasad 1: **Skonfiguruj kopię zapasową na maszynach wirtualnych bez danego tagu do istniejącego magazynu usługi Recovery Services w tej samej lokalizacji, w którym** znajduje się dany zakres. Podobne instrukcje będą dotyczyć innych zasad. Po przypisaniu każda nowa maszyna wirtualna utworzona w zakresie zostanie automatycznie skonfigurowana do tworzenia kopii zapasowej.

1. Zaloguj się do Azure Portal i przejdź do pulpitu nawigacyjnego **zasad** .
2. Wybierz pozycję **definicje** w menu po lewej stronie, aby wyświetlić listę wszystkich wbudowanych zasad w ramach zasobów platformy Azure.
3. Przefiltruj listę **kategorii = kopia zapasowa** i wybierz zasady o nazwie "Konfiguruj kopię zapasową na maszynach wirtualnych lokalizacji do istniejącego magazynu centralnego w tej samej lokalizacji".
![Pulpit nawigacyjny zasad](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Wybierz nazwę zasad. Nastąpi przekierowanie do szczegółowej definicji dla tych zasad.
![Okienko definicji zasad](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Wybierz przycisk **Przypisz** w górnej części okienka. Spowoduje to przekierowanie do okienka **przypisywanie zasad** .
6. W obszarze **podstawy** wybierz trzy kropki obok pola **zakres** . Spowoduje to otwarcie prawego okienka kontekstowego, w którym można wybrać subskrypcję zasad, która ma zostać zastosowana. Opcjonalnie można również wybrać grupę zasobów, aby zasady były stosowane tylko w przypadku maszyn wirtualnych w określonej grupie zasobów.
![Podstawowe informacje dotyczące przypisywania zasad](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. Na karcie **Parametry** wybierz lokalizację z listy rozwijanej, a następnie wybierz magazyn i zasady tworzenia kopii zapasowych, do których należy skojarzyć maszyny wirtualne w zakresie. Można również określić nazwę tagu i tablicę wartości tagów. Maszyna wirtualna zawierająca dowolne z określonych wartości dla danego tagu zostanie wykluczona z zakresu przypisania zasad.
![Parametry przypisania zasad](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Upewnij się, że dla **efektu** ustawiono wartość deployIfNotExists.
9. Przejdź do opcji **Recenzja + Utwórz** i wybierz pozycję **Utwórz**.

> [!NOTE]
>
> Azure Policy można również używać na istniejących maszynach wirtualnych przy użyciu [korygowania](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> Zaleca się, aby te zasady nie były przypisane do ponad 200 maszyn wirtualnych jednocześnie. Jeśli zasady są przypisane do ponad 200 maszyn wirtualnych, może to spowodować, że wykonywanie kopii zapasowej zostanie wyzwolone kilka godzin później niż określono w harmonogramie.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o Azure Policy](../governance/policy/overview.md)
