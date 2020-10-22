---
title: Zaktualizuj klaster, aby używał nazwy pospolitej certyfikatu
description: Dowiedz się, jak konwertować certyfikat klastra Service Fabric z deklaracji opartych na odcisku palca do nazw pospolitych.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 224798565921593d3c91dfcc187efa71a71b1fdd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368064"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Konwertowanie certyfikatów klastra z deklaracji opartych na odcisku palca do nazw pospolitych
Sygnatura certyfikatu (colloquially znana jako "odcisk palca") jest unikatowa, co oznacza, że certyfikat klastra zadeklarowany przez odcisk palca odnosi się do określonego wystąpienia certyfikatu. To z kolei sprawia, że Przerzucanie certyfikatów i zarządzanie nimi jest ogólnie trudne i jawne: Każda zmiana wymaga organizowania uaktualnień klastra i podstawowych hostów obliczeniowych. Konwertowanie deklaracji certyfikatu klastra Service Fabricowego z odcisku palca na podstawie deklaracji w oparciu o nazwę pospolitą podmiotu certyfikatu upraszcza zarządzanie, w szczególności, przerzucając certyfikat nie wymagając już uaktualnienia klastra. W tym artykule opisano sposób konwertowania istniejącego klastra na wspólne deklaracje oparte na nazwach bez przestoju.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="moving-to-certificate-authority-ca-signed-certificates"></a>Przeniesienie na certyfikaty z podpisem urzędu certyfikacji
Zabezpieczenia klastra, którego certyfikat jest zadeklarowany przez odciskiem palca, są niewykonalne lub nie jest możliwe do wypróbowania certyfikatu z tym samym podpisem co inny. W takim przypadku pochodzenie certyfikatu jest mniej ważne i dlatego certyfikaty z podpisem własnym są odpowiednie. Z kolei zabezpieczenia klastra z certyfikatami zadeklarowanymi przez typowe przepływy nazw z usługi infrastruktury kluczy publicznych (PKI), które wystawiły ten certyfikat, i obejmują takie aspekty, jak ich praktyki certyfikacji, czy ich zabezpieczenia operacyjne są poddawane inspekcji i wielu innych. Z tego powodu wybór infrastruktury PKI jest istotny, Intimate znajomość wystawców (urzędu certyfikacji lub urzędu certyfikacji), a certyfikaty z podpisem własnym są zasadniczo bezwartościowe. Certyfikat zadeklarowany według nazwy pospolitej (CN) jest zwykle uznawany za ważny, jeśli jego łańcuch można skompilować pomyślnie, podmiot ma oczekiwany element CN, a jego wystawca (bezpośrednie lub wyższe w łańcuchu) jest zaufany przez agenta, który przeprowadza walidację. Service Fabric obsługuje deklarowanie certyfikatów według nazwy POSPOLITej ("niejawny") (łańcuch musi kończyć się kotwicą zaufania) lub z wystawcami zadeklarowanymi przez odcisk palca ("Przypinanie wystawcy"); Aby uzyskać więcej informacji, zobacz ten  [artykuł](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) . Aby przekonwertować klaster przy użyciu certyfikatu z podpisem własnym zadeklarowanego przez odcisk palca do nazwy pospolitej, należy najpierw wprowadzić certyfikat podpisany przez urząd certyfikacji do klastra przez odcisk palca; tylko wtedy konwersja z jednostki TP na wartość CN jest możliwa.

W celach testowych certyfikat z podpisem własnym może być deklarowany przez CN, przypinając wystawcy do własnego odcisku palca; z punktu widzenia zabezpieczeń jest to niemal równoważne deklarowaniu tego samego certyfikatu przez jednostkę TP. Należy jednak zauważyć, że pomyślna konwersja tego rodzaju nie gwarantuje pomyślnej konwersji z klasy TP na CN z certyfikatem z podpisem CA. W związku z tym zaleca się przetestowanie konwersji przy użyciu prawidłowego certyfikatu podpisanego przez urząd certyfikacji (istnieją bezpłatne opcje).

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Przekaż certyfikat i zainstaluj go w zestawie skalowania
W systemie Azure zalecany mechanizm uzyskiwania i aprowizacji certyfikatów obejmuje usługę Azure Key Vault i jej narzędzia. Certyfikat zgodny z deklaracją certyfikatu klastra musi być zainicjowany do każdego węzła zestawu skalowania maszyn wirtualnych składającego się z klastra; Aby uzyskać więcej informacji, zobacz wpisy [tajne w zestawach skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm) . Należy pamiętać, że zarówno bieżący, jak i docelowy certyfikat klastra są instalowane na maszynach wirtualnych każdego typu węzła klastra przed wprowadzeniem zmian w deklaracjach certyfikatu klastra. Przejście z wystawiania certyfikatów do inicjowania obsługi administracyjnej w węźle usługi Service Fabric zostało omówione [tutaj](cluster-security-certificate-management.md#the-journey-of-a-certificate).

## <a name="bring-cluster-to-an-optimal-starting-state"></a>Przełącz klaster do optymalnego stanu uruchamiania
Konwertowanie deklaracji certyfikatu z odcisku palca na podstawie nazwy pospolitej na podstawie:

- Jak każdy węzeł w klastrze znajduje i przedstawia jego poświadczenia innym węzłom
- Jak każdy węzeł sprawdza poprawność poświadczeń swojego odpowiednika przy nawiązywaniu bezpiecznego połączenia  

Przed kontynuowaniem zapoznaj się z [prezentacją i regułami walidacji dla obu konfiguracji](cluster-security-certificates.md#certificate-configuration-rules) . Najważniejszym zagadnieniem podczas przeprowadzania konwersji nazw odciskiem palca jest to, że uaktualnione i nieuaktualnione węzły (czyli węzły należące do różnych domen uaktualnienia) muszą mieć możliwość pomyślnego uwierzytelnienia wzajemnego w dowolnym momencie podczas uaktualniania. Zalecanym sposobem osiągnięcia tego celu jest zadeklarowanie certyfikatu docelowego/celu przez odcisk palca w ramach wstępnego uaktualnienia i dokończenie przejścia do nazwy pospolitej w kolejnej pierwszej. Jeśli klaster jest już w zalecanym stanie uruchamiania, można pominąć tę sekcję.

Istnieje wiele prawidłowych Stanów początkowych dla konwersji; Niezmienna polega na tym, że klaster używa już certyfikatu docelowego (zadeklarowanego przez odcisk palca) na początku uaktualnienia do nazwy pospolitej. Uważamy `GoalCert` , `OldCert1` że `OldCert2` :

#### <a name="valid-starting-states"></a>Prawidłowe Stany uruchamiania
- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, gdzie `GoalCert` ma datę późniejszą `NotAfter` niż `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, gdzie `GoalCert` ma datę późniejszą `NotAfter` niż `OldCert1`

Jeśli klaster nie znajduje się w jednym z prawidłowych Stanów opisanych powyżej, zapoznaj się z dodatkiem na końcu tego artykułu.

## <a name="select-the-desired-common-name-based-certificate-validation-scheme"></a>Wybieranie odpowiedniego schematu walidacji certyfikatu opartego na nazwach
Jak opisano wcześniej, Service Fabric obsługuje deklarowanie certyfikatów według CN z niejawną kotwicą zaufania lub jawnie Przypinanie odcisków palców wystawcy. Zapoznaj się z [tym artykułem](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) , aby uzyskać szczegółowe informacje, i zadbaj o to, aby poznać różnice i wpływ wyboru jednego z tych mechanizmów. Syntaktycznie ta różnica/wybór jest określana przez wartość `certificateIssuerThumbprintList` parametru: puste oznacza poleganie na zaufanym głównym urzędzie certyfikacji (kotwicy zaufania), natomiast zestaw odcisków palców ogranicza dozwolone bezpośrednie wystawcy certyfikatów klastra.

   > [!NOTE]
   > Pole "certificateIssuerThumbprint" umożliwia określenie oczekiwanych bezpośrednich wystawców certyfikatów zadeklarowanych przez wspólną nazwę podmiotu. Dopuszczalne wartości to jeden lub więcej oddzielonych przecinkami odcisków palców SHA1. Zwróć uwagę na to, że jest to wzmocnienie weryfikacji certyfikatu — Jeśli nie określono żadnych wystawców/lista jest pusta, certyfikat zostanie zaakceptowany do uwierzytelnienia, jeśli jego łańcuch może zostać skompilowany i zakończony przez moduł walidacji. W przypadku określenia co najmniej jednego odcisku palca wystawcy certyfikat zostanie zaakceptowany, jeśli odcisk palca jego bezpośredniego wystawcy został wyodrębniony z łańcucha, dopasowuje wszystkie wartości określone w tym polu — niezależnie od tego, czy katalog główny jest zaufany, czy nie. Należy pamiętać, że infrastruktura PKI może używać różnych urzędów certyfikacji ("emitencis") do podpisywania certyfikatów z danym tematem i dlatego należy określić wszystkie oczekiwane odciski palców wystawcy dla tego tematu. Inaczej mówiąc, odnowienie certyfikatu nie jest gwarantowane przez tego samego wystawcę co odnawiany certyfikat.
   >
   > Określenie wystawcy jest uznawane za najlepsze rozwiązanie; Pomijanie będzie nadal działać — w przypadku certyfikatów w łańcuchu do zaufanego katalogu głównego — to zachowanie ma ograniczenia i może zostać rozwiązane w najbliższej przyszłości. Należy również zauważyć, że klastry wdrożone na platformie Azure i zabezpieczone certyfikatami x509 wystawione przez prywatną infrastrukturę PKI i zadeklarowane przez podmiot mogą nie być w stanie sprawdzić poprawności przez usługę Azure Service Fabric (w przypadku komunikacji między klastrami), jeśli zasady certyfikatu infrastruktury PKI nie są wykrywalne, dostępne i dostępne. 

## <a name="update-the-clusters-azure-resource-management-arm-template-and-deploy"></a>Aktualizowanie szablonu usługi Azure Resource Management (ARM) klastra i wdrażanie
Zalecane jest zarządzanie klastrami usługi Azure Service Fabric przy użyciu szablonów ARM; Alternatywą jest także użycie artefaktów JSON, [Azure Resource Explorer (wersja zapoznawcza)](https://resources.azure.com). W tej chwili w Azure Portal nie jest dostępne odpowiednie środowisko. Jeśli oryginalny szablon odpowiadający istniejącemu klastrowi nie jest dostępny, można uzyskać odpowiedni szablon w Azure Portal, przechodząc do grupy zasobów zawierającej klaster, wybierając pozycję **Eksportuj szablon** z menu po lewej stronie **automatyzacji** , a następnie wybierając odpowiednie zasoby. należy wyeksportować odpowiednio minimalny zestaw skalowania maszyn wirtualnych i zasoby klastra. Wygenerowany szablon można również pobrać. Uwaga Ten szablon może wymagać zmian, zanim zostanie w pełni wdrożony i może nie być zgodny z oryginalnie oryginalnym. jest to odbicie bieżącego stanu zasobu klastra.

Niezbędne zmiany są następujące:
    - aktualizowanie definicji rozszerzenia węzła Service Fabric (w ramach zasobu maszyny wirtualnej); Jeśli klaster definiuje wiele typów węzłów, należy zaktualizować definicję każdego odpowiadającego zestawu skalowania maszyn wirtualnych
    - aktualizowanie definicji zasobu klastra

Poniżej znajdują się szczegółowe przykłady.

### <a name="updating-the-virtual-machine-scale-set-resources"></a>Aktualizowanie zasobów zestawu skalowania maszyn wirtualnych
Źródło
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
Działanie
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

### <a name="updating-the-cluster-resource"></a>Aktualizowanie zasobu klastra
W zasobów **Microsoft. servicefabric/klastrów** Dodaj właściwość **certificateCommonNames** z ustawieniem **commonNames** i Usuń całkowicie Właściwość **certyfikatu** (wszystkie jej ustawienia):

Źródło
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
Działanie
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

Aby uzyskać więcej informacji, zobacz [wdrażanie klastra Service Fabric, który używa nazwy pospolitej certyfikatu zamiast odcisku palca.](./service-fabric-create-cluster-using-cert-cn.md)

## <a name="deploy-the-updated-template"></a>Wdróż zaktualizowany szablon
Wdróż ponownie zaktualizowany szablon po wprowadzeniu zmian.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="appendix-achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Dodatek: uzyskanie prawidłowego stanu początkowego na potrzeby konwertowania klastra na deklaracje certyfikatów oparte na CN

| Stan początkowy | Uaktualnienie 1 | Uaktualnienie 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` i `GoalCert` ma datę późniejszą `NotAfter` niż `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` i `OldCert1` ma datę późniejszą `NotAfter` niż `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, gdzie `OldCert1` ma datę późniejszą `NotAfter` niż `GoalCert` | Uaktualnij do programu `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, gdzie `OldCert1` ma datę późniejszą `NotAfter` niż `GoalCert` | Uaktualnij do programu `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Usuń jedno z `OldCert1` lub, `OldCert2` Aby przejść do stanu `Thumbprint: OldCertx, ThumbprintSecondary: None` | Kontynuuj od nowego stanu początkowego |

Aby uzyskać instrukcje dotyczące sposobu przeprowadzania któregokolwiek z tych uaktualnień, zobacz [ten dokument](service-fabric-cluster-security-update-certs-azure.md).


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zabezpieczeniach klastra](service-fabric-cluster-security.md).
* Dowiedz się [, jak przerzucać certyfikat klastra według nazwy pospolitej](service-fabric-cluster-rollover-cert-cn.md)
* Dowiedz się, jak [skonfigurować klaster na potrzeby przerzucania bezdotykowego](cluster-security-certificate-management.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
