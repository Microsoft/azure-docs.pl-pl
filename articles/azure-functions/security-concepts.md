---
title: Zabezpieczanie Azure Functions
description: Dowiedz się więcej na temat sposobu, w jaki kod funkcji działający na platformie Azure jest bezpieczniejszy przed typowymi atakami.
ms.date: 4/13/2020
ms.topic: conceptual
ms.openlocfilehash: 351bdca7ff94b6c058b5ab62fd9c16d707e7dc78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368493"
---
# <a name="securing-azure-functions"></a>Zabezpieczanie Azure Functions

Na wiele sposobów planowanie bezpiecznego programowania, wdrażania i działania funkcji bezserwerowych jest znacznie takie samo jak w przypadku dowolnej aplikacji hostowanej w sieci Web i w chmurze. [Azure App Service](../app-service/index.yml) udostępnia infrastrukturę hostingu dla aplikacji funkcji. Ten artykuł zawiera strategie zabezpieczeń do uruchamiania kodu funkcji oraz sposób, w jaki App Service może pomóc w zabezpieczeniu funkcji. 

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

Aby uzyskać zestaw zaleceń dotyczących zabezpieczeń, które są zgodne z [testem porównawczym zabezpieczeń platformy Azure](../security/benchmarks/overview.md), zobacz [podstawy zabezpieczeń platformy Azure dla Azure Functions](security-baseline.md).

## <a name="secure-operation"></a>Bezpieczna operacja 

W tej sekcji przedstawiono jak najszybciej skonfigurować i uruchomić aplikację funkcji. 

### <a name="security-center"></a>Security Center

Security Center integruje się z aplikacją funkcji w portalu. Umożliwia ona bezpłatne, szybką ocenę potencjalnych luk w zabezpieczeniach związanych z konfiguracją. Aplikacje funkcji działające w ramach dedykowanego planu mogą również korzystać z funkcji zabezpieczeń w czasie rzeczywistym Security Center, aby uzyskać dodatkowe koszty. Aby dowiedzieć się więcej, zobacz [Ochrona aplikacji sieci web Azure App Service i interfejsów API](../security-center/defender-for-app-service-introduction.md). 

### <a name="log-and-monitor"></a>Rejestrowanie i monitorowanie

Jednym ze sposobów wykrywania ataków jest przechodzenie przez działanie monitorowania aktywności i analiza rejestrowania. Funkcje integrują się z Application Insights w celu zbierania danych dotyczących dzienników, wydajności i błędów dla aplikacji funkcji. Application Insights automatycznie wykrywa anomalie wydajności i oferuje zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów oraz zrozumienie sposobu korzystania z funkcji. Aby dowiedzieć się więcej, zobacz [Monitor Azure Functions](functions-monitoring.md).

Funkcje programu integrują się również z dziennikami Azure Monitor, aby umożliwić konsolidowanie dzienników aplikacji funkcji przy użyciu zdarzeń systemowych w celu łatwiejszej analizy. Za pomocą ustawień diagnostycznych można skonfigurować eksportowanie strumieniowe dzienników platformy i metryki dla swoich funkcji do wybranego miejsca docelowego, na przykład obszaru roboczego analizy dzienników. Aby dowiedzieć się więcej, zobacz [Azure Functions monitorowania z dziennikami Azure monitor](functions-monitor-log-analytics.md). 

W przypadku wykrywania zagrożeń na poziomie przedsiębiorstwa i automatyzacji odpowiedzi można przesyłać strumieniowo dzienniki i zdarzenia do obszaru roboczego usługi Logs Analytics. Następnie można połączyć wskaźnik platformy Azure z tym obszarem roboczym. Aby dowiedzieć się więcej, zobacz [co to jest platforma Azure — wskaźnik](../sentinel/overview.md).  

Aby uzyskać więcej zaleceń dotyczących zabezpieczeń, zobacz [podstawy zabezpieczeń platformy Azure dla Azure Functions](security-baseline.md#logging-and-monitoring). 

### <a name="require-https"></a>Wymagaj protokołu HTTPS

Domyślnie klienci mogą łączyć się z punktami końcowymi funkcji przy użyciu protokołu HTTP lub HTTPS. Należy przekierować protokół HTTP do protokołu HTTPs, ponieważ protokół HTTPS używa protokołu SSL/TLS w celu zapewnienia bezpiecznego połączenia, które jest szyfrowane i uwierzytelniane. Aby dowiedzieć się, jak to zrobić, zobacz [Wymuś protokół https](../app-service/configure-ssl-bindings.md#enforce-https).

Gdy wymagasz protokołu HTTPS, należy również wymagać najnowszej wersji protokołu TLS. Aby dowiedzieć się, jak to zrobić, zobacz [Wymuś wersje protokołu TLS](../app-service/configure-ssl-bindings.md#enforce-tls-versions).

Aby uzyskać więcej informacji, zobacz [Secure Connections (TSL)](../app-service/overview-security.md#https-and-certificates).

### <a name="function-access-keys"></a>Klucze dostępu do funkcji

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

#### <a name="system-key"></a>Klucz systemowy 

Określone rozszerzenia mogą wymagać klucza zarządzanego przez system w celu uzyskania dostępu do punktów końcowych elementu webhook. Klucze systemowe są przeznaczone dla punktów końcowych funkcji specyficznych dla rozszerzenia, które są wywoływane przez składniki wewnętrzne. Na przykład [wyzwalacz Event Grid](functions-bindings-event-grid-trigger.md) wymaga, aby subskrypcja używała klucza systemowego podczas wywoływania punktu końcowego wyzwalacza. Durable Functions używa również kluczy systemowych do wywoływania [interfejsów API trwałego rozszerzenia zadań](durable/durable-functions-http-api.md). 

Zakres kluczy systemowych jest określany przez rozszerzenie, ale zazwyczaj ma zastosowanie do całej aplikacji funkcji. Klucze systemowe mogą być tworzone tylko przez określone rozszerzenia i nie można jawnie ustawić ich wartości. Podobnie jak inne klucze, można wygenerować nową wartość klucza z portalu lub za pomocą kluczowych interfejsów API.

#### <a name="keys-comparison"></a>Porównanie kluczy

Poniższa tabela zawiera porównanie użycia różnych rodzajów kluczy dostępu:

| Akcja                                        | Zakres                    | Prawidłowe klucze         |
|-----------------------------------------------|--------------------------|--------------------|
| Wykonaj funkcję                            | Określona funkcja        | Funkcja           |
| Wykonaj funkcję                            | Dowolna funkcja             | Funkcja lub host   |
| Wywoływanie punktu końcowego administratora                        | Aplikacja funkcji             | Host (tylko główny) |
| Wywoływanie interfejsów API trwałych rozszerzeń zadań              | Aplikacja funkcji<sup>1</sup> | System<sup>2</sup> |
| Wywoływanie elementu webhook określonego dla rozszerzenia (wewnętrzny) | Aplikacja funkcji<sup>1</sup> | System<sup>2</sup> |

<sup>1</sup> Zakres ustalony przez rozszerzenie.  
<sup>2</sup> Określone nazwy ustawione przez rozszerzenie.

Aby dowiedzieć się więcej na temat kluczy dostępu, zobacz artykuł dotyczący [powiązań wyzwalacza http](functions-bindings-http-webhook-trigger.md#obtaining-keys).


#### <a name="secret-repositories"></a>Repozytoria tajne

Domyślnie klucze są przechowywane w kontenerze magazynu obiektów BLOB na koncie udostępnianym przez `AzureWebJobsStorage` ustawienie. Możesz użyć określonych ustawień aplikacji, aby przesłonić to zachowanie i przechowywać klucze w innej lokalizacji.

|Lokalizacja  |Ustawienie | Wartość | Opis  |
|---------|---------|---------|---------|
|Inne konto magazynu     |  `AzureWebJobsSecretStorageSas`       | `<BLOB_SAS_URL` | Przechowuje klucze w magazynie obiektów BLOB na drugim koncie magazynu na podstawie podanego adresu URL sygnatury dostępu współdzielonego. Klucze są szyfrowane przed przechowywaniem przy użyciu klucza tajnego unikatowego dla aplikacji funkcji. |
|System plików   | `AzureWebJobsSecretStorageType`   |  `files`       | Klucze są utrwalane w systemie plików, szyfrowane przed magazynem przy użyciu klucza tajnego unikatowego dla aplikacji funkcji. |
|Azure Key Vault  | `AzureWebJobsSecretStorageType`<br/>`AzureWebJobsSecretStorageKeyVaultName` | `keyvault`<br/>`<VAULT_NAME>` | Magazyn musi mieć zasady dostępu odpowiadające tożsamości zarządzanej przypisanej do systemu zasobu hostingu. Zasady dostępu powinny udzielić tożsamości następujących uprawnień tajnych: `Get` , `Set` , `List` , i `Delete` . <br/>W przypadku uruchamiania lokalnego jest używana tożsamość dewelopera, a ustawienia muszą znajdować się w [local.settings.jspliku](functions-run-local.md#local-settings-file). | 
|Wpisy tajne usługi Kubernetes  |`AzureWebJobsSecretStorageType`<br/>`AzureWebJobsKubernetesSecretName` (opcjonalnie) | `kubernetes`<br/>`<SECRETS_RESOURCE>` | Obsługiwane tylko w przypadku uruchamiania środowiska uruchomieniowego Functions w Kubernetes. Gdy `AzureWebJobsKubernetesSecretName` nie jest ustawiona, repozytorium jest uznawane za tylko do odczytu. W takim przypadku wartości muszą zostać wygenerowane przed wdrożeniem. Azure Functions Core Tools automatycznie generuje wartości podczas wdrażania do Kubernetes.|

### <a name="authenticationauthorization"></a>Uwierzytelnianie/autoryzacja

Chociaż klucze funkcji mogą zapewnić pewne środki zaradcze dla niechcianego dostępu, jedynym sposobem na prawdziwą ochronę punktów końcowych funkcji jest wdrożenie pozytywnego uwierzytelniania klientów uzyskujących dostęp do funkcji. Następnie można podejmować decyzje dotyczące autoryzacji na podstawie tożsamości.  

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

### <a name="permissions"></a>Uprawnienia

Podobnie jak w przypadku dowolnej aplikacji lub usługi, celem jest uruchomienie aplikacji funkcji z najniższymi możliwymi uprawnieniami. 

#### <a name="user-management-permissions"></a>Uprawnienia do zarządzania użytkownikami

Funkcje obsługują wbudowaną [kontrolę dostępu opartą na rolach (Azure RBAC)](../role-based-access-control/overview.md). Role platformy Azure obsługiwane przez funkcje to [współautor](../role-based-access-control/built-in-roles.md#contributor), [właściciel](../role-based-access-control/built-in-roles.md#owner)i [czytelnik](../role-based-access-control/built-in-roles.md#owner). 

Uprawnienia są skuteczne na poziomie aplikacji funkcji. Rola współautor jest wymagana do wykonywania większości zadań na poziomie aplikacji. Tylko rola właściciela może usunąć aplikację funkcji. 

#### <a name="organize-functions-by-privilege"></a>Organizuj funkcje według uprawnień 

Parametry połączenia i inne poświadczenia przechowywane w ustawieniach aplikacji dają wszystkie funkcje w aplikacji funkcji ten sam zestaw uprawnień w skojarzonym zasobie. Rozważ zminimalizowanie liczby funkcji mających dostęp do określonych poświadczeń, przenosząc funkcje, które nie używają tych poświadczeń do osobnej aplikacji funkcji. Można zawsze używać technik, takich jak [łańcuchy funkcji](/learn/modules/chain-azure-functions-data-using-bindings/) , do przekazywania danych między funkcjami w różnych aplikacjach funkcji.  

#### <a name="managed-identities"></a>Tożsamości zarządzane

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

Tożsamości zarządzane mogą być używane zamiast wpisów tajnych dla połączeń z niektórych wyzwalaczy i powiązań. Zobacz [połączenia oparte na tożsamościach](#identity-based-connections).

Aby uzyskać więcej informacji, zobacz [jak używać zarządzanych tożsamości dla App Service i Azure Functions](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json).

#### <a name="restrict-cors-access"></a>Ogranicz dostęp do mechanizmu CORS

[Udostępnianie zasobów między źródłami (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) umożliwia aplikacjom sieci Web działającym w innej domenie wykonywanie żądań do punktów końcowych wyzwalacza http. App Service zapewnia wbudowaną obsługę przekazywania wymaganych nagłówków CORS w żądaniach HTTP. Reguły CORS są zdefiniowane na poziomie aplikacji funkcji.  

Chociaż chcemy używać symbolu wieloznacznego, który umożliwia wszystkim lokacjom dostęp do punktu końcowego. Nie ma to jednak na celu zapobieganie atakom z użyciem skryptów między witrynami. Zamiast tego należy dodać oddzielny wpis CORS dla domeny każdej aplikacji sieci Web, która musi uzyskać dostęp do punktu końcowego. 

### <a name="managing-secrets"></a>Zarządzanie wpisami tajnymi 

Aby można było nawiązać połączenie z różnymi usługami i zasobami, które wymagają uruchomienia kodu, aplikacje funkcji muszą mieć dostęp do wpisów tajnych, takich jak parametry połączenia i klucze usług. W tej sekcji opisano sposób przechowywania wpisów tajnych wymaganych przez funkcje.

Nigdy nie przechowuj wpisów tajnych w kodzie funkcji. 

#### <a name="application-settings"></a>Ustawienia aplikacji

Domyślnie przechowywane są parametry połączenia i wpisy tajne używane przez aplikację funkcji i powiązania jako ustawienia aplikacji. Dzięki temu te poświadczenia są dostępne zarówno dla kodu funkcji, jak i różnych powiązań używanych przez funkcję. Nazwa ustawienia aplikacji (klucz) służy do pobierania wartości rzeczywistej, która jest kluczem tajnym. 

Na przykład każda aplikacja funkcji wymaga skojarzonego konta magazynu, które jest używane przez środowisko uruchomieniowe. Domyślnie połączenie z tym kontem magazynu jest przechowywane w ustawieniu aplikacji o nazwie `AzureWebJobsStorage` .

Ustawienia aplikacji i parametry połączenia są przechowywane na platformie Azure. Są one odszyfrowywane tylko przed wstrzyknięciem do pamięci procesu aplikacji podczas uruchamiania aplikacji. Klucze szyfrowania są regularnie obracane. Jeśli wolisz zarządzać bezpiecznym magazynem wpisów tajnych, należy zamiast tego użyć ustawienia aplikacji do Azure Key Vault. 

Podczas tworzenia funkcji na komputerze lokalnym można także szyfrować domyślnie ustawienia w pliku local.settings.js. Aby dowiedzieć się więcej, zobacz `IsEncrypted` Właściwość w [pliku ustawień lokalnych](functions-run-local.md#local-settings-file).  

#### <a name="key-vault-references"></a>Odwołania Key Vault

Chociaż ustawienia aplikacji są wystarczające dla większości funkcji, warto udostępnić te same wpisy tajne w wielu usługach. W takim przypadku nadmiarowy Magazyn kluczy tajnych powoduje zwiększenie potencjalnych luk w zabezpieczeniach. Bardziej bezpiecznym podejściem jest centralny magazyn usługi Storage i Używanie odwołań do tej usługi zamiast samych tajemnic.      

[Azure Key Vault](../key-vault/general/overview.md) to usługa zapewniająca scentralizowane zarządzanie kluczami tajnymi z pełną kontrolą nad zasadami dostępu i historią inspekcji. Możesz użyć odwołania Key Vault w miejscu parametrów połączenia lub klucza w ustawieniach aplikacji. Aby dowiedzieć się więcej, zobacz [używanie Key Vault odwołań dla App Service i Azure Functions](../app-service/app-service-key-vault-references.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="identity-based-connections"></a>Połączenia oparte na tożsamościach

Tożsamości mogą być używane zamiast wpisów tajnych do łączenia się z niektórymi zasobami. Ta zaleta nie wymaga zarządzania wpisem tajnym i oferuje bardziej precyzyjną kontrolę dostępu i inspekcję. 

Podczas pisania kodu, który tworzy połączenie z [usługami platformy Azure, które obsługują uwierzytelnianie usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), możesz użyć tożsamości zamiast hasła lub parametrów połączenia. Szczegółowe informacje dotyczące obu metod połączenia znajdują się w dokumentacji każdej usługi.

Niektóre Azure Functions wyzwalacze i rozszerzenia powiązań można skonfigurować przy użyciu połączenia opartego na tożsamościach. Obecnie obejmuje to [usługi Azure Blob](./functions-bindings-storage-blob.md) i rozszerzenia [kolejki platformy Azure](./functions-bindings-storage-queue.md) . Informacje o sposobie konfigurowania tych rozszerzeń do korzystania z tożsamości znajdują się [w temacie jak używać połączeń opartych na tożsamościach w programie Azure Functions](./functions-reference.md#configure-an-identity-based-connection).

### <a name="set-usage-quotas"></a>Ustawianie przydziałów użycia

Rozważ ustawienie limitu przydziału użycia dla funkcji uruchomionych w ramach planu zużycia. W przypadku ustawienia dziennego limitu całkowitej liczby operacji w aplikacji funkcji wykonywanie jest przerywane po osiągnięciu limitu. Może to pomóc w ograniczeniu do złośliwego kodu wykonującego funkcje. Aby dowiedzieć się, jak oszacować zużycie dla funkcji, zobacz [szacowanie kosztów planu zużycia](functions-consumption-costs.md). 

### <a name="data-validation"></a>Walidacja danych

Wyzwalacze i powiązania używane przez funkcje nie zapewniają żadnej dodatkowej weryfikacji danych. Kod musi sprawdzać poprawność wszystkich danych odebranych z wyzwalacza lub powiązania wejściowego. W przypadku naruszenia zabezpieczeń usługi nadrzędnej nie są wymagane żadne niezweryfikowane dane wejściowe przepływające przez funkcje. Na przykład, jeśli funkcja przechowuje dane z kolejki usługi Azure Storage w relacyjnej bazie danych, należy sprawdzić poprawność danych i Sparametryzuj polecenia, aby uniknąć ataków iniekcji SQL. 

Nie zakładaj, że dane wprowadzone do funkcji zostały już zweryfikowane lub oczyszczone. Dobrym pomysłem jest również sprawdzenie, czy dane zapisywane do powiązań wyjściowych są prawidłowe. 

### <a name="handle-errors"></a>Obsługa błędów

Chociaż jest to podstawowe, ważne jest, aby napisać dobrą obsługę błędów w funkcjach. Nieobsłużone błędy bąbelków do hosta i są obsługiwane przez środowisko uruchomieniowe. Różne powiązania obsługują przetwarzanie błędów inaczej. Aby dowiedzieć się więcej, zobacz [Azure Functions obsługa błędów](functions-bindings-error-pages.md).

### <a name="disable-remote-debugging"></a>Wyłącz debugowanie zdalne

Upewnij się, że debugowanie zdalne jest wyłączone, z wyjątkiem sytuacji, gdy aktywnie debugujesz funkcje. Debugowanie zdalne można wyłączyć na karcie **Ustawienia ogólne** w **konfiguracji** aplikacji funkcji w portalu. 

### <a name="restrict-cors-access"></a>Ogranicz dostęp do mechanizmu CORS

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

Nie używaj symboli wieloznacznych na liście dozwolonych źródeł. Zamiast tego należy wyświetlić listę określonych domen, z których oczekuje się uzyskania żądań.

### <a name="store-data-encrypted"></a>Przechowywanie zaszyfrowanych danych

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="secure-deployment"></a>Bezpieczne wdrożenie

Azure Functions narzędzia integracji ułatwiają publikowanie kodu projektu funkcji lokalnych na platformie Azure. Ważne jest, aby zrozumieć, w jaki sposób wdrożenie działa podczas rozważania zabezpieczeń topologii Azure Functions.   

### <a name="deployment-credentials"></a>Poświadczenia wdrożenia

Wdrożenia App Service wymagają zestawu poświadczeń wdrożenia. Te poświadczenia wdrażania służą do zabezpieczania wdrożeń aplikacji funkcji. Poświadczenia wdrażania są zarządzane przez platformę App Service i szyfrowane w stanie spoczynku. 

Istnieją dwa rodzaje poświadczeń wdrożenia:

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

W tej chwili Key Vault nie jest obsługiwana w przypadku poświadczeń wdrożenia. Aby dowiedzieć się więcej o zarządzaniu poświadczeniami wdrażania, zobacz [Konfigurowanie poświadczeń wdrażania dla Azure App Service](../app-service/deploy-configure-credentials.md).

### <a name="disable-ftp"></a>Wyłącz FTP

Domyślnie każda aplikacja funkcji ma włączony punkt końcowy FTP. Dostęp do punktu końcowego FTP odbywa się przy użyciu poświadczeń wdrożenia. 

Usługa FTP nie jest zalecana w przypadku wdrażania kodu funkcji. Wdrożenia FTP są ręczne i wymagają synchronizacji wyzwalaczy. Aby dowiedzieć się więcej, zobacz [wdrażanie FTP](functions-deployment-technologies.md#ftp). 

Jeśli nie planujesz korzystać z protokołu FTP, należy go wyłączyć w portalu. W przypadku wybrania opcji korzystania z protokołu FTP należy [wymusić FTPS](../app-service/deploy-ftp.md#enforce-ftps).

### <a name="secure-the-scm-endpoint"></a>Zabezpieczanie punktu końcowego SCM

Każda aplikacja funkcji ma odpowiadający mu `scm` punkt końcowy usługi, który jest używany przez usługę Advanced Tools (kudu) na potrzeby wdrożeń i innych [rozszerzeń lokacji](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)App Service. Punkt końcowy usługi SCM dla aplikacji funkcji jest zawsze adresem URL w postaci `https://<FUNCTION_APP_NAME.scm.azurewebsites.net>` . W przypadku korzystania z izolacji sieciowej w celu zabezpieczenia funkcji należy również uwzględnić ten punkt końcowy. 

Mając osobny punkt końcowy SCM, można kontrolować wdrożenia i inne funkcje zaawansowanych narzędzi dla aplikacji funkcji, które są izolowane lub działają w sieci wirtualnej. Punkt końcowy usługi SCM obsługuje uwierzytelnianie podstawowe (przy użyciu poświadczeń wdrażania) i logowanie jednokrotne przy użyciu poświadczeń Azure Portal. Aby dowiedzieć się więcej, zobacz [dostęp do usługi kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). 

### <a name="continuous-security-validation"></a>Ciągła Weryfikacja zabezpieczeń

Ze względu na to, że zabezpieczenia muszą być uznawane za każdy krok w procesie programistycznym, warto również zaimplementować sprawdzanie poprawności zabezpieczeń w środowisku ciągłego wdrażania. Jest to czasami nazywane DevSecOps. Korzystanie z usługi Azure DevOps dla potoku wdrożenia pozwala zintegrować weryfikację w procesie wdrażania. Aby uzyskać więcej informacji, zobacz temat [Dowiedz się, jak dodać ciągłą weryfikację zabezpieczeń do potoku Ci/CD](/azure/devops/migrate/security-validation-cicd-pipeline).  

## <a name="network-security"></a>Bezpieczeństwo sieci

Ograniczanie dostępu do sieci do aplikacji funkcji pozwala kontrolować, kto może uzyskiwać dostęp do punktów końcowych funkcji. Funkcja wykorzystuje infrastrukturę App Service, aby umożliwić funkcjom dostęp do zasobów bez używania adresów z obsługą sieci Internet lub w celu ograniczenia dostępu do Internetu do punktu końcowego funkcji. Aby dowiedzieć się więcej na temat tych opcji sieciowych, zobacz [Azure Functions opcje sieci](functions-networking-options.md).

### <a name="set-access-restrictions"></a>Ustawianie ograniczeń dostępu

Ograniczenia dostępu umożliwiają definiowanie list reguł Zezwalaj/Odmów do kontrolowania ruchu do aplikacji. Reguły są oceniane w kolejności priorytetu. Jeśli nie zdefiniowano żadnych reguł, aplikacja będzie akceptować ruch z dowolnego adresu. Aby dowiedzieć się więcej, zobacz [Azure App Service ograniczeń dostępu #](../app-service/app-service-ip-restrictions.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="private-site-access"></a>Dostęp do witryn prywatnych

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

### <a name="deploy-your-function-app-in-isolation"></a>Wdróż aplikację funkcji w izolacji

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

### <a name="use-a-gateway-service"></a>Korzystanie z usługi bramy

Usługi bramy, takie jak [azure Application Gateway](../application-gateway/overview.md) i [Azure front drzwi](../frontdoor/front-door-overview.md) , umożliwiają skonfigurowanie zapory aplikacji sieci Web (WAF). Reguły WAF są używane do monitorowania lub blokowania wykrytych ataków, co zapewnia dodatkową warstwę ochrony dla funkcji. Aby skonfigurować WAF, aplikacja funkcji musi być uruchomiona w środowisku ASE lub przy użyciu prywatnych punktów końcowych (wersja zapoznawcza). Aby dowiedzieć się więcej, zobacz [Używanie prywatnych punktów końcowych](../app-service/networking/private-endpoint.md).    

## <a name="next-steps"></a>Następne kroki

+ [Podstawa zabezpieczeń platformy Azure dla Azure Functions](security-baseline.md)
+ [Diagnostyka Azure Functions](functions-diagnostics.md)
