---
title: Omówienie zabezpieczeń w Azure Data Lake Storage Gen1 | Microsoft Docs
description: Dowiedz się więcej o możliwościach zabezpieczeń Azure Data Lake Storage Gen1, takich jak uwierzytelnianie, autoryzacja, izolacja sieci, ochrona danych i inspekcja.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: twooley
ms.openlocfilehash: 240018381a3139a6378141d78514e43ae469de5d
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146299"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Zabezpieczenia w Azure Data Lake Storage Gen1

Wiele firm korzysta z analizy danych Big Data, aby ułatwić im podejmowanie szybkich decyzji. Organizacja może mieć złożone i regulowane środowisko, zwiększając liczbę różnych użytkowników. Firma może mieć pewność, że krytyczne dane biznesowe są bezpiecznie przechowywane przy użyciu odpowiedniego poziomu dostępu do poszczególnych użytkowników. Azure Data Lake Storage Gen1 jest zaprojektowana, aby pomóc spełnić te wymagania dotyczące zabezpieczeń. W tym artykule omówiono możliwości zabezpieczeń Data Lake Storage Gen1, w tym:

* Authentication
* Autoryzacja
* Izolacja sieciowa
* Ochrona danych
* Inspekcja

## <a name="authentication-and-identity-management"></a>Uwierzytelnianie i zarządzanie tożsamościami

Uwierzytelnianie to proces polegający na tym, że tożsamość użytkownika jest weryfikowana, gdy użytkownik współdziała z Data Lake Storage Gen1 lub dowolną usługą, która łączy się z Data Lake Storage Gen1. Aby zarządzać tożsamościami i ich uwierzytelnianiem, Data Lake Storage Gen1 używa [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), kompleksowego rozwiązania do zarządzania tożsamościami i dostępem w chmurze, które upraszcza zarządzanie użytkownikami i grupami.

Każdą subskrypcję platformy Azure można skojarzyć z wystąpieniem Azure Active Directory. Tylko tożsamości użytkowników i usług zdefiniowane w usłudze Azure Active Directory mogą uzyskiwać dostęp do konta Data Lake Storage Gen1 przy użyciu Azure Portal, narzędzi wiersza polecenia lub przez aplikacje klienckie, które organizacja kompiluje przy użyciu zestawu SDK Data Lake Storage Gen1. Najważniejsze zalety korzystania z Azure Active Directory jako scentralizowanego mechanizmu kontroli dostępu są następujące:

* Uproszczone zarządzanie cyklem życia tożsamości. Tożsamość użytkownika lub usługi (tożsamość główna usługi) można szybko utworzyć i szybko odwołać, po prostu usuwając lub wyłączając konto w katalogu.
* Uwierzytelnianie wieloskładnikowe. [Uwierzytelnianie wieloskładnikowe](../active-directory/authentication/concept-mfa-howitworks.md) stanowi dodatkową warstwę zabezpieczeń w przypadku logowania i transakcji użytkowników.
* Uwierzytelnianie od dowolnego klienta za pośrednictwem standardowego protokołu otwartego, takiego jak OAuth lub OpenID Connect.
* Federacja z usługami katalogowymi przedsiębiorstwa i dostawcami tożsamości w chmurze.

## <a name="authorization-and-access-control"></a>Autoryzacja i kontrola dostępu

Po Azure Active Directory uwierzytelnia użytkownika, tak aby użytkownik mógł uzyskać dostęp do Data Lake Storage Gen1, autoryzacja kontroluje uprawnienia dostępu do Data Lake Storage Gen1. Data Lake Storage Gen1 oddziela autoryzację dla działań związanych z kontem i danymi w następujący sposób:

* [Kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md) na potrzeby zarządzania kontami
* Lista ACL POSIX do uzyskiwania dostępu do danych w sklepie

### <a name="azure-rbac-for-account-management"></a>Azure RBAC do zarządzania kontami

Cztery role podstawowe są domyślnie zdefiniowane dla Data Lake Storage Gen1. Role umożliwiają wykonywanie różnych operacji na koncie Data Lake Storage Gen1 za pośrednictwem Azure Portal, poleceń cmdlet programu PowerShell i interfejsów API REST. Role właściciela i współautora mogą wykonywać na koncie różne funkcje administracyjne. Rolę czytelnika można przypisać do użytkowników, którzy wyświetlają tylko dane zarządzania kontami.

![Role platformy Azure](./media/data-lake-store-security-overview/rbac-roles.png "Role platformy Azure")

Należy pamiętać, że chociaż role są przypisane do zarządzania kontami, niektóre role mają wpływ na dostęp do danych. Aby kontrolować dostęp do operacji, które użytkownik może wykonywać w systemie plików, należy użyć list ACL. W poniższej tabeli przedstawiono podsumowanie praw zarządzania i praw dostępu do danych dla ról domyślnych.

| Role | Prawa do zarządzania | Prawa dostępu do danych | Objaśnienie |
| --- | --- | --- | --- |
| Nie przypisano żadnej roli |Brak |Regulowane przez listę kontroli dostępu |Użytkownik nie może użyć poleceń cmdlet Azure Portal lub Azure PowerShell do przeglądania Data Lake Storage Gen1. Użytkownik może używać tylko narzędzi wiersza polecenia. |
| Właściciel |Wszystko |Wszystko |Rola właściciela to administratora. Ta rola może zarządzać wszystkimi elementami i ma pełny dostęp do danych. |
| Czytelnik |Tylko odczyt |Regulowane przez listę kontroli dostępu |Rola czytelnik może wyświetlać wszystko dotyczące zarządzania kontami, na przykład tego, który użytkownik jest przypisany do danej roli. Rola czytelnik nie może wprowadzać żadnych zmian. |
| Współautor |Wszystkie z wyjątkiem dodawania i usuwania ról |Regulowane przez listę kontroli dostępu |Rola współautor może zarządzać niektórymi aspektami konta, takimi jak wdrożenia i tworzenie alertów oraz zarządzanie nimi. Rola współautor nie może dodawać ani usuwać ról. |
| Administrator dostępu użytkowników |Dodawanie i usuwanie ról |Regulowane przez listę kontroli dostępu |Rola Administrator dostępu użytkowników może zarządzać dostępem użytkowników do kont. |

Aby uzyskać instrukcje, zobacz [przypisywanie użytkowników lub grup zabezpieczeń do kont Data Lake Storage Gen1](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Korzystanie z list ACL dla operacji w systemach plików

Data Lake Storage Gen1 to hierarchiczny system plików, taki jak Hadoop rozproszony system plików (HDFS) i obsługują [listy kontroli dostępu POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Kontroluje uprawnienia Odczyt (r), zapis (w) i Execute (x) do zasobów dla roli właściciela, grupy Właściciele oraz dla innych użytkowników i grup. W Data Lake Storage Gen1 listy ACL można włączyć w folderze głównym, w podfolderach i w poszczególnych plikach. Aby uzyskać więcej informacji na temat działania list ACL w kontekście Data Lake Storage Gen1, zobacz [Kontrola dostępu w Data Lake Storage Gen1](data-lake-store-access-control.md).

Zalecamy zdefiniowanie list ACL dla wielu użytkowników przy użyciu [grup zabezpieczeń](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Dodaj użytkowników do grupy zabezpieczeń, a następnie przypisz listy ACL dla pliku lub folderu do tej grupy zabezpieczeń. Jest to przydatne, gdy chcesz zapewnić przypisane uprawnienia, ponieważ ograniczono do 28 wpisów dla przypisanych uprawnień. Aby uzyskać więcej informacji o sposobach lepszego zabezpieczania danych przechowywanych w Data Lake Storage Gen1 przy użyciu Azure Active Directory grup zabezpieczeń, zobacz [przypisywanie użytkowników lub grup zabezpieczeń jako list ACL do systemu plików Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

![Wyświetlanie listy uprawnień dostępu](./media/data-lake-store-security-overview/adl.acl.2.png "Wyświetlanie listy uprawnień dostępu")

## <a name="network-isolation"></a>Izolacja sieciowa

Użyj Data Lake Storage Gen1, aby pomóc kontrolować dostęp do magazynu danych na poziomie sieci. Można nawiązywać zapory i definiować zakres adresów IP dla zaufanych klientów. Z zakresem adresów IP tylko klienci, którzy mają adres IP w zdefiniowanym zakresie, mogą łączyć się z Data Lake Storage Gen1.

![Ustawienia zapory i dostęp do protokołu IP](./media/data-lake-store-security-overview/firewall-ip-access.png "Ustawienia zapory i adres IP")

Tagi usługi Azure Virtual Networks (VNet) obsługują Data Lake Gen 1. Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy. Aby uzyskać więcej informacji, zobacz [Omówienie tagów usługi platformy Azure](../virtual-network/service-tags-overview.md).

## <a name="data-protection"></a>Ochrona danych

Data Lake Storage Gen1 chroni dane w całym cyklu życia. W przypadku przesyłanych danych do zabezpieczania danych za pośrednictwem sieci Data Lake Storage Gen1 używa protokołu TLS 1,2 (Industry Transport Layer Security).

![Szyfrowanie w Data Lake Storage Gen1](./media/data-lake-store-security-overview/adls-encryption.png "Szyfrowanie w Data Lake Storage Gen1")

Data Lake Storage Gen1 również zapewnia szyfrowanie danych przechowywanych na koncie. Możesz wybrać szyfrowanie danych lub jego brak. W przypadku wybrania opcji szyfrowania dane przechowywane w Data Lake Storage Gen1 są szyfrowane przed zapisaniem na nośniku trwałym. W takim przypadku Data Lake Storage Gen1 automatycznie szyfruje dane przed utrwaleniem i odszyfrowuje dane przed pobraniem, dzięki czemu są one całkowicie niewidoczne dla klienta, który uzyskuje dostęp do danych. Po stronie klienta nie jest wymagana zmiana kodu do szyfrowania/odszyfrowywania danych.

W przypadku zarządzania kluczami Data Lake Storage Gen1 zapewnia dwa tryby zarządzania głównymi kluczami szyfrowania (głównymi kluczami szyfrowania), które są wymagane do odszyfrowania danych przechowywanych w Data Lake Storage Gen1. Możesz pozwolić, Data Lake Storage Gen1 zarządzać głównymi kluczami szyfrowaniaem, lub wybrać opcję zachowania własności głównymi kluczami szyfrowania przy użyciu konta Azure Key Vault. Podczas tworzenia konta Data Lake Storage Gen1 należy określić tryb zarządzania kluczami. Aby uzyskać więcej informacji na temat zapewniania konfiguracji związanej z szyfrowaniem, zobacz Wprowadzenie do [Azure Data Lake Storage Gen1 przy użyciu witryny Azure Portal](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Dzienniki aktywności i diagnostyczne

W zależności od tego, czy szukasz dzienników dla działań związanych z zarządzaniem kontem lub działań związanych z danymi, można użyć dzienników aktywności lub diagnostyki.

* Działania związane z zarządzaniem kontem używają Azure Resource Manager interfejsów API i są uwzględniane w Azure Portal za pomocą dzienników aktywności.
* Działania związane z danymi korzystają z interfejsów API REST WebHDFS i są one opisane w Azure Portal za pośrednictwem dzienników diagnostycznych.

### <a name="activity-log"></a>Dziennik aktywności

Aby zapewnić zgodność z przepisami, organizacja może wymagać odpowiednich śladów inspekcji działań związanych z zarządzaniem kontami, jeśli konieczna będzie DIG do określonych zdarzeń. Data Lake Storage Gen1 ma wbudowane monitorowanie i rejestruje wszystkie działania związane z zarządzaniem kontem.

W przypadku dzienników inspekcji zarządzania kontami Wyświetl i wybierz kolumny, które chcesz zarejestrować. Możesz również eksportować dzienniki aktywności do usługi Azure Storage.

![Dziennik aktywności](./media/data-lake-store-security-overview/activity-logs.png "Dziennik aktywności")

Aby uzyskać więcej informacji na temat pracy z dziennikami aktywności, zobacz [Wyświetlanie dzienników aktywności w celu inspekcji akcji na zasobach](../azure-resource-manager/management/view-activity-logs.md).

### <a name="diagnostics-logs"></a>Dzienniki diagnostyczne

Można włączyć inspekcję dostępu do danych i rejestrowanie diagnostyczne w Azure Portal i wysłać dzienniki do konta usługi Azure Blob Storage, centrum zdarzeń lub dzienników Azure Monitor.

![Dzienniki diagnostyczne](./media/data-lake-store-security-overview/diagnostic-logs.png "Dzienniki diagnostyczne")

Aby uzyskać więcej informacji na temat pracy z dziennikami diagnostycznymi przy użyciu Data Lake Storage Gen1, zobacz [Uzyskiwanie dostępu do dzienników diagnostycznych dla Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Podsumowanie

Klienci korporacyjni wymagają platformy w chmurze do analizy danych, która jest bezpieczna i łatwa w użyciu. Data Lake Storage Gen1 zaprojektowano w celu ułatwienia rozwiązywania tych wymagań za pomocą zarządzania tożsamościami i uwierzytelniania za pośrednictwem integracji Azure Active Directory, autoryzacji opartej na listach ACL, izolacji sieci, szyfrowania danych podczas przesyłania i przechowywania oraz inspekcji.

Jeśli chcesz zobaczyć nowe funkcje w Data Lake Storage Gen1, Wyślij nam swoją opinię na [forum Data Lake Storage Gen1 UserVoice](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Zobacz też

* [Omówienie Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Wprowadzenie do Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)