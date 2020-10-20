---
title: Co nowego w Azure Key Vault
description: Najnowsze aktualizacje dla Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: fac5fad51137cd08f2498db132768263a770430d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203878"
---
# <a name="whats-new-for-azure-key-vault"></a>Co nowego w Azure Key Vault

W tym miejscu nowości nowe Azure Key Vault. Nowe funkcje i ulepszenia są również ogłoszone w [kanale usługi Azure updates Key Vault](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="july-2020"></a>Lipiec 2020 r.

> [!WARNING]
> Te dwie aktualizacje mają możliwość wpływu na implementacje Azure Key Vault.

### <a name="soft-delete-on-by-default"></a>Domyślnie Usuń nietrwałe

Przed końcem 2020, **usuwanie nietrwałe będzie domyślnie włączone dla wszystkich magazynów kluczy**, nowych i istniejących wcześniej. Aby uzyskać szczegółowe informacje na temat tej potencjalnej zmiany, a także kroki znajdowania odpowiednich magazynów kluczy i zaktualizować je wcześniej, zobacz artykuł [soft-DELETE zostanie włączony dla wszystkich magazynów kluczy](soft-delete-change.md). 

### <a name="azure-tls-certificate-changes"></a>Zmiany certyfikatu protokołu TLS platformy Azure  

Firma Microsoft aktualizuje usługi platformy Azure, aby używać certyfikatów TLS z innego zestawu głównych urzędów certyfikacji (CA). Ta zmiana jest wykonywana, ponieważ bieżące certyfikaty urzędu certyfikacji [nie są zgodne z jednym z wymagań linii bazowej urzędu certyfikacji/przeglądarki](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951).

### <a name="when-will-this-change-happen"></a>Kiedy to nastąpi zmiana?

- Usługi [Azure Active Directory](/azure/active-directory) (Azure AD) zaczęły to przechodzenie od 7 lipca 2020.
- Wszystkie nowo utworzone punkty końcowe protokołu TLS/SSL usługi Azure zawierają zaktualizowane certyfikaty łańcucha do nowych głównych urzędów certyfikacji.
- Istniejące punkty końcowe platformy Azure będą przenoszone w sposób fazowy od 13 sierpnia 2020.
- [Usługi Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) i [DPS](/azure/iot-dps/) POzostaną w głównym urzędzie certyfikacji Baltimore CyberTrust, ale ich pośrednie urzędy certyfikacji zmienią się. Aby uzyskać szczegółowe informacje, zobacz obiekt BLOB post [usługi Azure IoT TLS: zmiany są dostępne. (... i dlaczego jest to ważne)](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).
- [Usługa Azure Storage](/azure/storage) pozostanie w głównym urzędzie certyfikacji Baltimore CyberTrust, ale ich pośrednie urzędy certyfikacji zmienią się. Aby uzyskać szczegółowe informacje, zobacz temat [Magazyn usługi Azure Storage TLS: zmiany zostały wprowadzone. (... i dlaczego jest to ważne)](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).

> [!IMPORTANT]
> Aby uniknąć błędów łączności podczas próby nawiązania połączenia z usługami platformy Azure, klienci mogą wymagać aktualizacji aplikacji po tej zmianie.

### <a name="what-is-changing"></a>Co się zmieni?

Dzisiaj większość certyfikatów protokołu TLS używanych przez łańcuch usług platformy Azure ma następujący główny urząd certyfikacji:

| Nazwa pospolita urzędu certyfikacji | Odcisk palca (SHA1) |
|--|--|
| [Baltimore CyberTrust, główny](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Certyfikaty TLS używane przez usługi platformy Azure będą łańcucha do jednego z następujących głównych urzędów certyfikacji:

| Nazwa pospolita urzędu certyfikacji | Odcisk palca (SHA1) |
|--|--|
| [DigiCert Global root — G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [Globalny główny urząd certyfikacji DigiCert](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust, główny](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D — zaufanie klasy głównej 3 urzędu certyfikacji 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Główny urząd certyfikacji RSA firmy Microsoft 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Główny urząd certyfikacji Microsoft EV ECC 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20EV%20ECC%20Root%20Certificate%20Authority%202017.crt) | 6b1937abfd64e1e40daf2262a27857c015d6228d |

### <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>Kiedy można zrezygnować z starego pośredniego odcisku palca?

Bieżące certyfikaty urzędu certyfikacji *nie* zostaną odwołane do 15 lutego 2021. Po tej dacie można usunąć stare odciski palców z kodu.

W przypadku zmiany daty otrzymasz powiadomienie o nowej dacie odwołania.

### <a name="will-this-change-affect-me"></a>Czy ta zmiana wpłynie na mnie? 

Oczekujemy, że nie wpłynie to na **większość klientów platformy Azure** .  Jednak może to mieć wpływ na aplikację, jeśli jawnie określi listę akceptowalnych urzędów certyfikacji. To rozwiązanie jest znane jako Przypinanie certyfikatów.

Oto kilka sposobów na wykrycie, czy ma to wpływ na aplikację:

- Przeszukaj kod źródłowy dla odcisku palca, nazwy pospolitej i innych właściwości certyfikatu dowolnego urzędu certyfikacji TLS firmy Microsoft znajdującego się w [tym miejscu](https://www.microsoft.com/pki/mscorp/cps/default.htm). Jeśli istnieje dopasowanie, będzie to miało wpływ na aplikację. Aby rozwiązać ten problem, należy zaktualizować kod źródłowy obejmujący nowe urzędy certyfikacji. Najlepszym rozwiązaniem jest upewnienie się, że urzędy certyfikacji mogą być dodawane lub edytowane przy użyciu krótkiej uwagi. Uregulowania branżowe wymagają, aby certyfikaty urzędu certyfikacji były zastępowane w ciągu siedmiu dni, a tym samym klienci korzystają z przypinania, aby szybko reagować.

- Jeśli masz aplikację, która integruje się z interfejsami API platformy Azure lub innymi usługami platformy Azure i nie masz pewności, czy używa ona przypinania certyfikatów, skontaktuj się z dostawcą aplikacji.

- Różne systemy operacyjne i środowiska uruchomieniowe języka, które komunikują się z usługami platformy Azure, mogą wymagać dodatkowych kroków w celu poprawnego skompilowania łańcucha certyfikatów przy użyciu tych nowych katalogów głównych:
    - **Linux**: wiele dystrybucji wymaga dodania urzędów certyfikacji do/etc/SSL/certs. Aby uzyskać szczegółowe instrukcje, zapoznaj się z dokumentacją dystrybucji.
    - **Java**: Upewnij się, że magazyn kluczy Java zawiera wymienione powyżej urzędy certyfikacji.
    - **System Windows działający w odłączonych środowiskach**: systemy działające w środowiskach odłączonych muszą mieć dodane nowe katalogi główne do magazynu zaufanych głównych urzędów certyfikacji, a następnie pośrednicy dodani do magazynu pośrednich urzędów certyfikacji.
    - **Android**: Zapoznaj się z dokumentacją urządzenia i wersji systemu Android.
    - **Inne urządzenia sprzętowe, zwłaszcza IoT**: skontaktuj się z producentem urządzenia.

- Jeśli masz środowisko, w którym reguły zapory są ustawione tak, aby zezwalały na połączenia wychodzące tylko na pobieranie określonych list odwołania certyfikatów (CRL) i/lub lokalizacje weryfikacyjne protokołu stanu certyfikatów (OCSP). Musisz zezwolić na następującą listę CRL i adresy URL protokołu OCSP:

    - http://crl3&#46;d igicert&#46;com
    - http://crl4&#46;d igicert&#46;com
    - http://ocsp&#46;d igicert&#46;com
    - http://www&#46;d zaufania&#46;sieci
    - http://root-c3-ca2-2009&#46; Protokół OCSP&#46;d — zaufanie&#46;sieci
    - http://crl&#46; Microsoft&#46;com
    - http://oneocsp&#46; Microsoft&#46;com
    - http://ocsp&#46; msocsp&#46;com

## <a name="june-2020"></a>Czerwiec 2020 r.

Azure Monitor dla Key Vault jest teraz w wersji zapoznawczej.  Azure Monitor zapewnia kompleksowe monitorowanie magazynów kluczy, udostępniając ujednolicony wgląd w żądania Key Vault, wydajność, błędy i opóźnienia. Aby uzyskać więcej informacji, zobacz [Azure monitor for Key Vault (wersja zapoznawcza).](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="may-2020"></a>Maj 2020 r.

Key Vault "Przenieś swój własny klucz" (BYOK) jest teraz ogólnie dostępny. Zapoznaj się ze [specyfikacją Azure Key Vault BYOK](../keys/byok-specification.md)i Dowiedz się, jak [zaimportować klucze chronione przez moduł HSM do Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Marzec 2020 r.

Prywatne punkty końcowe są teraz dostępne w wersji zapoznawczej. Usługa link prywatny platformy Azure umożliwia dostęp do Azure Key Vault i hostowanych usług partnerskich klientów platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej.  Dowiedz się, jak [zintegrować Key Vault z prywatnym łączem platformy Azure](private-link-service.md).

## <a name="2019"></a>2019

- Wydanie zestawów SDK Azure Key Vault nowej generacji. Przykłady ich użycia znajdują się w temacie Azure Key Vaulte Przewodniki Szybki Start dla języka [Python](../secrets/quick-create-python.md), [.NET](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md)i [Node.js](../secrets/quick-create-node.md)
- Nowe zasady platformy Azure do zarządzania certyfikatami magazynu kluczy. Zapoznaj się z [Azure Policy wbudowanymi definicjami Key Vault](../policy-samples.md).
- Azure Key Vault rozszerzenie maszyny wirtualnej jest teraz ogólnie dostępne.  Zobacz [Key Vault rozszerzenie maszyny wirtualnej dla systemu Linux](../../virtual-machines/extensions/key-vault-linux.md) i [Key Vault rozszerzenie maszyny wirtualnej w systemie Windows](../../virtual-machines/extensions/key-vault-windows.md).
- Zarządzanie kluczami tajnymi opartymi na zdarzeniach dla Azure Key Vault jest teraz dostępne w programie Azure Event Grid. Aby uzyskać więcej informacji, zobacz [schemat Event Grid dla zdarzeń w Azure Key Vault] (.. /.. /Event-Grid/Event-Schema-Key-Vault.MD] i Dowiedz się, jak [odbierać powiadomienia magazynu kluczy i odpowiadać na nie za pomocą Azure Event Grid](event-grid-tutorial.md).

## <a name="2018"></a>2018

Nowe funkcje i integracje wydane w tym roku:

- Integracja z Azure Functions. Aby zapoznać się z przykładowym scenariuszem dotyczącym [Azure Functions](../../azure-functions/index.yml) operacji magazynu kluczy, zobacz [Automatyzowanie obrotu klucza tajnego](../secrets/tutorial-rotation.md). 
- [Integracja z Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). W tym Azure Databricks teraz obsługiwane są dwa typy zakresów tajnych: Azure Key Vault-kopia zapasowa i datakostki — kopia zapasowa. Aby uzyskać więcej informacji, zobacz [Tworzenie zakresu tajnego kopii zapasowej Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Punkty końcowe usługi sieci wirtualnej dla Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Nowe funkcje wydane w tym roku:

- Zarządzane klucze konta magazynu. Funkcja kluczy konta magazynu ułatwia integrację z usługą Azure Storage. Aby uzyskać więcej informacji, zobacz temat Omówienie [zarządzanych kluczy kont magazynu](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).
- Usuwanie nietrwałe. Funkcja usuwania nietrwałego usprawnia ochronę danych magazynów kluczy i obiektów magazynu kluczy. Aby uzyskać więcej informacji, zobacz temat Omówienie [usuwania nietrwałego](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015"></a>2015

Nowe funkcje wydane w tym roku:
- Zarządzanie certyfikatami. Dodano jako funkcję do wersji GA 2015-06-01 w dniu 26 września 2016.

Ogólna dostępność (wersja 2015-06-01) została ogłoszona 24 czerwca 2015. W tej wersji wprowadzono następujące zmiany: 
- Usuń klucz — pole "Użyj" zostało usunięte.
- Pobierz informacje o kluczu "use" usunięte.
- Zaimportuj klucz do magazynu — usunięte pole "Użyj".
- Przywróć klucz — usunięte pole "use".     
- Zmieniono "RSA_OAEP" na "RSA-OAEP" dla algorytmów RSA. Zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-certificates.md).    
 
Druga wersja zapoznawcza (wersja 2015-02-01-Preview) została ogłoszona 20 kwietnia 2015. Aby uzyskać więcej informacji, zobacz wpis w blogu [interfejsu API REST](https://docs.microsoft.com/archive/blogs/kv/rest-api-update) . Zaktualizowano następujące zadania:
 
- Wyświetl listę kluczy w magazynie — dodaliśmy obsługę dzielenia na strony.
- Aby wyświetlić listę wersji klucza, Wyświetl listę wersji operacji dodanej do klucza.  
- Wyświetlanie wpisów tajnych w magazynie — obsługa dzielenia na strony.
- Wyświetl listę wersji wpisu tajnego, aby wyświetlić listę wersji klucza tajnego.  
- Wszystkie operacje — dodano utworzone/zaktualizowane sygnatury czasowe do atrybutów.  
- Utwórz wpis tajny typu zawartości, który został dodany do wpisów tajnych.
- Utwórz Tagi dodane do klucza jako informacje opcjonalne.
- Utwórz Tagi tajne dodane jako opcjonalne informacje.
- Zaktualizuj znaczniki dodawane do klucza jako informacje opcjonalne.
- Zaktualizuj Tagi tajne dodane jako informacje opcjonalne.
- Zmieniono maksymalny rozmiar wpisów tajnych z 10 K na 25 KB. Zobacz [temat informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-certificates.md).    

## <a name="2014"></a>2014
 
Pierwsza wersja zapoznawcza (wersja 2014-12-08-Preview) została ogłoszona 8 stycznia 2015.  

## <a name="next-steps"></a>Następne kroki

Jeśli masz dodatkowe pytania, skontaktuj się z nami, korzystając z [pomocy technicznej](https://azure.microsoft.com/support/options/).  
