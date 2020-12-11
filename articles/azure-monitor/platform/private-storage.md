---
title: Korzystanie z kont magazynu zarządzanych przez klienta w programie Azure Monitor Log Analytics
description: Korzystanie z własnego konta magazynu na potrzeby scenariuszy Log Analytics
ms.subservice: logs
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: f221237bee441ec78d726dabf476d1085a27071d
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095308"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Korzystanie z kont magazynu zarządzanych przez klienta w programie Azure Monitor Log Analytics

Log Analytics opiera się na usłudze Azure Storage w różnych scenariuszach. To użycie jest zazwyczaj zarządzane automatycznie. Jednak niektóre przypadki wymagają podania konta magazynu i zarządzania nim, nazywanego również kontem magazynu zarządzanym przez klienta. Ten dokument zawiera szczegółowe informacje o sposobie użycia magazynu zarządzanego przez klienta na potrzeby pozyskiwania dzienników funkcji wad/LAD, prywatnych połączeń indywidualnych oraz szyfrowania klucza zarządzanego przez klienta (CMK). 

> [!NOTE]
> Firma Microsoft zaleca, aby nie korzystać z zależności od zawartości Log Analytics przekazanej do magazynu zarządzanego przez klienta, biorąc pod względu na to, że formatowanie i zawartość mogą ulec zmianie.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Pozyskiwanie dzienników rozszerzeń Diagnostyka Azure (funkcji wad/LAD)
Agentów rozszerzeń Diagnostyka Azure (nazywanych również funkcji wad i LAD dla agentów systemu Windows i Linux) zbierają różne Dzienniki systemu operacyjnego i przechowują je na koncie magazynu zarządzanym przez klienta. Następnie można pozyskiwać te dzienniki w Log Analytics, aby je przejrzeć i przeanalizować.
Jak zbierać dzienniki rozszerzeń Diagnostyka Azure z konta magazynu, Połącz konto magazynu z obszarem roboczym Log Analytics jako źródło danych magazynu za pomocą [Azure Portal](./diagnostics-extension-logs.md#collect-logs-from-azure-storage) lub wywołując [interfejs API usługi Storage Insights](/rest/api/loganalytics/storage%20insights/createorupdate).

Obsługiwane typy danych:
* Dziennik systemu
* Zdarzenia systemu Windows
* Service Fabric
* Zdarzenia ETW
* Dzienniki usług IIS

## <a name="using-private-links"></a>Używanie linków prywatnych
Konta magazynu zarządzane przez klienta są wymagane w niektórych przypadkach użycia, gdy prywatne linki są używane do łączenia się z zasobami Azure Monitor. Jednym z takich przypadków jest pozyskiwanie dzienników niestandardowych lub dzienników usług IIS. Te typy danych są najpierw przekazywane jako obiekty blob do konta usługi Azure Storage, a następnie przekazana do obszaru roboczego. Podobnie niektóre rozwiązania Azure Monitor mogą używać kont magazynu do przechowywania dużych plików, takich jak pliki zrzutów programu Watson, które są używane przez rozwiązanie Azure Security Center. 

##### <a name="private-link-scenarios-that-require-a-customer-managed-storage"></a>Prywatne scenariusze połączeń, które wymagają magazynu zarządzanego przez klienta
* Pozyskiwanie dzienników niestandardowych i dzienników usług IIS
* Zezwalanie na rozwiązanie ASC do zbierania plików zrzutów programu Watson

### <a name="how-to-use-a-customer-managed-storage-account-over-a-private-link"></a>Jak używać konta magazynu zarządzanego przez klienta za pośrednictwem prywatnego linku
##### <a name="workspace-requirements"></a>Wymagania dotyczące obszaru roboczego
Podczas nawiązywania połączenia z Azure Monitor za pośrednictwem prywatnego linku Agenty Log Analytics mogą wysyłać dzienniki do obszarów roboczych połączonych z siecią za pośrednictwem prywatnego linku. Ta reguła wymaga poprawnego skonfigurowania Azure Monitor obiektu zakresu linków prywatnych (AMPLS), połączenia go z obszarami roboczymi, a następnie połączenia AMPLS z siecią za pośrednictwem prywatnego linku. Aby uzyskać więcej informacji na temat procedury konfiguracji AMPLS, zobacz [bezpieczne łączenie sieci do Azure monitor za pomocą prywatnego linku platformy Azure](./private-link-security.md). 
##### <a name="storage-account-requirements"></a>Wymagania konta magazynu
Aby konto magazynu zostało pomyślnie połączone z prywatnym łączem, musi:
* Znajdować się w sieci wirtualnej lub komunikacji równorzędnej i połączonej z siecią wirtualną za pośrednictwem prywatnego linku. Dzięki temu agenci w sieci wirtualnej mogą wysyłać dzienniki do konta magazynu.
* Znajdować się w tym samym regionie, w którym znajduje się obszar roboczy, z którym jest połączony.
* Zezwól Azure Monitor na dostęp do konta magazynu. Jeśli wybrano opcję zezwalania tylko wybranym sieci na dostęp do konta magazynu, należy również zezwolić na ten wyjątek: "Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu". Umożliwia to Log Analytics odczytywanie dzienników pozyskiwanych na tym koncie magazynu.
* Jeśli obszar roboczy obsługuje również ruch z innych sieci, należy skonfigurować konto magazynu tak, aby zezwalało na ruch przychodzący z odpowiednich sieci/Internetu.

##### <a name="link-your-storage-account-to-a-log-analytics-workspace"></a>Łączenie konta magazynu z obszarem roboczym Log Analytics
Możesz połączyć konto magazynu z obszarem roboczym za pośrednictwem interfejsu [wiersza polecenia platformy Azure](/cli/azure/monitor/log-analytics/workspace/linked-storage) lub [interfejs API REST](/rest/api/loganalytics/linkedstorageaccounts). Odpowiednie wartości DataSourceType:
* CustomLogs — do korzystania z magazynu dla dzienników niestandardowych i dzienników usług IIS podczas pozyskiwania.
* AzureWatson — Użyj usługi Storage dla plików zrzutu programu Watson przekazanych przez rozwiązanie ASC (Azure Security Center). Aby uzyskać więcej informacji o zarządzaniu przechowywaniem, zastępowaniu połączonego konta magazynu i monitorowaniu działania konta magazynu, zobacz [Zarządzanie kontami połączonego magazynu](#managing-linked-storage-accounts). 

## <a name="encrypting-data-with-cmk"></a>Szyfrowanie danych za pomocą CMK
Usługa Azure Storage szyfruje wszystkie dane przechowywane na koncie magazynu. Domyślnie szyfruje dane za pomocą kluczy zarządzanych przez firmę Microsoft (MMK). Jednak usługa Azure Storage będzie mogła używać klucza zarządzanego przez klienta (CMK) z magazynu kluczy platformy Azure do szyfrowania danych magazynu. Możesz zaimportować własne klucze do Azure Key Vault lub użyć interfejsów API Azure Key Vault do wygenerowania kluczy.
##### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>Scenariusze CMK, które wymagają konta magazynu zarządzanego przez klienta
* Szyfrowanie zapytań alertów dziennika za pomocą CMK
* Szyfrowanie zapisanych zapytań za pomocą CMK

### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>Jak zastosować CMK do kont magazynu zarządzanych przez klienta
##### <a name="storage-account-requirements"></a>Wymagania konta magazynu
Konto magazynu i Magazyn kluczy muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat szyfrowania i zarządzania kluczami usługi Azure Storage, zobacz [szyfrowanie usługi Azure Storage dla danych przechowywanych w spoczynku](../../storage/common/storage-service-encryption.md).

##### <a name="apply-cmk-to-your-storage-accounts"></a>Zastosuj CMK do kont magazynu
Aby skonfigurować konto usługi Azure Storage do korzystania z kluczy zarządzanych przez klienta z Azure Key Vault, użyj [Azure Portal](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [programu PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) lub [interfejsu wiersza polecenia](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="managing-linked-storage-accounts"></a>Zarządzanie kontami połączonych magazynów

Aby połączyć lub odłączyć konta magazynu w obszarze roboczym, użyj interfejsu [wiersza polecenia platformy Azure](/cli/azure/monitor/log-analytics/workspace/linked-storage) lub [API REST](/rest/api/loganalytics/linkedstorageaccounts).

##### <a name="create-or-modify-a-link"></a>Tworzenie lub modyfikowanie linku
Po połączeniu konta magazynu z obszarem roboczym Log Analytics rozpocznie korzystanie z niego zamiast konta magazynu należącego do usługi. Można 
* Rejestrowanie wielu kont magazynu w celu rozłożenia obciążenia dzienników między nimi
* Ponownie Użyj tego samego konta magazynu dla wielu obszarów roboczych

##### <a name="unlink-a-storage-account"></a>Odłączanie konta magazynu
Aby zatrzymać korzystanie z konta magazynu, odłącz magazyn od obszaru roboczego. Odłączanie wszystkich kont magazynu z obszaru roboczego oznacza, że Log Analytics spróbuje polegać na kontach magazynu zarządzanych przez usługę. Jeśli sieć ma ograniczony dostęp do Internetu, te magazyny mogą być niedostępne, a żaden scenariusz, który opiera się na magazynie, zakończy się niepowodzeniem.

##### <a name="replace-a-storage-account"></a>Zastępowanie konta magazynu
Aby zastąpić konto magazynu używane na potrzeby pozyskiwania,
1.  **Utwórz link do nowego konta magazynu.** Agenci rejestrowania otrzymają zaktualizowaną konfigurację i rozpoczną wysyłanie danych do nowego magazynu. Proces może potrwać kilka minut.
2.  **Następnie odłącz stare konto magazynu, aby agenci zatrzymali zapisywanie na usuniętym koncie.** Proces pozyskiwania przechowuje dane z tego konta do momentu jego pozyskania. Nie usuwaj konta magazynu, dopóki nie zostaną wyświetlone wszystkie dzienniki.

### <a name="maintaining-storage-accounts"></a>Obsługa kont magazynu
##### <a name="manage-log-retention"></a>Zarządzanie przechowywaniem dzienników
W przypadku korzystania z własnego konta magazynu przechowywanie jest możliwe. Innymi słowy Log Analytics nie usuwa dzienników przechowywanych w magazynie prywatnym. Zamiast tego należy skonfigurować zasady, aby obsłużyć obciążenie zgodnie z preferencjami.

##### <a name="consider-load"></a>Rozważ obciążenie
Konta magazynu mogą obsługiwać pewne obciążenie żądań odczytu i zapisu przed rozpoczęciem ograniczania żądań (zobacz [elementy docelowe skalowalności i wydajności dla usługi BLOB Storage,](../../storage/common/scalability-targets-standard-account.md) Aby uzyskać więcej informacji). Ograniczanie przepustowości ma wpływ na czas pobierania dzienników. Jeśli konto magazynu jest przeciążone, zarejestruj dodatkowe konto magazynu, aby rozłożyć obciążenie między nimi. W celu monitorowania pojemności i wydajności konta magazynu Sprawdź [szczegółowe informacje w Azure Portal]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Powiązane opłaty
Konta magazynu są rozliczone przez ilość przechowywanych danych, typ magazynu i typ nadmiarowości. Aby uzyskać szczegółowe informacje, zobacz [Cennik usługi Block BLOB](https://azure.microsoft.com/pricing/details/storage/blobs) i [Cennik Table Storage](https://azure.microsoft.com/pricing/details/storage/tables).


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [korzystaniu z prywatnego linku platformy Azure w celu bezpiecznego łączenia sieci z Azure monitor](private-link-security.md)
- Informacje o [Azure monitor kluczy zarządzanych przez klienta](customer-managed-keys.md)
