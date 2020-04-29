---
title: Omówienie funkcji zabezpieczeń
description: Dowiedz się więcej o możliwościach zabezpieczeń w Azure Backup, które ułatwiają ochronę danych kopii zapasowych i zaspokajanie potrzeb firmy.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 2eec3ee50f1de695b5432ee50b0900e35b81a6eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585815"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Omówienie funkcji zabezpieczeń w programie Azure Backup

Jednym z najważniejszych kroków, które można wykonać w celu ochrony danych, jest posiadanie niezawodnej infrastruktury kopii zapasowych. Jednak równie ważne jest, aby zapewnić, że kopie zapasowe danych są wykonywane w bezpieczny sposób i że kopie zapasowe są chronione przez cały czas. Azure Backup zapewnia zabezpieczenia dla środowiska kopii zapasowej — zarówno w przypadku przesyłania danych, jak i w spoczynku. W tym artykule wymieniono możliwości zabezpieczeń w Azure Backup, które pomagają chronić dane kopii zapasowych i zaspokajać potrzeby firmy.

## <a name="management-and-control-of-identity-and-user-access"></a>Zarządzanie tożsamościami i dostępem użytkowników oraz ich kontrola

Azure Backup umożliwia zarządzanie szczegółowym dostępem przy użyciu [Access Control opartego na rolach (RBAC) na platformie Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). RBAC umożliwia rozdzielenie obowiązków w zespole i udzielanie dostępu użytkownikom niezbędnym do wykonywania swoich zadań.

Azure Backup udostępnia trzy wbudowane role do kontrolowania operacji zarządzania kopiami zapasowymi:

* Współautorzy kopii zapasowych — tworzenie kopii zapasowych i zarządzanie nimi, z wyjątkiem usuwania magazynu Recovery Services i udzielania dostępu innym osobom
* Operator kopii zapasowych — wszystko to współautor, z wyjątkiem usuwania kopii zapasowych i zarządzania zasadami tworzenia kopii zapasowych
* Czytnik kopii zapasowych — uprawnienia do wyświetlania wszystkich operacji zarządzania kopiami zapasowymi

Dowiedz się więcej na temat [kontroli dostępu opartej na rolach, aby zarządzać Azure Backup](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

Azure Backup ma kilka kontrolek zabezpieczeń wbudowanych w usługę w celu zapobiegania i wykrywania luk w zabezpieczeniach oraz reagowania na nie. Dowiedz się więcej o [kontrolkach zabezpieczeń dla Azure Backup](https://docs.microsoft.com/azure/backup/backup-security-controls).

## <a name="separation-between-guest-and-azure-storage"></a>Rozdzielenie między gośćmi a usługą Azure Storage

W przypadku Azure Backup, które obejmują kopie zapasowe maszyn wirtualnych oraz SQL i SAP HANA w kopii zapasowej maszyny wirtualnej, dane kopii zapasowej są przechowywane w usłudze Azure Storage, a gość nie ma bezpośredniego dostępu do magazynu kopii zapasowych ani jego zawartości.  W przypadku tworzenia kopii zapasowych maszyn wirtualnych tworzenie i przechowywanie migawek kopii zapasowej odbywa się za pomocą sieci szkieletowej platformy Azure, w której Gość nie ma udziału w stanie innym niż przekroczenie obciążenia kopii zapasowych spójnych z aplikacjami.  W przypadku używania SQL i SAP HANA rozszerzenie kopii zapasowej pobiera tymczasowy dostęp do zapisu w określonych obiektach Blob.  W ten sposób nawet w środowisku z naruszonymi kopiami zapasowymi nie można zmieniać ani usuwać z nich kopii

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Łączność z Internetem nie jest wymagana w przypadku kopii zapasowej maszyny wirtualnej platformy Azure

Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure wymaga przenoszenia danych z dysku maszyny wirtualnej do magazynu Recovery Services. Jednak wszystkie wymagane operacje komunikacji i transferu danych odbywają się tylko w sieci szkieletowej platformy Azure bez konieczności uzyskiwania dostępu do sieci wirtualnej. W związku z tym tworzenie kopii zapasowych maszyn wirtualnych platformy Azure umieszczonych w zabezpieczonych sieciach nie wymaga zezwolenia na dostęp do żadnych adresów IP ani nazw FQDN.

## <a name="private-endpoints-for-azure-backup"></a>Prywatne punkty końcowe dla usługi Azure Backup

Możesz teraz używać [prywatnych punktów końcowych](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) do bezpiecznego wykonywania kopii zapasowych danych z serwerów w sieci wirtualnej do magazynu Recovery Services. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla Twojego magazynu, więc nie trzeba ujawniać sieci wirtualnych żadnym publicznym adresom IP. Prywatne punkty końcowe mogą służyć do tworzenia kopii zapasowych i przywracania baz danych SQL i SAP HANA, które działają w ramach maszyn wirtualnych platformy Azure. Można go również użyć dla serwerów lokalnych przy użyciu agenta MARS.

>[!NOTE]
> Ta funkcja jest obecnie w ograniczonej dostępności. Wypełnij [tę ankietę](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) i [Wyślij wiadomość e-mail do nas](mailto:azbackupnetsec@microsoft.com) , Jeśli interesuje Cię używanie prywatnych punktów końcowych dla Azure Backup. Możliwość korzystania z tej funkcji podlega zatwierdzeniu przez usługę Azure Backup.

## <a name="encryption-of-data-in-transit-and-at-rest"></a>Szyfrowanie danych podczas przesyłania i przechowywania

Szyfrowanie chroni dane i pomaga sprostać wymaganiom dotyczącym zabezpieczeń i zgodności w organizacji. W ramach platformy Azure dane przesyłane między usługą Azure Storage i magazynem są chronione za pośrednictwem protokołu HTTPS. Te dane pozostają w sieci szkieletowej platformy Azure.

* Dane kopii zapasowej są automatycznie szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Można także szyfrować kopie zapasowe maszyn wirtualnych z dyskami zarządzanymi w magazynie Recovery Services przy użyciu [kluczy zarządzanych przez klienta](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys) przechowywanych w Azure Key Vault. Aby włączyć szyfrowanie, nie trzeba podejmować żadnych jawnych akcji. Ma to zastosowanie do wszystkich obciążeń, których kopie zapasowe są tworzone w magazynie Recovery Services.

* Azure Backup obsługuje tworzenie kopii zapasowych i przywracanie maszyn wirtualnych platformy Azure, w których dyski systemu operacyjnego/danych są szyfrowane za pomocą Azure Disk Encryption (ADE). [Dowiedz się więcej o zaszyfrowanych maszynach wirtualnych platformy Azure i Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Ochrona danych kopii zapasowej przed przypadkowym usunięciem

Azure Backup zapewnia funkcje zabezpieczeń chroniące dane kopii zapasowej nawet po usunięciu. W przypadku usuwania nietrwałego, jeśli użytkownik usunie kopię zapasową maszyny wirtualnej, dane kopii zapasowej są przechowywane przez 14 dodatkowych dni, umożliwiając odzyskanie tego elementu kopii zapasowej bez utraty danych. Dodatkowe 14 dni przechowywania danych kopii zapasowej w stanie "usuwanie nietrwałe" nie wiążą się z kosztem dla klienta. [Dowiedz się więcej na temat usuwania nietrwałego](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitorowanie i alerty podejrzanych działań

Azure Backup udostępnia [wbudowane funkcje monitorowania i alertów](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) umożliwiające wyświetlanie i Konfigurowanie akcji dla zdarzeń związanych z Azure Backup. [Raporty kopii zapasowych](https://docs.microsoft.com/azure/backup/configure-reports) stanowią jedno miejsce docelowe do śledzenia użycia, inspekcji kopii zapasowych i przywracania oraz identyfikowania kluczowych trendów na różnych poziomach szczegółowości. Korzystając z narzędzi do monitorowania i raportowania Azure Backup, można powiadamiać użytkownika o wszelkich nieautoryzowanych, podejrzanych lub złośliwych działaniach, gdy tylko wystąpią.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Funkcje zabezpieczeń chroniące hybrydowe kopie zapasowe

Usługa Azure Backup używa agenta Microsoft Azure Recovery Services (MARS) do tworzenia kopii zapasowych i przywracania plików, folderów oraz stanu woluminu lub systemu z komputera lokalnego na platformę Azure. Usługa MARS zapewnia teraz funkcje zabezpieczeń chroniące hybrydowe kopie zapasowe. Między innymi są to następujące funkcje:

* Dodatkowa warstwa uwierzytelniania jest dodawana zawsze wtedy, gdy jest wykonywane krytyczne operacje, takie jak zmiana hasła. Ta weryfikacja polega na zapewnieniu, że operacje te mogą być wykonywane tylko przez użytkowników, którzy mają prawidłowe poświadczenia platformy Azure. [Dowiedz się więcej o funkcjach, które uniemożliwiają ataki](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks).

* Usunięte dane kopii zapasowej są przechowywane przez dodatkowe 14 dni od daty usunięcia. Zapewnia to możliwość odzyskania danych w danym okresie, dlatego nie ma utraty danych, nawet jeśli wystąpi atak. Ponadto do ochrony przed uszkodzeniem danych są utrzymywane większą liczbę punktów odzyskiwania. [Dowiedz się więcej o odzyskiwaniu usuniętych danych kopii zapasowej](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data).

* W przypadku danych utworzonych przy użyciu agenta Microsoft Azure Recovery Services (MARS) hasło służy do zapewnienia szyfrowania danych przed przekazaniem do Azure Backup i odszyfrowywane dopiero po pobraniu z Azure Backup. Szczegóły hasła są dostępne tylko dla użytkownika, który utworzył hasło, oraz agenta, który jest z nim skonfigurowany. Nic nie jest przesyłane ani udostępniane usłudze. Zapewnia to pełne bezpieczeństwo danych, ponieważ wszystkie dane, które są narażone na przypadkowe (takie jak atak typu man-in-the-Middle w sieci), nie nadają się do użytku bez hasła i hasło nie jest wysyłane w sieci.

## <a name="compliance-with-standardized-security-requirements"></a>Zgodność z znormalizowanymi wymaganiami dotyczącymi zabezpieczeń

Aby ułatwić organizacjom zgodność z krajowymi, regionalnymi i branżowymi wymaganiami dotyczącymi zbierania i używania danych indywidualnych, Microsoft Azure & Azure Backup oferuje kompleksowy zestaw certyfikatów i zaświadczania. [Zapoznaj się z listą certyfikatów zgodności](compliance-offerings.md)

## <a name="next-steps"></a>Następne kroki

* [Funkcje zabezpieczeń pomagające w ochronie obciążeń w chmurze korzystających z Azure Backup](backup-azure-security-feature-cloud.md)
* [Funkcje zabezpieczeń chroniące hybrydowe kopie zapasowe, które używają Azure Backup](backup-azure-security-feature.md)
