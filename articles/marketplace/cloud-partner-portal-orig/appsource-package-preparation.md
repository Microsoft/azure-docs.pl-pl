---
title: Przygotowanie pakietu AppSource | Portal Azure Marketplace
description: Explanaion przygotowywanie i kompilowanie pakietów AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 76f8cbd6cb16b585a7dbda7b2ffa5eeeeb1b68d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280613"
---
# <a name="appsource-package-preparation"></a>Przygotowywanie pakietu AppSource

Oprócz pliku rozwiązania. zip wymagany jest **pakiet AppSource**. Jest to plik. zip, który zawiera wszystkie zasoby, które są konieczne do zautomatyzowania procesu wdrażania rozwiązania w środowisku programu CRM klienta. **Pakiet AppSource** ma następujące składniki

* Pakiet narzędzia do wdrażania pakietów
* Plik **Content_Types. XML** z używanymi zasobami
* plik XML z danymi specyficznymi dla aplikacji
* logo 32x32, które będzie wyświetlane na liście w centrum administracyjnym
* Postanowienia licencyjne, zasady ochrony prywatności

Poniższe kroki ułatwią utworzenie pakietu AppSource.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Tworzenie pakietu dla narzędzia do wdrażania pakietów

Pakiet narzędzia do wdrażania pakietów jest jedną z części pakietu AppSource.

Aby utworzyć pakiet dla narzędzia do wdrażania pakietów, należy wykonać następujące instrukcje: [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx). Po zakończeniu pakiet obejmuje następujące zasoby:

1. Folder pakietu: zawiera wszystkie rozwiązania, dane konfiguracji, pliki płaskie i zawartość pakietu. _Uwaga: w poniższym przykładzie przyjęto założenie, że folder pakietu nosi nazwę "PkgFolder"_
2. DLL: zestaw zawiera niestandardowy kod dla pakietu. _Uwaga: w poniższym przykładzie przyjęto założenie, że ten plik ma nazwę "MicrosoftSample. dll"._

Teraz musisz utworzyć plik o nazwie "**Content_Types. XML**", aby wyświetlić listę wszystkich rozszerzeń zasobów, które są częścią pakietu. Oto przykładowy kod dla tego pliku.

    <?xml version="1.0" encoding="utf-8"?>
        <Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
        <Default Extension="xml" ContentType="application/octet-stream" />
        <Default Extension="xaml" ContentType="application/octet-stream" />
        <Default Extension="dll" ContentType="application/octet-stream" />
        <Default Extension="zip" ContentType="application/octet-stream" />
        <Default Extension="jpg" ContentType="application/octet-stream" />
        <Default Extension="gif" ContentType="application/octet-stream" />
        <Default Extension="png" ContentType="application/octet-stream" />
        <Default Extension="htm" ContentType="application/octet-stream" />
        <Default Extension="html" ContentType="application/octet-stream" />
        <Default Extension="db" ContentType="application/octet-stream" />
        <Default Extension="css" ContentType="application/octet-stream" />
    </Types>

Ostatnim krokiem jest zaplikowanie zip poniżej w jednym pliku. Wywołaj **pakiet IT Package. zip**. Będzie zawierać

1. PkgFolder (w tym wszystkie elementy wewnątrz folderu)
2. bibliotece
3. **Content_Types. XML**

Procedura tworzenia pliku Package. zip:

1. Umieść folder pakietu, plik **Content_Types. XML** i PackageName. dll w jednym katalogu.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Zaznacz wszystkie elementy w folderze, kliknij prawym przyciskiem myszy i wybierz polecenie Wyślij do skompresowanego folderu (zip).

![CRMScreenShot3](media/CRMScreenShot3.png)

1. Zmień nazwę na Package. zip

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Tworzenie pakietu AppSource

Pakiet AppSource wymaga kilku dodatkowych plików.

1. jpg (rozpoznawanie 32x32)
2. HTML (plik w formacie HTML)
3. **Content_Types. XML** (analogicznie jak powyżej)
4. xml

Oto przykładowy kod dla Input. XML. Zobacz definicje w poniższej tabeli.

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>https://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**Miejscu**

|Pole|Szczegóły|
|---|---|
|ProviderName|Kim jest rozwiązanie. Jeśli zespół firmy Microsoft, powinien to być firma Microsoft.|
|PackageFile |Zasoby wdrażania pakietów spakowane razem z plikiem Content\_Types. XML. Ten plik zip powinien zawierać zestaw Package Deployer i folder z zasobami wdrażania pakietu. To jest plik Package. zip|
|SolutionAnchorName |Nazwa pliku zip rozwiązania w programie do wdrażania pakietów, który jest używany jako nazwa wyświetlana i opis zasobów rozwiązania.|
| StartDate| Jest to data udostępnienia pakietu rozwiązania. Format to MM/DD/RRRR|
|EndDate|Jest to data, o którą pakiet rozwiązania zostanie zatrzymany. Format to MM/DD/RRRR |
|SupportedCountries |Jest to rozdzielana przecinkami lista krajów/regionów, które powinny zobaczyć ten pakiet. Skontaktuj się z Usługi online, aby uzyskać listę wszystkich bieżących kodów krajów. W tym momencie ten zapis listy to: AE, AL, AM, AO, AR, AT, AU, AZ, BA, BB, BD, do, BG, BH, BM, mld USD, BO, BR, przez, CA, CH, CI, CL, CM,, CR, CV, CW, CY, CZ, DE, DK, DO, DZ, we, EE, EG,, FI, HN, w tym,, , W, IQ, IS, IT, JM, JO, JP, KE, KG, KN, KR, KW, KY, KZ, LB, LK, LT, LU, LV, LY, MA, MC, MD, ME, MK, MN, MO, MT, SO, SO, MY, NG, NI, NL, NO, NZ, OM, PA, PE, PH, PK, PL, PR, PS, PT, PR, DO, DO, RO, RS, RU, RW, SA, SE, SG, SI, SK, SN, SV, TH, TM, TN , TR, TT, TW, UA, US, UY, UZ, VE, VI, VN, ZA, ZW |
|LearnMoreLink | Adres URL do strony więcej informacji dla tego pakietu. |
|Ustawień regionalnych|Jedno wystąpienie tego węzła dla każdego języka środowiska użytkownika, który ma być obsługiwany w preferowanym środowisku środowiska. Ten węzeł zawiera elementy podrzędne opisujące ustawienia regionalne, logo i warunki dla każdego języka|
|Ustawienia regionalne: PackageLocale. Code|Identyfikator LCID języka dla tego węzła. Przykład: angielski (Stany Zjednoczone) to 1033|
|Ustawienia regionalne: PackageLocale. IsDefault|Wskazuje, że jest to język domyślny. Jest on używany jako język powrotu, jeśli język UX wybrany przez klienta nie jest dostępny.|
|Ustawienia regionalne: logo|Jest to możliwe, jeśli logo, którego chcesz użyć dla tego pakietu. Rozmiar ikony to 32x32. Dozwolone formaty to PNG i JPG|
|Ustawienia regionalne: warunki: PackageTerm. File|Jest to nazwa pliku dokumentu HTML zawierającego postanowienia licencyjne.|

Oto gdzie będzie wyświetlane logo:

![CRMScreenShot5](media/CRMScreenShot5.png)

Ostatnim krokiem jest zaplikowanie zip poniżej w jednym pliku.

1. plik zip (utworzony wcześniej)
2. **Content_Types. XML**
3. xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Zmień nazwę pliku na odpowiedni dla aplikacji. Preferujemy dołączenie nazwy firmy i nazwy aplikacji. Na przykład: **_Microsoft_SamplePackage. zip**.
