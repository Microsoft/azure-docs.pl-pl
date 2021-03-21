---
title: 'Błąd rozwiązywania problemów: środowisko uruchomieniowe usługi Azure Functions jest nieosiągalny'
description: Dowiedz się, jak rozwiązywać problemy z nieprawidłowym kontem magazynu.
ms.topic: article
ms.date: 09/05/2018
ms.openlocfilehash: 392882fc2f0394e61aee973a20479d8f1fa9bc92
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606977"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Błąd rozwiązywania problemów: "środowisko uruchomieniowe usługi Azure Functions jest nieosiągalny"

Ten artykuł pomaga w rozwiązywaniu problemów z następującym ciągiem błędu, który pojawia się w Azure Portal:

> "Błąd: środowisko uruchomieniowe usługi Azure Functions jest nieosiągalny. Kliknij tutaj, aby uzyskać szczegółowe informacje na temat konfiguracji magazynu ".

Ten problem występuje, gdy nie można uruchomić środowiska uruchomieniowego funkcji. Najbardziej typową przyczyną jest to, że aplikacja funkcji utraciła dostęp do konta magazynu. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące konta magazynu](storage-considerations.md#storage-account-requirements).

Pozostała część tego artykułu pomaga w rozwiązywaniu określonych przyczyn tego błędu, w tym o sposobach identyfikowania i rozwiązywania problemów.

## <a name="storage-account-was-deleted"></a>Konto magazynu zostało usunięte

Każda aplikacja funkcji wymaga konta magazynu do działania. Jeśli to konto zostanie usunięte, funkcje nie będą działać.

Zacznij od przejrzenia nazwy konta magazynu w ustawieniach aplikacji. Albo `AzureWebJobsStorage` `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` zawiera nazwę konta magazynu w ramach parametrów połączenia. Aby uzyskać więcej informacji, zobacz temat informacje o [ustawieniach aplikacji dla Azure Functions](./functions-app-settings.md#azurewebjobsstorage).

Wyszukaj swoje konto magazynu w Azure Portal, aby sprawdzić, czy nadal istnieje. Jeśli został usunięty, Utwórz ponownie konto magazynu i Zastąp parametry połączenia magazynu. Kod funkcji zostanie utracony i konieczne będzie jego ponowne wdrożenie.

## <a name="storage-account-application-settings-were-deleted"></a>Ustawienia aplikacji konta magazynu zostały usunięte

W poprzednim kroku, jeśli nie możesz znaleźć parametrów połączenia konta magazynu, prawdopodobnie został on usunięty lub nadpisany. Najczęściej trwa usuwanie ustawień aplikacji w przypadku używania miejsc wdrożenia lub skryptów Azure Resource Manager do ustawiania ustawień aplikacji.

### <a name="required-application-settings"></a>Wymagane ustawienia aplikacji

* Wymagane:
    * [`AzureWebJobsStorage`](./functions-app-settings.md#azurewebjobsstorage)
* Wymagane dla funkcji planu Premium:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](./functions-app-settings.md)
    * [`WEBSITE_CONTENTSHARE`](./functions-app-settings.md)

Aby uzyskać więcej informacji, zobacz temat informacje o [ustawieniach aplikacji dla Azure Functions](./functions-app-settings.md).

### <a name="guidance"></a>Wskazówki

* Nie sprawdzaj **Ustawienia gniazda** dla żadnego z tych ustawień. W przypadku wymiany miejsc wdrożenia funkcja jest przerywana.
* Nie należy modyfikować tych ustawień w ramach zautomatyzowanych wdrożeń.
* Te ustawienia muszą być podane i ważne podczas tworzenia. Automatyczne wdrożenie, które nie zawiera tych ustawień, powoduje, że aplikacja funkcji nie zostanie uruchomiona, nawet jeśli ustawienia zostaną dodane później.

## <a name="storage-account-credentials-are-invalid"></a>Poświadczenia konta magazynu są nieprawidłowe

W przypadku ponownego wygenerowania kluczy magazynu należy zaktualizować wcześniej omówione parametry połączenia konta magazynu. Aby uzyskać więcej informacji na temat zarządzania kluczami magazynu, zobacz [Tworzenie konta usługi Azure Storage](../storage/common/storage-account-create.md).

## <a name="storage-account-is-inaccessible"></a>Konto magazynu jest niedostępne

Aplikacja funkcji musi mieć dostęp do konta magazynu. Typowe problemy z zablokowaniem dostępu aplikacji funkcji do konta magazynu są następujące:

* Aplikacja funkcji jest wdrażana w App Service Environment (ASE) bez poprawnych reguł sieciowych w celu zezwalania na ruch do i z konta magazynu.

* Zapora konta magazynu jest włączona i nie jest skonfigurowana w taki sposób, aby zezwalała na ruch do i z funkcji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
* Sprawdź, czy `allowSharedKeyAccess` ustawienie ma ustawioną `true` wartość domyślną. Aby uzyskać więcej informacji, zobacz [Zapobiegaj autoryzacji klucza współużytkowanego dla konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/shared-key-authorization-prevent?tabs=portal#verify-that-shared-key-access-is-not-allowed). 

## <a name="daily-execution-quota-is-full"></a>Dzienny limit przydziału wykonywania jest pełny

W przypadku skonfigurowania dziennego przydziału wykonywania aplikacja funkcji jest tymczasowo wyłączona, co spowoduje, że wiele kontrolek portalu stanie się niedostępna. 

Aby sprawdzić limit przydziału w [Azure Portal](https://portal.azure.com), wybierz pozycję **funkcje platformy**  >  **aplikacja funkcji ustawienia** w aplikacji funkcji. Jeśli osiągnięto **dzienny limit przydziału użycia** , zostanie wyświetlony następujący komunikat:

  > "Aplikacja funkcji osiągnął dzienny limit przydziału użycia i został zatrzymany do następnego 24-godzinnego okresu czasu".

Aby rozwiązać ten problem, Usuń lub Zwiększ dzienny limit przydziału, a następnie uruchom ponownie aplikację. W przeciwnym razie wykonywanie aplikacji jest blokowane do następnego dnia.

## <a name="app-is-behind-a-firewall"></a>Aplikacja znajduje się za zaporą

Aplikacja funkcji może być nieosiągalna z jednego z następujących powodów:

* Aplikacja funkcji jest hostowana w ramach [wewnętrznego równoważenia obciążenia App Service Environment](../app-service/environment/create-ilb-ase.md) i jest skonfigurowana do blokowania przychodzącego ruchu internetowego.

* Aplikacja funkcji ma [Ograniczenia adresów IP dla ruchu przychodzącego](functions-networking-options.md#inbound-access-restrictions) , które są skonfigurowane do blokowania dostępu do Internetu. 

Azure Portal wykonuje wywołania bezpośrednio do uruchomionej aplikacji w celu pobrania listy funkcji i wysyła wywołania HTTP do punktu końcowego kudu. Ustawienia na poziomie platformy na karcie **funkcje platformy** są nadal dostępne.

Aby sprawdzić konfigurację środowiska ASE:
1. Przejdź do sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) podsieci, w której znajduje się środowisko ASE.
1. Sprawdź poprawność reguł ruchu przychodzącego, aby zezwolić na ruch pochodzący z publicznego adresu IP komputera, na którym uzyskujesz dostęp do aplikacji. 
   
Portalu można także użyć z komputera, który jest połączony z siecią wirtualną, na której uruchomiona jest aplikacja lub do maszyny wirtualnej działającej w sieci wirtualnej. 

Aby uzyskać więcej informacji na temat konfiguracji reguł ruchu przychodzącego, zobacz sekcję "sieciowe grupy zabezpieczeń" [zagadnienia dotyczące sieci App Service Environment](../app-service/environment/network-info.md#network-security-groups).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o monitorowaniu aplikacji funkcji:

> [!div class="nextstepaction"]
> [Monitorowanie usługi Azure Functions](functions-monitoring.md)
