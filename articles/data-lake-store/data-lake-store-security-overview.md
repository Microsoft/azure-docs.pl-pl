---
title: Omówienie zabezpieczeń w usłudze Azure Data Lake Storage Gen1 | Dokumenty firmy Microsoft
description: Dowiedz się, jak usługa Azure Data Lake Storage Gen1 jest bezpieczniejszym magazynem dużych zbiorów danych
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: twooley
ms.openlocfilehash: 7e987c56c3a125a03e3a90540313ace1f8adf47a
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086576"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Zabezpieczenia w usłudze Azure Data Storage Gen1

Wiele przedsiębiorstw korzysta z analizy dużych zbiorów danych w celu uzyskania szczegółowych informacji biznesowych, aby pomóc im w podejmowaniu mądrych decyzji. Organizacja może mieć złożone i regulowane środowisko, z rosnącą liczbą różnych użytkowników. Ważne jest, aby przedsiębiorstwo upewniło się, że krytyczne dane biznesowe są przechowywane bezpieczniej, przy czym odpowiedni poziom dostępu jest przyznawany poszczególnym użytkownikom. Usługa Azure Data Lake Storage Gen1 została zaprojektowana w celu spełnienia tych wymagań dotyczących zabezpieczeń. W tym artykule dowiesz się o możliwościach zabezpieczeń usługi Data Lake Storage Gen1, w tym:

* Authentication
* Autoryzacja
* Izolacja sieciowa
* Ochrona danych
* Inspekcja

## <a name="authentication-and-identity-management"></a>Uwierzytelnianie i zarządzanie tożsamościami

Uwierzytelnianie jest procesem, w którym tożsamość użytkownika jest weryfikowana, gdy użytkownik wchodzi w interakcję z data lake storage gen1 lub z dowolną usługą, która łączy się z Data Lake Storage Gen1. W przypadku zarządzania tożsamościami i uwierzytelniania usługa Data Lake Storage Gen1 używa [usługi Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)— kompleksowego rozwiązania chmury zarządzania tożsamościami i dostępem, które upraszcza zarządzanie użytkownikami i grupami.

Każda subskrypcja platformy Azure może być skojarzona z wystąpieniem usługi Azure Active Directory. Tylko użytkownicy i tożsamości usługi, które są zdefiniowane w usłudze Azure Active Directory można uzyskać dostęp do konta Usługi Data Lake Storage Gen1, za pomocą witryny Azure portal, narzędzia wiersza polecenia lub za pośrednictwem aplikacji klienckich organizacji tworzy przy użyciu data lake storage Gen1 SDK. Kluczowe zalety korzystania z usługi Azure Active Directory jako scentralizowanego mechanizmu kontroli dostępu to:

* Uproszczone zarządzanie cyklem życia tożsamości. Tożsamość użytkownika lub usługi (tożsamości głównej usługi) można szybko utworzyć i szybko odwołać, po prostu usuwając lub wyłączając konto w katalogu.
* Uwierzytelnianie wieloskładnikowe. [Uwierzytelnianie wieloskładnikowe](../active-directory/authentication/multi-factor-authentication.md) zapewnia dodatkową warstwę zabezpieczeń dla logowania użytkowników i transakcji.
* Uwierzytelnianie od dowolnego klienta za pośrednictwem standardowego otwartego protokołu, takiego jak OAuth lub OpenID.
* Federacja z usługami katalogowymi przedsiębiorstwa i dostawcami tożsamości w chmurze.

## <a name="authorization-and-access-control"></a>Autoryzacja i kontrola dostępu

Po usłudze Azure Active Directory uwierzytelnia użytkownika, dzięki czemu użytkownik może uzyskać dostęp do magazynu usługi Data Lake Gen1, autoryzacja kontroluje uprawnienia dostępu dla usługi Data Lake Storage Gen1. Data Lake Storage Gen1 oddziela autoryzację dla działań związanych z kontem i związanych z danymi w następujący sposób:

* [Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md) (RBAC) dostarczana przez platformę Azure w celu zarządzania kontami
* POSIX ACL do uzyskiwania dostępu do danych w magazynie

### <a name="rbac-for-account-management"></a>RBAC do zarządzania kontem

Domyślnie zdefiniowano cztery podstawowe role dla usługi Data Lake Storage Gen1. Role zezwalają na różne operacje na koncie Data Lake Storage Gen1 za pośrednictwem witryny Azure portal, poleceń cmdlet programu PowerShell i interfejsów API REST. Rola właściciela i współautora może wykonywać różne funkcje administracyjne na koncie. Rolę Czytelnik można przypisać użytkownikom, którzy wyświetlają tylko dane zarządzania kontem.

![Role RBAC](./media/data-lake-store-security-overview/rbac-roles.png "Role RBAC")

Należy zauważyć, że chociaż role są przypisane do zarządzania kontem, niektóre role wpływają na dostęp do danych. Listy ACL należy używać do kontrolowania dostępu do operacji, które użytkownik może wykonywać w systemie plików. W poniższej tabeli przedstawiono podsumowanie praw do zarządzania i praw dostępu do danych dla ról domyślnych.

| Role | Prawa do zarządzania | Prawa dostępu do danych | Wyjaśnienie |
| --- | --- | --- | --- |
| Nie przypisano żadnej roli |Brak |Regulowane przez ACL |Użytkownik nie może używać poleceń cmdlet witryny Azure Portal lub Azure PowerShell do przeglądania usługi Data Lake Storage Gen1. Użytkownik może używać tylko narzędzi wiersza polecenia. |
| Właściciel |Wszystkie |Wszystkie |Rola Właściciel jest superużytnikiem. Ta rola może zarządzać wszystkim i ma pełny dostęp do danych. |
| Czytelnik |Tylko odczyt |Regulowane przez ACL |Rola Czytelnika może wyświetlać wszystko dotyczące zarządzania kontem, na przykład który użytkownik jest przypisany do której roli. Rola Czytelnik nie może wprowadzać żadnych zmian. |
| Współautor |Wszystkie z wyjątkiem dodawania i usuwania ról |Regulowane przez ACL |Rola współautora może zarządzać niektórymi aspektami konta, takimi jak wdrożenia i tworzenie alertów i zarządzanie nimi. Rola Współautor nie może dodawać ani usuwać ról. |
| Administrator dostępu użytkowników |Dodawanie i usuwanie ról |Regulowane przez ACL |Rola Administrator dostępu użytkownika może zarządzać dostępem użytkowników do kont. |

Aby uzyskać instrukcje, zobacz [Przypisywanie użytkowników lub grup zabezpieczeń do kont Data Lake Storage Gen1](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Używanie list ACL do operacji w systemach plików

Data Lake Storage Gen1 to hierarchiczny system plików, taki jak Hadoop Distributed File System (HDFS) i obsługuje [listy POSIX.](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists) Steruje odczytem (r), zapisem (w) i wykonywaniem (x) uprawnień do zasobów dla roli Właściciel, dla grupy Właściciele oraz dla innych użytkowników i grup. W umiar pamięci masowej usługi Data Lake Gen1 listy ACL można włączyć w folderze głównym, w podfolderach i w poszczególnych plikach. Aby uzyskać więcej informacji na temat działania list ACL w kontekście pamięci masowej Data Lake Storage Gen1, zobacz [Kontrola dostępu w programie Data Lake Storage Gen1](data-lake-store-access-control.md).

Zaleca się definiowanie list ACL dla wielu użytkowników przy użyciu [grup zabezpieczeń](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Dodaj użytkowników do grupy zabezpieczeń, a następnie przypisz listy ACL dla pliku lub folderu do tej grupy zabezpieczeń. Jest to przydatne, gdy chcesz podać przypisane uprawnienia, ponieważ są ograniczone do maksymalnie 28 wpisów dla przypisanych uprawnień. Aby uzyskać więcej informacji na temat lepszego zabezpieczania danych przechowywanych w usłudze Data Lake Storage Gen1 przy użyciu grup zabezpieczeń usługi Azure Active Directory, zobacz [Przypisywanie użytkowników lub grupy zabezpieczeń jako list ACL do systemu plików Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

![Uprawnienia dostępu do listy](./media/data-lake-store-security-overview/adl.acl.2.png "Uprawnienia dostępu do listy")

## <a name="network-isolation"></a>Izolacja sieciowa

Użyj usługi Data Lake Storage Gen1, aby kontrolować dostęp do magazynu danych na poziomie sieci. Można ustanowić zapory i zdefiniować zakres adresów IP dla zaufanych klientów. W przypadku zakresu adresów IP tylko klienci, którzy mają adres IP w zdefiniowanym zakresie, mogą łączyć się z gen1 magazynu usługi Data Lake.

![Ustawienia zapory i dostęp do adresu IP](./media/data-lake-store-security-overview/firewall-ip-access.png "Ustawienia zapory i adres IP")

Tagi usługi sieci wirtualnej platformy Azure dla usługi Data Lake Gen 1. Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów objętymi tagiem usługi i automatycznie aktualizuje tag usługi w miarę zmiany adresów. Aby uzyskać więcej informacji, zobacz [omówienie tagów usługi platformy Azure](../virtual-network/service-tags-overview.md).

## <a name="data-protection"></a>Ochrona danych

Data Lake Storage Gen1 chroni twoje dane przez cały okres ich życia. W przypadku przesyłanych danych usługa Data Lake Storage Gen1 używa standardowego protokołu TLS 1.2 (Transport Layer Security) do zabezpieczania danych w sieci.

![Szyfrowanie w umiań przechowywania usługi Data Lake](./media/data-lake-store-security-overview/adls-encryption.png "Szyfrowanie w umiań przechowywania usługi Data Lake")

Data Lake Storage Gen1 zapewnia również szyfrowanie danych przechowywanych na koncie. Możesz wybrać szyfrowanie danych lub jego brak. Jeśli zdecydujesz się na szyfrowanie, dane przechowywane w programie Data Lake Storage Gen1 są szyfrowane przed zapisaniem na nośniku trwałym. W takim przypadku Data Lake Storage Gen1 automatycznie szyfruje dane przed utrwalaniem i odszyfrowywaniem danych przed pobraniem, dzięki czemu jest całkowicie przezroczysty dla klienta uzyskującego dostęp do danych. Nie ma żadnych zmian kodu wymagane po stronie klienta do szyfrowania/odszyfrowywania danych.

W celu zarządzania kluczami usługa Data Lake Storage Gen1 udostępnia dwa tryby zarządzania głównymi kluczami szyfrowania (MEK), które są wymagane do odszyfrowywania wszelkich danych przechowywanych w umiań magazynu usługi Data Lake Gen1. Możesz pozwolić usługi Data Lake Storage Gen1 zarządzać meks dla Ciebie lub wybrać, aby zachować własność MEK przy użyciu konta usługi Azure Key Vault. Określić tryb zarządzania kluczami podczas tworzenia konta Usługi Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat dostarczania konfiguracji związanej z szyfrowaniem, zobacz [Wprowadzenie do usługi Azure Data Lake Storage Gen1 przy użyciu witryny Azure Portal.](data-lake-store-get-started-portal.md)

## <a name="activity-and-diagnostic-logs"></a>Dzienniki aktywności i diagnostyczne

Dzienniki aktywności lub diagnostyki można używać, w zależności od tego, czy szukasz dzienników dla działań związanych z zarządzaniem kontem, czy działań związanych z danymi.

* Działania związane z zarządzaniem kontami używają interfejsów API usługi Azure Resource Manager i są dostępne w witrynie Azure portal za pośrednictwem dzienników aktywności.
* Działania związane z danymi używają interfejsów API REST systemu WebHDFS i są dostępne w witrynie Azure portal za pomocą dzienników diagnostycznych.

### <a name="activity-log"></a>Dziennik aktywności

Aby zapewnić zgodność z przepisami, organizacja może wymagać odpowiednich śladów inspekcji działań związanych z zarządzaniem kontem, jeśli musi zagłębić się w określone zdarzenia. Data Lake Storage Gen1 ma wbudowane monitorowanie i rejestruje wszystkie działania związane z zarządzaniem kontem.

W przypadku śladów inspekcji zarządzania kontem wyświetl i wybierz kolumny, które chcesz zarejestrować. Można również eksportować dzienniki aktywności do usługi Azure Storage.

![Dziennik aktywności](./media/data-lake-store-security-overview/activity-logs.png "Dziennik aktywności")

Aby uzyskać więcej informacji na temat pracy z dziennikami działań, zobacz [Wyświetlanie dzienników aktywności w celu inspekcji akcji dotyczących zasobów](../azure-resource-manager/management/view-activity-logs.md).

### <a name="diagnostics-logs"></a>Dzienniki diagnostyczne

Inspekcja dostępu do danych i rejestrowanie diagnostyczne można włączyć w witrynie Azure portal i wysłać dzienniki do konta magazynu obiektów Blob platformy Azure, centrum zdarzeń lub dzienników usługi Azure Monitor.

![Dzienniki diagnostyczne](./media/data-lake-store-security-overview/diagnostic-logs.png "Dzienniki diagnostyczne")

Aby uzyskać więcej informacji na temat pracy z dziennikami diagnostycznymi za pomocą usługi Data Lake Storage Gen1, zobacz [Uzyskiwanie dostępu do dzienników diagnostycznych dla pamięci masowej usługi Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Podsumowanie

Klienci korporacyjni wymagają bezpiecznej i łatwej w użyciu platformy chmury analizy danych. Usługa Data Lake Storage Gen1 została zaprojektowana w celu rozwiązania tych wymagań za pomocą zarządzania tożsamościami i uwierzytelniania za pośrednictwem integracji usługi Azure Active Directory, autoryzacji opartej na acl, izolacji sieci, szyfrowania danych podczas przesyłania i przechowywania oraz inspekcji.

Jeśli chcesz zobaczyć nowe funkcje w Data Lake Storage Gen1, wyślij nam swoją opinię na [forum Data Lake Storage Gen1 UserVoice](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Zobacz też

* [Omówienie usługi Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Wprowadzenie do usługi Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
