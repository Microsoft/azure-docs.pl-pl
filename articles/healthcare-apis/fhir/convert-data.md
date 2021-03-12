---
title: Konwersja danych dla interfejsu API platformy Azure dla usługi FHIR
description: Aby przekonwertować dane w interfejsie API platformy Azure dla FHIR, użyj punktu końcowego danych $convert i szablonów dostosowywania.
services: healthcare-apis
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 01/19/2021
ms.author: ranku
ms.openlocfilehash: 7518f5e2984029c087eec1e6697f3237410bda4b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020424"
---
# <a name="how-to-convert-data-to-fhir-preview"></a>Jak przekonwertować dane na FHIR (wersja zapoznawcza)

> [!IMPORTANT]
> Ta możliwość jest dostępna w publicznej wersji zapoznawczej, nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Niestandardowy punkt końcowy danych $convert w interfejsie API platformy Azure dla FHIR jest przeznaczony do konwersji danych z różnych formatów do FHIR. Używa aparatu szablonu płynu i szablonów z projektu [konwertera FHIR](https://github.com/microsoft/FHIR-Converter) jako domyślnych szablonów. Te szablony konwersji można dostosować zgodnie z wymaganiami. Obecnie obsługuje konwersję HL7v2 na FHIR.

## <a name="use-the-convert-data-endpoint"></a>Korzystanie z punktu końcowego danych $convert

`https://<<FHIR service base URL>>/$convert-data`

$convert — dane pobierają zasób [parametru](http://hl7.org/fhir/parameters.html) w treści żądania, zgodnie z poniższym opisem:

**Zasób parametru:**

| Nazwa parametru      | Opis | Dopuszczalne wartości |
| ----------- | ----------- | ----------- |
| inputData      | Dane do przekonwertowania. | Prawidłowa wartość typu danych ciągu JSON|
| inputDataType   | Typ danych wejściowych. | ```HL7v2``` |
| templateCollectionReference | Odwołanie do kolekcji szablonów. Może to być odwołanie do **szablonów domyślnych** lub obraz szablonu niestandardowego, który jest zarejestrowany w interfejsie API platformy Azure dla FHIR. Zobacz poniżej, aby dowiedzieć się więcej o dostosowywaniu szablonów, hostingu na ACR i rejestrowaniu w interfejsie API platformy Azure dla usługi FHIR.  | ```microsofthealth/fhirconverter:default```, \<RegistryServer\>/\<imageName\>@\<imageDigest\> |
| rootTemplate | Szablon główny do użycia podczas przekształcania danych. | ```ADT_A01```, ```OML_O21```, ```ORU_R01```, ```VXU_V04``` |  

> [!WARNING]
> Szablony domyślne ułatwiają szybkie rozpoczęcie pracy. Jednak te aktualizacje mogą zostać zaktualizowane po uaktualnieniu interfejsu API platformy Azure dla usługi FHIR. Aby zapewnić spójne zachowanie konwersji danych w różnych wersjach interfejsu API platformy Azure dla usługi FHIR, musisz hostować własne kopie szablonów na Azure Container Registry, zarejestrować je w INTERFEJSie API platformy Azure dla FHIR i użyć w wywołaniach interfejsu API w sposób opisany w dalszej części tego problemu.

**Przykładowe żądanie:**

```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "inputData",
            "valueString": "MSH|^~\\&|SIMHOSP|SFAC|RAPP|RFAC|20200508131015||ADT^A01|517|T|2.3|||AL||44|ASCII\nEVN|A01|20200508131015|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|\nPID|1|3735064194^^^SIMULATOR MRN^MRN|3735064194^^^SIMULATOR MRN^MRN~2021051528^^^NHSNBR^NHSNMBR||Kinmonth^Joanna^Chelsea^^Ms^^CURRENT||19870624000000|F|||89 Transaction House^Handmaiden Street^Wembley^^FV75 4GJ^GBR^HOME||020 3614 5541^HOME|||||||||C^White - Other^^^||||||||\nPD1|||FAMILY PRACTICE^^12345|\nPV1|1|I|OtherWard^MainRoom^Bed 183^Simulated Hospital^^BED^Main Building^4|28b|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|||CAR|||||||||16094728916771313876^^^^visitid||||||||||||||||||||||ARRIVED|||20200508131015||"
        },
        {
            "name": "inputDataType",
            "valueString": "Hl7v2"
        },
        {
            "name": "templateCollectionReference",
            "valueString": "microsofthealth/fhirconverter:default"
        },
        {
            "name": "rootTemplate",
            "valueString": "ADT_A01"
        }
    ]
}
```

**Przykładowa odpowiedź:**

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "entry": [
    {
      "fullUrl": "urn:uuid:9d697ec3-48c3-3e17-db6a-29a1765e22c6",
      "resource": {
        "resourceType": "Patient",
        "id": "9d697ec3-48c3-3e17-db6a-29a1765e22c6",
        ...
        ...
      "request": {
        "method": "PUT",
        "url": "Location/50becdb5-ff56-56c6-40a1-6d554dca80f0"
      }
    }
  ]
}
```

## <a name="customize-templates"></a>Dostosowywanie szablonów

Aby dostosowywać szablony zgodnie z potrzebami, można użyć Visual Studio Code [rozszerzenia konwertera FHIR](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) . Rozszerzenie zapewnia interaktywne środowisko edycji i ułatwia Pobieranie szablonów opublikowanych przez firmę Microsoft i przykładowych danych. Aby uzyskać szczegółowe informacje, zobacz dokumentację w rozszerzeniu.

## <a name="host-and-use-templates"></a>Hostowanie i używanie szablonów

Zdecydowanie zaleca się hostowanie własnych kopii szablonów w witrynie ACR. Istnieją cztery kroki, które należy wykonać w celu hostowania własnej kopii szablonów i korzystania z nich w operacji $convert danych:

1. Wypchnij szablony do Azure Container Registry.
1. Włącz zarządzaną tożsamość na wystąpieniu usługi Azure API for FHIR.
1. Zapewnianie dostępu do ACR do interfejsu API platformy Azure dla tożsamości zarządzanej FHIR.
1. Zarejestruj serwery ACR w interfejsie API platformy Azure dla usługi FHIR.

### <a name="push-templates-to-azure-container-registry"></a>Wypychanie szablonów do Azure Container Registry

Po utworzeniu wystąpienia ACR można użyć polecenia _konwerter FHIR: push templates_ w [rozszerzeniu konwerter FHIR](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) , aby wypchnąć dostosowane szablony do ACR. Alternatywnie można użyć w tym celu [Narzędzia interfejsu wiersza polecenia zarządzania szablonami](https://github.com/microsoft/FHIR-Converter/blob/main/docs/TemplateManagementCLI.md) .

### <a name="enable-managed-identity-on-azure-api-for-fhir"></a>Włącz zarządzaną tożsamość w interfejsie API platformy Azure dla FHIR

Przejdź do wystąpienia usługi Azure API for FHIR w Azure Portal i wybierz blok **tożsamość** .
Zmień stan na **włączone** , aby włączyć zarządzaną tożsamość w interfejsie API platformy Azure dla FHIR.

![Włącz tożsamość zarządzaną](media/convert-data/fhir-mi-enabled.png)

### <a name="provide-access-of-the-acr-to-azure-api-for-fhir"></a>Zapewnianie dostępu ACR do interfejsu API platformy Azure dla FHIR

Przejdź do bloku Access Control (IAM) w wystąpieniu ACR i wybierz pozycję _Dodaj przypisania ról_.

![Przypisanie roli ACR](media/convert-data/fhir-acr-role-assignment.png)

Udziel roli AcrPull do interfejsu API platformy Azure dla wystąpienia usługi FHIR.

![Dodaj rolę](media/convert-data/fhir-acr-role-add.png)

### <a name="register-the-acr-servers-in-azure-api-for-fhir"></a>Rejestrowanie serwerów ACR w interfejsie API platformy Azure dla FHIR

Możesz zarejestrować do dwudziestu serwerów ACR w interfejsie API platformy Azure dla FHIR.

Zainstaluj interfejs wiersza polecenia healthcareapis z Azure PowerShell w razie konieczności:

```powershell
az extension add -n healthcareapis
```

Zarejestruj serwery ACR w interfejsie API platformy Azure dla FHIR, postępując zgodnie z poniższymi przykładami:

#### <a name="register-a-single-acr-server"></a>Rejestrowanie pojedynczego serwera ACR

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

#### <a name="register-multiple-acr-servers"></a>Rejestrowanie wielu serwerów ACR

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

### <a name="verify"></a>Weryfikacja

Nawiązać połączenie z interfejsem API danych $convert, określając odwołanie do szablonu w parametrze templateCollectionReference.

`<RegistryServer>/<imageName>@<imageDigest>`

## <a name="known-issues-and-workarounds"></a>Znane problemy i rozwiązania

- Niektóre pliki szablonów domyślnych zawierają BOM w formacie UTF-8. W związku z tym wygenerowana wartość identyfikatora będzie zawierać znak BOM. Może to spowodować problem z serwerem FHIR. Obejście polega na ściąganiu szablonów firmy Microsoft przy użyciu rozszerzenia VS Code i wypchnięciu ich do własnych ACR po usunięciu znaków BOM z _ID/_procedur. Liquid_, _id/_udowodniono. Liquid_ i _id/_immunization. Liquid_.

