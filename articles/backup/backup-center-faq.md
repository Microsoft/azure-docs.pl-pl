---
title: Centrum kopii zapasowych — często zadawane pytania
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące centrum kopii zapasowych
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: b10a9e73e65cf12c43ce28b429a8f12e0b960a76
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997686"
---
# <a name="backup-center---frequently-asked-questions"></a>Centrum kopii zapasowych — często zadawane pytania

## <a name="management"></a>Zarządzanie

### <a name="can-backup-center-be-used-across-tenants"></a>Czy można korzystać z centrum kopii zapasowych w wielu dzierżawcach?

Tak, jeśli używasz [usługi Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) i masz delegowany dostęp do subskrypcji między różnymi dzierżawcami, możesz użyć centrum kopii zapasowych jako pojedynczego okienka Glass do zarządzania kopiami zapasowymi między dzierżawcami.

### <a name="can-backup-center-be-used-to-manage-both-recovery-services-vaults-and-backup-vaults"></a>Czy można zarządzać magazynami Recovery Services i magazynami kopii zapasowych przy użyciu centrum kopii zapasowych?

Tak, centrum kopii zapasowych może zarządzać [magazynami Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) i [magazynami kopii zapasowych](backup-vault-overview.md).

### <a name="is-there-a-delay-before-data-surfaces-in-backup-center"></a>Czy istnieje opóźnienie przed powierzchniami danych w centrum kopii zapasowych?

Centrum kopii zapasowych ma na celu dostarczanie informacji w czasie rzeczywistym. Może istnieć kilka sekund zwłoki między czasem, gdy jednostka zostanie wyświetlona na pojedynczym ekranie magazynu, a czas tego samego podmiotu jest wyświetlany w centrum kopii zapasowych.

## <a name="configuration"></a>Konfiguracja

### <a name="do-i-need-to-configure-anything-to-see-data-in-backup-center"></a>Czy muszę skonfigurować wszystko, aby wyświetlić dane w centrum kopii zapasowych?

Nie. Centrum kopii zapasowych jest gotowe do użycia. Aby jednak wyświetlić [raporty dotyczące kopii zapasowych](https://docs.microsoft.com/azure/backup/configure-reports) w ramach centrum kopii zapasowych, należy skonfigurować raportowanie dla swoich magazynów.

### <a name="do-i-need-to-have-any-special-permissions-to-use-backup-center"></a>Czy muszę mieć specjalne uprawnienia do korzystania z centrum kopii zapasowych?

Centrum kopii zapasowych nie wymaga żadnych nowych uprawnień. O ile dysponujesz odpowiednim poziomem dostępu RBAC dla zasobów, którymi zarządzasz, możesz użyć centrum kopii zapasowych dla tych zasobów. Aby na przykład wyświetlić informacje o kopiach zapasowych, musisz mieć dostęp do swoich magazynów przez **czytelnika** . Aby skonfigurować kopię zapasową i wykonać inne akcje związane z kopiami zapasowymi, musisz mieć role **współautor kopii zapasowej** lub **Operatorzy kopii zapasowych** . Dowiedz się więcej o [rolach RBAC dla Azure Backup](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="pricing"></a>Cennik

### <a name="do-i-need-to-pay-anything-extra-to-use-backup-explorer"></a>Czy muszę uiścić wszystkie dodatkowe możliwości korzystania z Eksploratora kopii zapasowych?

Obecnie nie ma dodatkowych kosztów (oprócz kosztów tworzenia kopii zapasowych) do korzystania z centrum kopii zapasowych. Jeśli jednak korzystasz z [raportów kopii zapasowych](https://docs.microsoft.com/azure/backup/configure-reports) w obszarze centrum kopii zapasowych, jest [naliczany koszt](https://azure.microsoft.com/pricing/details/monitor/) korzystania z dzienników Azure monitor na potrzeby raportów kopii zapasowych.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi często zadawanymi pytaniami:

* [Często zadawane pytania dotyczące magazynów Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq)
* [Często zadawane pytania dotyczące kopii zapasowych maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq)
