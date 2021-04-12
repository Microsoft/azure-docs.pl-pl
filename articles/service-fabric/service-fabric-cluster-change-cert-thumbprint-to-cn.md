---
title: Zaktualizuj klaster, aby używał nazwy pospolitej certyfikatu
description: Informacje o konwertowaniu certyfikatu klastra Service Fabric platformy Azure z deklaracji opartych na odcisku palca do nazw pospolitych.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: f719b1eb39da776827c6babec61e9e6701bb4602
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97900794"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Konwertowanie certyfikatów klastra z deklaracji opartych na odcisku palca do nazw pospolitych

Podpis certyfikatu (często znany jako odcisk palca) jest unikatowy. Certyfikat klastra zadeklarowany przez odcisk palca odnosi się do określonego wystąpienia certyfikatu. Ta specyficzność sprawia, że Przerzucanie certyfikatów i zarządzanie to ogólnie, trudnym i jawnym. Każda zmiana wymaga aranżacji uaktualnień klastra i podstawowych hostów obliczeniowych.

Konwertowanie deklaracji certyfikatu klastra Service Fabric platformy Azure z odciskiem palca na podstawie deklaracji opartych na nazwie pospolitej podmiotu (CN) certyfikatu upraszcza zarządzanie istotnie. W szczególności przekroczenie certyfikatu nie wymaga już uaktualnienia klastra. W tym artykule opisano, jak przekonwertować istniejący klaster na deklaracje oparte na CN bez przestojów.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-to-certificate-authority-signed-certificates"></a>Przenieś do certyfikatów z podpisem urzędu certyfikacji

Zabezpieczenia klastra, którego certyfikat jest zadeklarowany przez odcisk palca, są w stanie niemożliwym lub niewykonalnym wypróbować certyfikat o takim samym podpisie jak inny. W takim przypadku pochodzenie certyfikatu jest mniej ważne, dlatego certyfikaty z podpisem własnym są odpowiednie.

Z kolei zabezpieczenia klastra, którego certyfikaty są deklarowane przez przepływy CN z niejawnego zaufania, właściciel klastra ma w swoim dostawcy certyfikatów. Dostawca jest usługą infrastruktury kluczy publicznych (PKI), która wystawił certyfikat. Relacja zaufania jest oparta na innych czynnikach, w praktykach certyfikacji infrastruktury PKI, bez względu na to, czy ich zabezpieczenia operacyjne są poddawane inspekcji i zatwierdzane przez inne zaufane strony itd.

Właściciel klastra musi mieć również szczegółową wiedzę na temat tego, które urzędy certyfikacji są wystawiane jako certyfikaty, ponieważ jest to podstawowy aspekt weryfikowania certyfikatów według podmiotu. Oznacza to również, że certyfikaty z podpisem własnym są całkowicie nieodpowiednie do tego celu. Każdy użytkownik może generować certyfikat z danym tematem.

Certyfikat zadeklarowany przez CN jest zwykle uznawany za ważny, jeśli:

* Jego łańcuch można skompilować pomyślnie.
* Podmiot ma oczekiwany element CN.
* Jego wystawca (bezpośredni lub wyższy w łańcuchu) jest traktowany jako zaufany przez agenta, który przeprowadza walidację.

Service Fabric obsługuje deklarowanie certyfikatów według nazwy POSPOLITej na dwa sposoby:

* Z *niejawnymi* wystawcami, co oznacza, że łańcuch musi kończyć się kotwicą zaufania.
* Z wystawcami zadeklarowanymi przez odcisk palca, który jest znany jako Przypinanie wystawcy.

Aby uzyskać więcej informacji, zobacz [deklaracje weryfikacji certyfikatu opartego na nazwach wspólnych](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Aby przekonwertować klaster przy użyciu certyfikatu z podpisem własnym zadeklarowanego przez odcisk palca do CN, docelowy certyfikat podpisany przez urząd certyfikacji musi zostać najpierw wprowadzony do klastra za pomocą odcisku palca. Tylko konwersja z odcisku palca do możliwej do CN.

W celach testowych certyfikat z podpisem własnym *może* być zadeklarowany przez CN, ale tylko wtedy, gdy wystawca jest przypięty do własnego odcisku palca. Z punktu widzenia zabezpieczeń ta akcja jest niemal równoważna przy deklarowaniu tego samego certyfikatu przez odcisk palca. Pomyślna konwersja tego rodzaju nie gwarantuje pomyślnej konwersji odcisku palca do CN z certyfikatem podpisanym przez urząd certyfikacji. Zalecamy przetestowanie konwersji przy użyciu odpowiedniego certyfikatu podpisanego przez urząd certyfikacji. Dla tego testu istnieją bezpłatne opcje.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Przekaż certyfikat i zainstaluj go w zestawie skalowania

Na platformie Azure zalecanym mechanizmem uzyskiwania i aprowizacji certyfikatów jest Azure Key Vault i narzędzi. Certyfikat zgodny z deklaracją certyfikatu klastra musi być zainicjowany do każdego węzła zestawu skalowania maszyn wirtualnych, który składa się z klastra. Aby uzyskać więcej informacji, zobacz wpisy [tajne w zestawach skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm).

Ważne jest, aby zainstalować zarówno bieżące, jak i docelowe certyfikaty klastra na maszynach wirtualnych każdego typu węzła klastra przed wprowadzeniem zmian w deklaracjach certyfikatu klastra. Przejście z wystawiania certyfikatów do inicjowania obsługi administracyjnej w węźle Service Fabric zostało omówione na głębokości w [podróży certyfikatu](cluster-security-certificate-management.md#the-journey-of-a-certificate).

## <a name="bring-the-cluster-to-an-optimal-starting-state"></a>Przełączenie klastra do optymalnego stanu uruchamiania

Konwertowanie deklaracji certyfikatu z odcisku palca na podstawie wpływu na podstawie nazwy POSPOLITej:

- Jak każdy węzeł w klastrze znajduje i przedstawia jego poświadczenia innym węzłom.
- Jak każdy węzeł sprawdza poprawność poświadczeń swojego odpowiednika przy nawiązywaniu bezpiecznego połączenia.

Przed kontynuowaniem zapoznaj się z [prezentacją i regułami walidacji dla obu konfiguracji](cluster-security-certificates.md#certificate-configuration-rules) . Najważniejszym zagadnieniem podczas konwersji odcisku palca na CN jest to, że uaktualnione i nieuaktualnione węzły (czyli węzły należące do różnych domen uaktualnienia) muszą mieć możliwość pomyślnego uwierzytelnienia wzajemnego w dowolnym momencie podczas uaktualniania. Zalecanym sposobem osiągnięcia tego zachowania jest zadeklarowanie obiektu docelowego lub certyfikatu celu przez odcisk palca podczas wstępnego uaktualniania. Następnie ukończ przejście do nazwy CN w kolejnej. Jeśli klaster jest już w zalecanym stanie uruchamiania, można pominąć tę sekcję.

Istnieje wiele prawidłowych Stanów początkowych dla konwersji. Niezmienna polega na tym, że klaster używa już certyfikatu docelowego (zadeklarowanego przez odcisk palca) na początku uaktualniania do CN. Rozważamy `GoalCert` , `OldCert1` i `OldCert2` w tym artykule.

#### <a name="valid-starting-states"></a>Prawidłowe Stany uruchamiania

- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, gdzie `GoalCert` ma datę późniejszą `NotBefore` niż `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, gdzie `GoalCert` ma datę późniejszą `NotBefore` niż `OldCert1`

> [!NOTE]
> Przed wersjami 7.2.445 (7,2 CU4) Service Fabric wybrany certyfikat z poprzednią datą wygaśnięcia (certyfikat z najpóźniejszą właściwością "NotAfter"), więc powyższe Stany uruchamiania przed 7,2 CU4 wymagają, aby GoalCert miał `NotAfter` datę późniejszą niż `OldCert1`

Jeśli klaster nie jest w jednym z wcześniej opisanych prawidłowych Stanów, zapoznaj się z informacjami na temat osiągnięcia tego stanu w sekcji na końcu tego artykułu.

## <a name="select-the-desired-cn-based-certificate-validation-scheme"></a>Wybieranie żądanego schematu walidacji certyfikatu opartego na CN

Jak opisano wcześniej, Service Fabric obsługuje deklarowanie certyfikatów przez CN z niejawną kotwicą zaufania lub jawnie Przypinanie odcisków palców wystawcy. Aby uzyskać więcej informacji, zobacz [deklaracje weryfikacji certyfikatu opartego na nazwach wspólnych](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Upewnij się, że masz dobre zrozumienie różnic i implikacje wyboru jednego z tych mechanizmów. Syntaktycznie, ta różnica lub wybór jest określana przez wartość `certificateIssuerThumbprintList` parametru. Puste oznacza poleganie na zaufanym głównym urzędzie certyfikacji (kotwicy zaufania), natomiast zestaw odcisków palców ogranicza dozwolone bezpośrednie wystawcy certyfikatów klastra.

   > [!NOTE]
   > `certificateIssuerThumbprint`Pole umożliwia określenie oczekiwanych bezpośrednich wystawców certyfikatów zadeklarowanych przez podmiot CN. Dopuszczalne wartości to jeden lub więcej oddzielonych przecinkami odcisków palców SHA1. Ta akcja wzmacnia weryfikację certyfikatu.
   >
   > Jeśli nie określono żadnych wystawców lub lista jest pusta, certyfikat zostanie zaakceptowany do uwierzytelnienia, jeśli jego łańcuch może być skompilowany. Certyfikat zostanie następnie zakończony w katalogu głównym zaufanym przez moduł sprawdzania poprawności. W przypadku określenia co najmniej jednego odcisku palca wystawcy certyfikat zostanie zaakceptowany, jeśli odcisk palca jego bezpośredniego wystawcy, jako wyodrębniony z łańcucha, jest zgodny z dowolnymi wartościami określonymi w tym polu. Certyfikat zostanie zaakceptowany niezależnie od tego, czy katalog główny jest zaufany, czy nie.
   >
   > Infrastruktura PKI może używać różnych urzędów certyfikacji (znanych także jako *wystawców*) do podpisywania certyfikatów z danym tematem. Z tego powodu należy określić wszystkie oczekiwane odciski palców wystawcy dla tego tematu. Innymi słowy, odnowienie certyfikatu nie jest gwarantowane podpisem tego samego wystawcy w przypadku odnowienia certyfikatu.
   >
   > Określenie wystawcy jest uznawane za najlepsze rozwiązanie. Pominięcie wystawcy będzie nadal działać w przypadku certyfikatów w łańcuchu do zaufanego katalogu głównego, ale takie zachowanie ma ograniczenia i może zostać rozwiązane w najbliższej przyszłości. Klastry wdrożone na platformie Azure zabezpieczone certyfikatami x509 wystawione przez prywatną infrastrukturę PKI i zadeklarowane przez podmiot mogą nie być w stanie sprawdzić poprawności przez Service Fabric (w przypadku komunikacji między klastrami). Sprawdzanie poprawności wymaga, aby zasady certyfikatu infrastruktury PKI były wykrywalne, dostępne i dostępne.

## <a name="update-the-clusters-azure-resource-manager-template-and-deploy"></a>Zaktualizuj szablon Azure Resource Manager i Wdróż go w klastrze

Zarządzaj klastrami Service Fabric przy użyciu szablonów Azure Resource Manager (ARM). Alternatywą, która używa również artefaktów JSON, jest [Azure Resource Explorer (wersja zapoznawcza)](https://resources.azure.com). W tej chwili nie jest dostępne odpowiednie środowisko w Azure Portal.

Jeśli oryginalny szablon odpowiadający istniejącemu klastrowi nie jest dostępny, można uzyskać odpowiedni szablon w Azure Portal. Przejdź do grupy zasobów zawierającej klaster, a następnie wybierz pozycję **Eksportuj szablon** z menu **Automatyzacja** po lewej stronie. Następnie wybierz żądane zasoby. Należy wyeksportować odpowiednio minimalny zestaw skalowania maszyn wirtualnych i zasoby klastra. Wygenerowany szablon można również pobrać. Ten szablon może wymagać zmian przed pełnym wdrożeniem. Szablon może być również niezgodny z oryginałem. Jest to odbicie bieżącego stanu zasobu klastra.

Niezbędne zmiany są następujące:

- Aktualizowanie definicji rozszerzenia węzła Service Fabric (w ramach zasobu maszyny wirtualnej). Jeśli klaster definiuje wiele typów węzłów, należy zaktualizować definicję każdego odpowiadającego zestawu skalowania maszyn wirtualnych.
- Aktualizowanie definicji zasobu klastra.

Szczegółowe przykłady są zawarte tutaj.

### <a name="update-the-virtual-machine-scale-set-resources"></a>Aktualizowanie zasobów zestawu skalowania maszyn wirtualnych
Od:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
Do:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="update-the-cluster-resource"></a>Aktualizowanie zasobu klastra

W zasobów **Microsoft. servicefabric/klastrów** Dodaj właściwość **certificateCommonNames** z ustawieniem **commonNames** , a następnie Usuń właściwość **certyfikatu** całkowicie (wszystkie jej ustawienia).

Od:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
Do:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

Aby uzyskać więcej informacji, zobacz [wdrażanie klastra Service Fabric, który używa nazwy pospolitej certyfikatu zamiast odcisku palca](./service-fabric-create-cluster-using-cert-cn.md).

## <a name="deploy-the-updated-template"></a>Wdróż zaktualizowany szablon

Wdróż ponownie zaktualizowany szablon po wprowadzeniu zmian.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Uzyskaj prawidłowy stan początkowy na potrzeby konwertowania klastra na deklaracje certyfikatów oparte na CN

| Stan początkowy | Uaktualnienie 1 | Uaktualnienie 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` i `GoalCert` ma datę późniejszą `NotBefore` niż `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` i `OldCert1` ma datę późniejszą `NotBefore` niż `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, gdzie `OldCert1` ma datę późniejszą `NotBefore` niż `GoalCert` | Uaktualnij do programu `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, gdzie `OldCert1` ma datę późniejszą `NotBefore` niż `GoalCert` | Uaktualnij do programu `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Usuń jedno z `OldCert1` lub, `OldCert2` Aby przejść do stanu `Thumbprint: OldCertx, ThumbprintSecondary: None` | Kontynuuj od nowego stanu początkowego |

> [!NOTE]
> W przypadku klastra w wersji wcześniejszej niż wersja 7.2.445 (7,2 CU4) Zamień `NotBefore` `NotAfter` je na powyższe Stany.

Aby uzyskać instrukcje dotyczące sposobu przeprowadzania dowolnego z tych uaktualnień, zobacz [Zarządzanie certyfikatami w klastrze usługi Azure Service Fabric](service-fabric-cluster-security-update-certs-azure.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zabezpieczeniach klastra](service-fabric-cluster-security.md).
* Dowiedz się, jak przetworzyć [certyfikat klastra według nazwy pospolitej](service-fabric-cluster-rollover-cert-cn.md).
* Dowiedz się, jak [skonfigurować klaster do bezdotykowego przerzucania](cluster-security-certificate-management.md).

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
