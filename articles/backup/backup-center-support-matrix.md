---
title: Macierz obsługi dla centrum kopii zapasowych
description: Ten artykuł zawiera podsumowanie scenariuszy obsługiwanych przez centrum kopii zapasowych dla każdego typu obciążenia
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 672f0e127f173260f25978497198fd7b554aa390
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98893596"
---
# <a name="support-matrix-for-backup-center"></a>Macierz obsługi dla centrum kopii zapasowych

Centrum kopii zapasowych udostępnia pojedyncze okienko szkła dla przedsiębiorstw, które pozwala na [Zarządzanie, monitorowanie, obsługiwanie i analizowanie kopii zapasowych na dużą skalę](backup-center-overview.md). Ten artykuł zawiera podsumowanie scenariuszy obsługiwanych przez centrum kopii zapasowych dla każdego typu obciążenia.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

| **Kategoria** | **Scenariusz**  | **Obsługiwane obciążenia**  | **Limity** |
| -------------| ------------- | ----------------------- |------------|
| Monitorowanie   | Wyświetl wszystkie zadania | <li> Maszyna wirtualna platformy Azure <br><br> <li> Serwer Azure Database for PostgreSQL <br><br> <li> SQL na maszynie wirtualnej platformy Azure <br><br> <li> SAP HANA na maszynie wirtualnej platformy Azure <br><br> <li> Pliki platformy Azure | <li> 7 dni, w przypadku których dostępne są zadania. <br> <li> Każdy filtr/lista rozwijana obsługuje maksymalnie 1000 elementów. Centrum kopii zapasowych może służyć do monitorowania maksymalnie 1000 subskrypcji i 1000 magazynów w dzierżawach. |
| Monitorowanie | Wyświetl wszystkie wystąpienia kopii zapasowej | <li> Maszyna wirtualna platformy Azure <br><br> <li> Serwer Azure Database for PostgreSQL <br><br> <li> SQL na maszynie wirtualnej platformy Azure <br><br> <li> SAP HANA na maszynie wirtualnej platformy Azure <br><br> <li> Azure Files | Jak wyżej |
| Monitorowanie | Wyświetl wszystkie zasady tworzenia kopii zapasowych | <li> Maszyna wirtualna platformy Azure <br><br> <li> Serwer Azure Database for PostgreSQL <br><br> <li> SQL na maszynie wirtualnej platformy Azure <br><br> <li> SAP HANA na maszynie wirtualnej platformy Azure <br><br> <li> Azure Files | Jak wyżej |
| Monitorowanie | Wyświetl wszystkie magazyny | <li> Maszyna wirtualna platformy Azure <br><br> <li> Serwer Azure Database for PostgreSQL <br><br> <li> SQL na maszynie wirtualnej platformy Azure <br><br> <li> SAP HANA na maszynie wirtualnej platformy Azure <br><br> <li> Azure Files | Jak wyżej |
| Akcje | Konfigurowanie kopii zapasowych | <li> Maszyna wirtualna platformy Azure <br><br> <li> Serwer Azure Database for PostgreSQL <br><br> <li> SQL na maszynie wirtualnej platformy Azure <br><br> <li> SAP HANA na maszynie wirtualnej platformy Azure <br><br> <li> Azure Files | Zapoznaj się z macierzami pomocniczymi dla kopii [zapasowych maszyny wirtualnej platformy Azure](./backup-support-matrix-iaas.md) [Azure Database for PostgreSQL i kopii zapasowej serwera](backup-azure-database-postgresql.md#support-matrix) |
| Akcje | Przywróć wystąpienie kopii zapasowej | <li> Maszyna wirtualna platformy Azure <br><br> <li> Serwer Azure Database for PostgreSQL <br><br> <li> SQL na maszynie wirtualnej platformy Azure <br><br> <li> SAP HANA na maszynie wirtualnej platformy Azure <br><br> <li> Azure Files | Zapoznaj się z macierzami pomocniczymi dla kopii [zapasowych maszyny wirtualnej platformy Azure](./backup-support-matrix-iaas.md) [Azure Database for PostgreSQL i kopii zapasowej serwera](backup-azure-database-postgresql.md#support-matrix) |
| Akcje | Utwórz magazyn | <li> Maszyna wirtualna platformy Azure <br><br> <li> Serwer Azure Database for PostgreSQL <br><br> <li> SQL na maszynie wirtualnej platformy Azure <br><br> <li> SAP HANA na maszynie wirtualnej platformy Azure <br><br> <li> Azure Files | Zapoznaj się z macierzami pomocy technicznej dla [magazynu Recovery Services](./backup-support-matrix.md#vault-support) |
| Akcje | Tworzenie zasad kopii zapasowych | <li> Maszyna wirtualna platformy Azure <br><br> <li> Serwer Azure Database for PostgreSQL <br><br> <li> SQL na maszynie wirtualnej platformy Azure <br><br> <li> SAP HANA na maszynie wirtualnej platformy Azure <br><br> <li> Azure Files | Zapoznaj się z macierzami pomocy technicznej dla [magazynu Recovery Services](./backup-support-matrix.md#vault-support) |
| Akcje | Wykonaj kopię zapasową na żądanie dla wystąpienia kopii zapasowej | <li> Maszyna wirtualna platformy Azure <br><br> <li> Serwer Azure Database for PostgreSQL <br><br> <li> SQL na maszynie wirtualnej platformy Azure <br><br> <li> SAP HANA na maszynie wirtualnej platformy Azure <br><br> <li> Azure Files | Zapoznaj się z macierzami pomocniczymi dla kopii [zapasowych maszyny wirtualnej platformy Azure](./backup-support-matrix-iaas.md) [Azure Database for PostgreSQL i kopii zapasowej serwera](backup-azure-database-postgresql.md#support-matrix) |
| Akcje | Zatrzymaj kopię zapasową wystąpienia kopii zapasowej | <li> Maszyna wirtualna platformy Azure <br><br> <li> Serwer Azure Database for PostgreSQL <br><br> <li> SQL na maszynie wirtualnej platformy Azure <br><br> <li> SAP HANA na maszynie wirtualnej platformy Azure <br><br> <li> Azure Files | Zapoznaj się z macierzami pomocniczymi dla kopii [zapasowych maszyny wirtualnej platformy Azure](./backup-support-matrix-iaas.md) [Azure Database for PostgreSQL i kopii zapasowej serwera](backup-azure-database-postgresql.md#support-matrix) |
| Insights | Wyświetlanie raportów kopii zapasowych | <li> Maszyna wirtualna platformy Azure <br><br> <li> SQL na maszynie wirtualnej platformy Azure <br><br> <li> SAP HANA na maszynie wirtualnej platformy Azure <br><br> <li> Azure Files <br><br> <li> System Center Data Protection Manager <br><br> <li> Agent Azure Backup (MARS) <br><br> <li> Azure Backup Server (MABS) | Zapoznaj się z [obsługiwanymi scenariuszami dotyczącymi raportów kopii zapasowych](./configure-reports.md#supported-scenarios) |
| Nadzór | Wyświetlanie i przypisywanie wbudowanych i niestandardowych zasad platformy Azure w kategorii "kopia zapasowa" | NIE DOTYCZY | NIE DOTYCZY |
| Nadzór | Wyświetl źródła danych, które nie są skonfigurowane dla kopii zapasowej | <li> Maszyna wirtualna platformy Azure <br><br> <li> Serwer Azure Database for PostgreSQL | Nie dotyczy |

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

| **Kategoria** | **Scenariusz**  |
|--------------|---------------|
| Monitorowanie | Wyświetlanie alertów na dużą skalę |
| Akcje | Konfigurowanie ustawień magazynu na dużą skalę |
| Akcje | Wykonywanie zadania przywracania między regionami z poziomu centrum kopii zapasowych |

## <a name="next-steps"></a>Następne kroki

* [Zapoznaj się z matrycą pomocy technicznej dla Azure Backup](./backup-support-matrix.md)
* [Przegląd macierzy obsługi kopii zapasowej maszyny wirtualnej platformy Azure](./backup-support-matrix-iaas.md)
* [Zapoznaj się z macierzą pomocy technicznej dla Azure Database for PostgreSQL kopii zapasowej serwera](backup-azure-database-postgresql.md#support-matrix)