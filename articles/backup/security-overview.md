---
title: Omówienie funkcji zabezpieczeń
description: Dowiedz się więcej o możliwościach zabezpieczeń w Azure Backup, które ułatwiają ochronę danych kopii zapasowych i zaspokajanie potrzeb firmy.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 9aa1909f1590b477d9a7f7a09ad0c2b1936e3e29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96325659"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Omówienie funkcji zabezpieczeń w programie Azure Backup

Jednym z najważniejszych kroków, które można wykonać w celu ochrony danych, jest posiadanie niezawodnej infrastruktury kopii zapasowych. Jednak równie ważne jest, aby zapewnić, że kopie zapasowe danych są wykonywane w bezpieczny sposób i że kopie zapasowe są chronione przez cały czas. Azure Backup zapewnia zabezpieczenia dla środowiska kopii zapasowej — zarówno w przypadku przesyłania danych, jak i w spoczynku. W tym artykule wymieniono możliwości zabezpieczeń w Azure Backup, które pomagają chronić dane kopii zapasowych i zaspokajać potrzeby firmy.

## <a name="management-and-control-of-identity-and-user-access"></a>Zarządzanie tożsamościami i dostępem użytkowników oraz ich kontrola

Konta magazynu używane przez magazyny Recovery Services są izolowane i nie są dostępne dla użytkowników w żadnych złośliwych celach. Dostęp jest dozwolony tylko za pomocą Azure Backup operacji zarządzania, takich jak przywracanie. Azure Backup pozwala kontrolować zarządzane operacje za pośrednictwem szczegółowego dostępu przy użyciu [kontroli dostępu opartej na rolach (Azure RBAC)](./backup-rbac-rs-vault.md). Kontrola RBAC platformy Azure umożliwia rozdzielenie obowiązków w zespole i przyznanie dostępu użytkownikom niezbędnym do wykonywania swoich zadań.

Azure Backup udostępnia trzy [wbudowane role](../role-based-access-control/built-in-roles.md) do kontrolowania operacji zarządzania kopiami zapasowymi:

* Współautorzy kopii zapasowych — tworzenie kopii zapasowych i zarządzanie nimi, z wyjątkiem usuwania magazynu Recovery Services i udzielania dostępu innym osobom
* Operator kopii zapasowych — wszystko to współautor, z wyjątkiem usuwania kopii zapasowych i zarządzania zasadami tworzenia kopii zapasowych
* Czytnik kopii zapasowych — uprawnienia do wyświetlania wszystkich operacji zarządzania kopiami zapasowymi

Dowiedz się więcej [na temat kontroli dostępu opartej na rolach na platformie Azure, aby zarządzać Azure Backup](./backup-rbac-rs-vault.md).

Azure Backup ma kilka kontrolek zabezpieczeń wbudowanych w usługę w celu zapobiegania i wykrywania luk w zabezpieczeniach oraz reagowania na nie. Dowiedz się więcej o [kontrolkach zabezpieczeń dla Azure Backup](./security-baseline.md).

## <a name="separation-between-guest-and-azure-storage"></a>Rozdzielenie między gośćmi a usługą Azure Storage

W przypadku Azure Backup, które obejmują kopie zapasowe maszyn wirtualnych oraz SQL i SAP HANA w kopii zapasowej maszyny wirtualnej, dane kopii zapasowej są przechowywane w usłudze Azure Storage, a gość nie ma bezpośredniego dostępu do magazynu kopii zapasowych ani jego zawartości.  W przypadku tworzenia kopii zapasowych maszyn wirtualnych tworzenie i przechowywanie migawek kopii zapasowej odbywa się za pomocą sieci szkieletowej platformy Azure, w której Gość nie ma udziału w stanie innym niż przekroczenie obciążenia kopii zapasowych spójnych z aplikacjami.  W przypadku używania SQL i SAP HANA rozszerzenie kopii zapasowej pobiera tymczasowy dostęp do zapisu w określonych obiektach Blob.  W ten sposób nawet w środowisku z naruszonymi kopiami zapasowymi nie można zmieniać ani usuwać z nich kopii

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Łączność z Internetem nie jest wymagana w przypadku kopii zapasowej maszyny wirtualnej platformy Azure

Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure wymaga przenoszenia danych z dysku maszyny wirtualnej do magazynu Recovery Services. Jednak wszystkie wymagane operacje komunikacji i transferu danych odbywają się tylko w sieci szkieletowej platformy Azure bez konieczności uzyskiwania dostępu do sieci wirtualnej. W związku z tym tworzenie kopii zapasowych maszyn wirtualnych platformy Azure umieszczonych w zabezpieczonych sieciach nie wymaga zezwolenia na dostęp do żadnych adresów IP ani nazw FQDN.

## <a name="private-endpoints-for-azure-backup"></a>Prywatne punkty końcowe dla Azure Backup

Możesz teraz używać [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md) do bezpiecznego wykonywania kopii zapasowych danych z serwerów w sieci wirtualnej do magazynu Recovery Services. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla Twojego magazynu, więc nie trzeba ujawniać sieci wirtualnych żadnym publicznym adresom IP. Prywatne punkty końcowe mogą służyć do tworzenia kopii zapasowych i przywracania baz danych SQL i SAP HANA, które działają w ramach maszyn wirtualnych platformy Azure. Można go również użyć dla serwerów lokalnych przy użyciu agenta MARS.

Przeczytaj więcej na temat prywatnych punktów końcowych Azure Backup [tym miejscu](./private-endpoints.md).

## <a name="encryption-of-data"></a>Szyfrowanie danych

Szyfrowanie chroni dane i pomaga sprostać wymaganiom dotyczącym zabezpieczeń i zgodności w organizacji. Szyfrowanie danych odbywa się w wielu etapach Azure Backup:

* W ramach platformy Azure dane przesyłane między usługą Azure Storage i magazynem są [chronione za pośrednictwem protokołu HTTPS](backup-support-matrix.md#network-traffic-to-azure). Te dane pozostają w sieci szkieletowej platformy Azure.

* Dane kopii zapasowej są automatycznie szyfrowane przy użyciu [kluczy zarządzanych przez platformę](backup-encryption.md)i nie trzeba podejmować żadnych jawnych akcji, aby je włączyć. Dane kopii zapasowej można także szyfrować przy użyciu [kluczy zarządzanych przez klienta](encryption-at-rest-with-cmk.md) przechowywanych w Azure Key Vault. Ma to zastosowanie do wszystkich obciążeń, których kopie zapasowe są tworzone w magazynie Recovery Services.

* Azure Backup obsługuje tworzenie kopii zapasowych i przywracanie maszyn wirtualnych platformy Azure, w których dyski systemu operacyjnego/danych są szyfrowane za pomocą [Azure Disk Encryption (ADE)](backup-azure-vms-encryption.md#encryption-support-using-ade) i [maszyn wirtualnych z dyskami szyfrowanymi CMK](backup-azure-vms-encryption.md#encryption-using-customer-managed-keys). Aby uzyskać więcej informacji, [Dowiedz się więcej o zaszyfrowanych maszynach wirtualnych platformy Azure i Azure Backup](./backup-azure-vms-encryption.md).

* W przypadku tworzenia kopii zapasowej danych z serwerów lokalnych przy użyciu agenta MARS dane są szyfrowane przy użyciu hasła przed przekazaniem do Azure Backup i odszyfrowywane dopiero po pobraniu z Azure Backup. Dowiedz [się więcej na temat funkcji zabezpieczeń, aby chronić hybrydowe kopie zapasowe](#security-features-to-help-protect-hybrid-backups).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Ochrona danych kopii zapasowej przed przypadkowym usunięciem

Azure Backup zapewnia funkcje zabezpieczeń chroniące dane kopii zapasowej nawet po usunięciu. W przypadku usuwania nietrwałego, jeśli użytkownik usunie kopię zapasową maszyny wirtualnej, dane kopii zapasowej są przechowywane przez 14 dodatkowych dni, umożliwiając odzyskanie tego elementu kopii zapasowej bez utraty danych. Dodatkowe 14 dni przechowywania danych kopii zapasowej w stanie "usuwanie nietrwałe" nie wiążą się z pozostałymi kosztami. [Dowiedz się więcej na temat usuwania nietrwałego](backup-azure-security-feature-cloud.md).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitorowanie i alerty podejrzanych działań

Azure Backup udostępnia [wbudowane funkcje monitorowania i alertów](./backup-azure-monitoring-built-in-monitor.md) umożliwiające wyświetlanie i Konfigurowanie akcji dla zdarzeń związanych z Azure Backup. [Raporty kopii zapasowych](./configure-reports.md) stanowią jedno miejsce docelowe do śledzenia użycia, inspekcji kopii zapasowych i przywracania oraz identyfikowania kluczowych trendów na różnych poziomach szczegółowości. Korzystając z narzędzi do monitorowania i raportowania Azure Backup, można powiadamiać użytkownika o wszelkich nieautoryzowanych, podejrzanych lub złośliwych działaniach, gdy tylko wystąpią.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Funkcje zabezpieczeń chroniące hybrydowe kopie zapasowe

Usługa Azure Backup używa agenta Microsoft Azure Recovery Services (MARS) do tworzenia kopii zapasowych i przywracania plików, folderów oraz stanu woluminu lub systemu z komputera lokalnego na platformę Azure. Usługa MARS zapewnia teraz funkcje zabezpieczeń chroniące hybrydowe kopie zapasowe. Są one następujące:

* Dodatkowa warstwa uwierzytelniania jest dodawana zawsze wtedy, gdy jest wykonywane krytyczne operacje, takie jak zmiana hasła. Ta weryfikacja polega na zapewnieniu, że operacje te mogą być wykonywane tylko przez użytkowników, którzy mają prawidłowe poświadczenia platformy Azure. [Dowiedz się więcej o funkcjach, które uniemożliwiają ataki](./backup-azure-security-feature.md#prevent-attacks).

* Usunięte dane kopii zapasowej są przechowywane przez dodatkowe 14 dni od daty usunięcia. Zapewnia to możliwość odzyskania danych w danym okresie, dlatego nie ma utraty danych, nawet jeśli wystąpi atak. Ponadto do ochrony przed uszkodzeniem danych są utrzymywane większą liczbę punktów odzyskiwania. [Dowiedz się więcej o odzyskiwaniu usuniętych danych kopii zapasowej](./backup-azure-security-feature.md#recover-deleted-backup-data).

* W przypadku danych utworzonych przy użyciu agenta Microsoft Azure Recovery Services (MARS) hasło służy do zapewnienia szyfrowania danych przed przekazaniem do Azure Backup i odszyfrowywane dopiero po pobraniu z Azure Backup. Szczegóły hasła są dostępne tylko dla użytkownika, który utworzył hasło, oraz agenta, który jest z nim skonfigurowany. Nic nie jest przesyłane ani udostępniane usłudze. Zapewnia to pełne bezpieczeństwo danych, ponieważ wszystkie dane, które zostały przypadkowo ujawnione (na przykład atak typu "man-in-the-middle" w sieci) nie nadaje się do użytku bez hasła, a hasło nie jest wysyłane przez sieć.

## <a name="compliance-with-standardized-security-requirements"></a>Zgodność z znormalizowanymi wymaganiami dotyczącymi zabezpieczeń

Aby ułatwić organizacjom zgodność z krajowymi, regionalnymi i branżowymi wymaganiami dotyczącymi zbierania i używania danych indywidualnych, Microsoft Azure & Azure Backup oferuje kompleksowy zestaw certyfikatów i zaświadczania. [Zapoznaj się z listą certyfikatów zgodności](compliance-offerings.md)

## <a name="next-steps"></a>Następne kroki

* [Funkcje zabezpieczeń pomagające w ochronie obciążeń w chmurze korzystających z Azure Backup](backup-azure-security-feature-cloud.md)
* [Funkcje zabezpieczeń chroniące hybrydowe kopie zapasowe, które używają Azure Backup](backup-azure-security-feature.md)