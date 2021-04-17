---
title: Jak generować i transferować klucze chronione przez moduł HSM dla Azure Key Vault — Azure Key Vault
description: Ten artykuł pomoże Ci zaplanować, wygenerować, a następnie przenieść własne klucze chronione przez moduł HSM do użycia z Azure Key Vault. Znana także jako BYOK lub bring your own key.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/24/2021
ms.author: ambapat
ms.openlocfilehash: e8230404d71b0fe46e8a7cc536d2b5f3a4c5004f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588398"
---
# <a name="import-hsm-protected-keys-for-key-vault-ncipher"></a>Importowanie kluczy chronionych przez moduł HSM Key Vault (nCipher)

> [!WARNING]
> Metoda importowania klucza HSM opisana w tym dokumencie jest **przestarzała** i nie będzie obsługiwana po 30 czerwca 2021 r. Działa tylko z rodziną nCipher nShield modułów HSM z oprogramowaniem układowym 12.40.2 lub 12.50 z poprawkami. Zdecydowanie [zaleca się importowanie kluczy HSM przy](hsm-protected-keys-byok.md) użyciu nowej metody.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby uzyskać dodatkową pewność, gdy używasz Azure Key Vault, możesz zaimportować lub wygenerować klucze w sprzętowych modułach zabezpieczeń (HSM), które nigdy nie opuszczają granic modułu HSM. Ten scenariusz jest często określany jako *używania własnego klucza* (BYOK). Azure Key Vault ochrony kluczy używa rodziny modułów HSM nCipher nShield (zweryfikowanych w modelu FIPS 140-2 poziom 2).


Informacje zawarte w tym temacie ułatwiają planowanie, generowanie, a następnie przenoszenie własnych kluczy chronionych przez moduł HSM do użycia z Azure Key Vault. 

Ta funkcja nie jest dostępna dla Azure (Chiny) — 21Vianet.

> [!NOTE]
> Aby uzyskać więcej informacji na Azure Key Vault, zobacz [Co to jest Azure Key Vault?](../general/overview.md)  
> Aby uzyskać samouczek z wprowadzeniem, który obejmuje tworzenie magazynu kluczy dla kluczy chronionych przez moduł HSM, zobacz Co to [jest Azure Key Vault?](../general/overview.md).

Więcej informacji na temat generowania i przesyłania klucza chronionego przez moduł HSM przez Internet:

* Klucz jest generowany na stacji roboczej w trybie offline, co zmniejsza powierzchnię ataku.
* Klucz jest szyfrowany za pomocą klucza wymiany klucza (KEK), który pozostaje zaszyfrowany do momentu jego transferu do Azure Key Vault HSM. Tylko zaszyfrowana wersja klucza opuszcza oryginalną stację roboczą.
* Zestaw narzędzi ustawia właściwości klucza dzierżawy, które wiążą klucz z Azure Key Vault Security World. Dlatego po otrzymaniu Azure Key Vault HSM odbierze i odszyfruje klucz, tylko te moduły HSM będą z niego korzystać. Nie można wyeksportować klucza. To powiązanie jest wymuszane przez nCipher HSMs.
* Klucz wymiany klucza (KEK) używany do szyfrowania klucza jest generowany wewnątrz Azure Key Vault HSM i nie można go eksportować. Sprzętowe moduły zabezpieczeń wymagają, aby poza ich obrębem nie był dostępny żaden jasny obraz klucza wymiany klucza (KEK). Ponadto zestaw narzędzi zawiera zaświadczenia z nCipher, że KEK nie jest eksportowalny i został wygenerowany wewnątrz oryginalnego modułu HSM, który został wyprodukowany przez nCipher.
* Zestaw narzędzi zawiera zaświadczenia z nCipher, że Azure Key Vault security world został również wygenerowany na oryginalnym hsm produkowane przez nCipher. To potwierdzenie potwierdza, że firma Microsoft korzysta z oryginalnego sprzętu.
* Firma Microsoft używa oddzielnych kEK i oddzielnych obszarów zabezpieczeń Security World w każdym regionie geograficznym. Takie rozdzielenie zapewnia, że klucz może być używany tylko w centrach danych w regionie, w którym został zaszyfrowany. Na przykład klucza europejskiego klienta nie można używać w centrach danych w Ameryce Północnej ani Azji.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Więcej informacji na temat modułów HSM i usługi firmy Microsoft

NCipher Security, Entrust Datacard, jest liderem na rynku modułów HSM ogólnego przeznaczenia, zapewniając wiodące w świecie organizacje, zapewniając zaufanie, integralność i kontrolę w zakresie informacji i aplikacji o krytycznym znaczeniu dla firmy. Rozwiązania kryptograficzne nCipher zabezpieczają nowo pojawiające się technologie — chmurę, IoT, łańcuch bloków, płatności cyfrowe — i pomagają spełnić nowe wymagania dotyczące zgodności, korzystając z tej samej sprawdzonej technologii, od których obecnie zależą globalne organizacje, aby chronić dane poufne, komunikację sieciową i infrastrukturę przedsiębiorstwa. Rozwiązanie nCipher zapewnia zaufanie aplikacjom o krytycznym znaczeniu dla firmy, zapewniając integralność danych i zapewniając klientom pełną kontrolę — dziś, jutro, przez cały czas.

Firma Microsoft współpracowała z usługą nCipher Security, aby ulepszyć najnowocześniejsze moduły HSM. Opracowane udoskonalenia pozwalają użytkownikom czerpać korzyści z typowych zalet usług hostowanych bez potrzeby rezygnacji z kontroli nad kluczami. Ulepszenia te umożliwiają firmie Microsoft w szczególności zarządzanie sprzętowymi modułami zabezpieczeń, zdejmując ten ciężar z użytkownika. Jako usługa w chmurze Azure Key Vault w górę w krótkim czasie, aby sprostać skokom użycia w organizacji. W tym samym czasie klucz jest chroniony wewnątrz modułów HSM firmy Microsoft: Zachowujesz kontrolę nad cyklem życia klucza, ponieważ generujesz klucz i przenosisz go do modułów HSM firmy Microsoft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementowanie używania własnego klucza (BYOK) na potrzeby usługi Azure Key Vault

Skorzystaj z poniższych informacji i procedur, jeśli wygenerujesz własny klucz chroniony przez moduł HSM, a następnie przeniossz go do Azure Key Vault scenariusza BYOK (Bring Your Own Key).

## <a name="prerequisites-for-byok"></a>Wymagania wstępne dotyczące funkcji BYOK

Zapoznaj się z następującą tabelą, aby uzyskać listę wymagań wstępnych dla aplikacji BYOK (Bring Your Own Key) Azure Key Vault.

| Wymaganie | Więcej informacji |
| --- | --- |
| Subskrypcja platformy Azure |Aby utworzyć konto Azure Key Vault, potrzebujesz subskrypcji platformy Azure: [Utwórz konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/) |
| Warstwa Azure Key Vault Premium do obsługi kluczy chronionych przez moduł HSM |Aby uzyskać więcej informacji na temat warstw usług i możliwości usługi Azure Key Vault, zobacz witrynę [internetową Azure Key Vault Cennik.](https://azure.microsoft.com/pricing/details/key-vault/) |
| NCipher nShield HSMs, smartcards, and support software (Moduły HSM nCipher nShield, karty inteligentne i oprogramowanie pomocy technicznej) |Musisz mieć dostęp do sprzętowego modułu zabezpieczeń nCipher oraz podstawową wiedzę operacyjną na temat modułów HSM nCipher nShield. Aby uzyskać listę zgodnych modeli, zobacz [nCipher nShield Hardware Security Module](https://go.ncipher.com/rs/104-QOX-775/images/nCipher_nShield_Family_Brochure.pdf?_ga=2.106120835.1607422418.1590478092-577009923.1587131206) (Sprzętowy moduł zabezpieczeń nShield) lub aby kupić moduł HSM, jeśli go nie masz. |
| Następujący sprzęt i oprogramowanie:<ol><li>Stacja robocza w trybie offline z systemem operacyjnym Windows 7 lub nCipher nShield w wersji co najmniej 11.50.<br/><br/>Jeśli na tej stacji roboczej działa system Windows 7, należy [zainstalować Microsoft .NET Framework 4.5.](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe)</li><li>Stacja robocza, która jest podłączona do Internetu i ma zainstalowany system operacyjny Windows 7 lub [Azure PowerShell](/powershell/azure/) minimalną wersję **1.1.0.**</li><li>Dysk USB lub inne przenośne urządzenie pamięci masowej, które ma co najmniej 16 MB wolnego miejsca.</li></ol> |Ze względów bezpieczeństwa odradza się podłączanie pierwszej stacji roboczej do sieci. Jednak to zalecenie nie jest wymuszane programowo.<br/><br/>W poniższych instrukcjach ta stacja robocza jest nazywana odłączonym stacją roboczą.</p></blockquote><br/>Ponadto jeśli klucz dzierżawy jest dla sieci produkcyjnej, zalecamy użycie drugiej, oddzielnej stacji roboczej do pobrania zestawu narzędzi i przekazania klucza dzierżawy. Do celów testowych można jednak użyć pierwszej stacji roboczej.<br/><br/>W poniższych instrukcjach druga stacja robocza jest określana jako stacja robocza połączona z Internetem.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generowanie i przenoszenie klucza do Azure Key Vault HSM

Aby wygenerować i przenieść klucz do modułu HSM, należy wykonać pięć następujących Azure Key Vault:

* [Krok 1. Przygotowanie stacji roboczej podłączonej do Internetu](#step-1-prepare-your-internet-connected-workstation)
* [Krok 2. Przygotowanie odłączonego stacji roboczej](#step-2-prepare-your-disconnected-workstation)
* [Krok 3. Generowanie klucza](#step-3-generate-your-key)
* [Krok 4. Przygotowanie klucza do przesłania](#step-4-prepare-your-key-for-transfer)
* [Krok 5. Przesłanie klucza do usługi Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Krok 1. Przygotowanie stacji roboczej podłączonej do Internetu

W tym pierwszym kroku wykonaj następujące procedury na stacji roboczej, która jest połączona z Internetem.

### <a name="step-11-install-azure-powershell"></a>Krok 1.1. Instalowanie Azure PowerShell

Z połączonej z Internetem stacji roboczej pobierz i zainstaluj moduł Azure PowerShell, który zawiera polecenia cmdlet do zarządzania Azure Key Vault. Aby uzyskać instrukcje instalacji, [zobacz Jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/).

### <a name="step-12-get-your-azure-subscription-id"></a>Krok 1.2. Uzyskiwanie identyfikatora subskrypcji platformy Azure

Uruchom sesję Azure PowerShell i zaloguj się do konta platformy Azure przy użyciu następującego polecenia:

```Powershell
   Connect-AzAccount
```
W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Następnie użyj polecenia [Get-AzSubscription:](/powershell/module/az.accounts/get-azsubscription)

```powershell
   Get-AzSubscription
```
W danych wyjściowych znajdź identyfikator subskrypcji, która będzie Azure Key Vault. Ten identyfikator subskrypcji będzie potrzebny później.

Nie zamykaj okna Azure PowerShell danych.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Krok 1.3. Pobieranie zestawu narzędzi BYOK dla Azure Key Vault

Przejdź do Centrum pobierania Microsoft i pobierz zestaw [narzędzi Azure Key Vault BYOK](https://www.microsoft.com/download/details.aspx?id=45345) dla regionu geograficznego lub wystąpienia platformy Azure. Użyj następujących informacji, aby zidentyfikować nazwę pakietu do pobrania i odpowiadający mu skrót pakietu SHA-256:

---
**Stany Zjednoczone:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Europie:**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**Azji:**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**Ameryka Łacińska:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

---
**Japonia:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**Korea:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

---
**Republika Południowej Afryki:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAAAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**Zea:**

KeyVault-BYOK-Tools-UAE.zip

FADE80210B06962AA0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Australia:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

---
[**Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**US Government DOD:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**Kanada:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**Niemcy:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

---
**Germany Public:**

KeyVault-BYOK-Tools-Germany-Public.zip

54534936D0A0C99C8117DB724C34A5E50FD204CFCBD75C78972B785865364A29

---
**Indie:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**Francja:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Wielka Brytania:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**Szwajcaria:**

KeyVault-BYOK-Tools-Switzerland.zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


Aby sprawdzić integralność pobranego zestawu narzędzi byOK, z sesji Azure PowerShell użyj polecenia cmdlet [Get-FileHash.](/powershell/module/microsoft.powershell.utility/get-filehash)

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Zestaw narzędzi zawiera:

* Pakiet klucza wymiany klucza (KEK) o nazwie rozpoczynającej się od **BYOK-KEK-pkg-.**
* Pakiet Security World o nazwie rozpoczynającej się od **BYOK-SecurityWorld-pkg-.**
* Skrypt w języku Python **o nazwie verifykeypackage.py.**
* Plik wykonywalny wiersza polecenia o nazwie **KeyTransferRemote.exe** skojarzonych bibliotek DLL.
* Pakiet pakiet redystrybucyjny programu Visual C++ o nazwie **vcredist_x64.exe.**

Skopiuj pakiet na dysk USB lub do innego przenośnego urządzenia pamięci masowej.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Krok 2. Przygotowanie odłączonych stacji roboczych

W tym drugim kroku wykonaj następujące procedury na stacji roboczej, która nie jest połączona z siecią (Internetem lub siecią wewnętrzną).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Krok 2.1. Przygotowanie odłączonych stacji roboczych przy użyciu modułu HSM nCipher nShield

Zainstaluj oprogramowanie obsługi nCipher na komputerze z systemem Windows, a następnie dołącz do tego komputera moduł HSM nCipher nShield.

Upewnij się, że narzędzia nCipher znajdują się w ścieżce (**%nfast_home%\bin).** Na przykład wpisz następujące polecenie:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Aby uzyskać więcej informacji, zobacz podręcznik użytkownika dołączony do modułu HSM nShield.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Krok 2.2. Instalowanie zestawu narzędzi BYOK na odłączonym stacji roboczej

Skopiuj pakiet zestawu narzędzi BYOK z dysku USB lub innego przenośnego urządzenia pamięci masowej, a następnie wykonaj następujące czynności:

1. Wyodrębnij pliki z pobranego pakietu do dowolnego folderu.
2. Z poziomu tego folderu uruchom plik vcredist_x64.exe.
3. Postępuj zgodnie z instrukcjami, aby zainstalować Visual C++ środowiska uruchomieniowego dla Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Krok 3. Generowanie klucza

W tym trzecim kroku wykonaj następujące procedury na odłączonych stacjach roboczych. Aby wykonać ten krok, moduł HSM musi być w trybie inicjowania. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Krok 3.1. Zmiana trybu HSM na "I"

Jeśli używasz nCipher nShield Edge, aby zmienić tryb: 1. Użyj przycisku Tryb, aby wyróżnić wymagany tryb. 2. W ciągu kilku sekund naciśnij i przytrzymaj przycisk Wyczyść przez kilka sekund. Jeśli tryb zmieni się, dioda LED nowego trybu przestanie migać i pozostanie oświetlona. Dioda LED stanu może migać nieregularnie przez kilka sekund, a następnie będzie regularnie migać, gdy urządzenie będzie gotowe. W przeciwnym razie urządzenie pozostanie w bieżącym trybie z diodą LED odpowiedniego trybu.

### <a name="step-32-create-a-security-world"></a>Krok 3.2. Tworzenie świata zabezpieczeń

Uruchom wiersz polecenia i uruchom program nCipher new-world.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Uruchomienie programu powoduje utworzenie pliku **Security World** w lokalizacji %NFAST_KMDATA%\local\world, co odpowiada lokalizacji folderu C:\ProgramData\nCipher\Key Management Data\local. Możesz użyć różnych wartości dla kworum, ale w naszym przykładzie zostanie wyświetlony monit o wprowadzenie trzech pustych kart i pinezek dla każdej z nich. Następnie dowolne dwie karty zapewniają pełny dostęp do świata zabezpieczeń. Karty te tworzą od tej chwili **zestaw kart administratora** nowego środowiska zabezpieczeń Security World.

> [!NOTE]
> Jeśli moduł HSM nie obsługuje nowszego zestawu szyfrowania DLf3072s256mRijndael, możesz zastąpić wartość --cipher-suite= DLf3072s256mRijndael z --cipher-suite=DLf1024s160mRijndael
> 
> Zabezpieczenia world utworzone za new-world.exe dostarczanym z oprogramowaniem nCipher w wersji 12.50 nie są zgodne z tą procedurą BYOK. Dostępne są dwie opcje:
> 1) Obniżyć wersję oprogramowania nCipher do wersji 12.40.2 w celu utworzenia nowego świata zabezpieczeń.
> 2) Skontaktuj się z pomocą techniczną aplikacji nCipher i poproś o podanie poprawki dla wersji oprogramowania 12.50, która umożliwia korzystanie z wersji 12.40.2 usługi new-world.exe zgodnej z tą procedurą BYOK.

Następnie wykonaj poniższe czynności:

* Utwórz kopię zapasową pliku środowiska zabezpieczeń. Zabezpiecz i chroń plik środowiska zabezpieczeń, karty administratora oraz ich kody PIN, a także upewnij się, że nikt nie ma dostępu do więcej niż jednej karty.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Krok 3.3. Zmiana trybu HSM na "O"

Jeśli używasz nCipher nShield Edge, aby zmienić tryb: 1. Użyj przycisku Tryb, aby wyróżnić wymagany tryb. 2. W ciągu kilku sekund naciśnij i przytrzymaj przycisk Wyczyść przez kilka sekund. Jeśli tryb zmieni się, dioda LED nowego trybu przestaje migać i pozostaje oświetlona. Dioda LED stanu może migać nieregularnie przez kilka sekund, a następnie będzie regularnie migać, gdy urządzenie będzie gotowe. W przeciwnym razie urządzenie pozostanie w bieżącym trybie z diodą LED odpowiedniego trybu.

### <a name="step-34-validate-the-downloaded-package"></a>Krok 3.4. Weryfikowanie pobranego pakietu

Ta czynność jest opcjonalna, ale zaleca się jej wykonanie w celu upewnienia się, że:

* Klucz wymiany klucza dołączony do zestawu narzędzi został wygenerowany z oryginalnego modułu HSM nCipher nShield.
* Skrót security world, który znajduje się w zestawie narzędzi został wygenerowany w oryginalnym nCipher nShield HSM.
* Klucz wymiany klucza nie może zostać wyeksportowany.

> [!NOTE]
> Aby zweryfikować pobrany pakiet, moduł HSM musi być połączony, włączony i musi mieć na nim świat zabezpieczeń (taki jak właśnie utworzony).

Aby zweryfikować pobrany pakiet:

1. Uruchom skrypt verifykeypackage.py, wpisując jedną z następujących wartości w zależności od regionu geograficznego lub wystąpienia platformy Azure:

   * Dla Ameryki Północnej:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
      ```
   * Dla Europy:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
      ```
   * Azja:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
        ```
   * Na Ameryka Łacińska:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
        ```
   * Japonia:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
        ```
   * For Korea:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
        ```
   * Dla Południowej Afryki:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
        ```
   * W Przypadku Zjednoczonego Emiratu Emiratów Zjednoczonych:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
        ```
   * Dla Australii:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
        ```
   * Na [Azure Government](https://azure.microsoft.com/features/gov/), które używa wystąpienia platformy Azure dla instytucji rządowych USA:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
        ```
   * W przypadku dod dla instytucji rządowych USA:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
        ```
   * Kanada:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
        ```
   * Niemcy:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * For Germany Public:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * Indie:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
      ```
   * Francja:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
        ```
   * Dla Zjednoczonego Królestwa:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
        ```
   * Dla Niemiec:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1
        ```

     > [!TIP]
     > Oprogramowanie nCipher nShield zawiera język Python w lokalizacji %NFAST_HOME%\python\bin
     >
     >
2. Upewnij się, że widzisz następujące informacje, co oznacza pomyślne sprawdzenie poprawności: **Wynik: POWODZENIE**

Ten skrypt weryfikuje łańcuch podpisatora aż do klucza głównego nShield. Skrót klucza głównego jest osadzony w skrypcie; jego wartość powinna wynosić **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Możesz również potwierdzić tę wartość oddzielnie, odwiedzając [witrynę internetową nCipher.](https://www.ncipher.com)

Teraz możesz utworzyć nowy klucz.

### <a name="step-35-create-a-new-key"></a>Krok 3.5. Tworzenie nowego klucza

Wygeneruj klucz przy użyciu programu nCipher nShield **generatekey.**

Uruchom następujące polecenie, aby wygenerować klucz:

```azurepowershell
generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=
```

Podczas uruchamiania tego polecenia należy zastosować się do następujących instrukcji:

* Parametr *protect* musi mieć ustawioną wartość **module**, jak przedstawiono. Spowoduje to utworzenie klucza chronionego przez moduł. Zestaw narzędzi BYOK nie obsługuje kluczy chronionych z użyciem protokołu OCS.
* Zamień wartość *contosokey* dla pozycji **ident** i **plainname** na dowolną wartość ciągu. Aby zminimalizować koszty administracyjne i zmniejszyć ryzyko błędów, zalecamy użycie tej samej wartości dla obu tych czynników. Wartość **ident** musi zawierać tylko cyfry, łączniki i małe litery.
* W tym przykładzie parametr pubexp został pozostawiony pusty (wartość domyślna), można jednak określić konkretne jego wartości. Aby uzyskać więcej informacji, zobacz [dokumentację nCipher.](https://www.entrust.com/-/media/documentation/brochures/entrust-nshield-general-purpose-hsms-br-a4.pdf)

To polecenie tworzy plik tokenizowanego klucza w folderze %NFAST_KMDATA%\local o nazwie rozpoczynającej  się od **key_simple_**, a następnie identyfikator określony w poleceniu . Na przykład: **key_simple_contosokey**. Ten plik zawiera zaszyfrowany klucz.

Utwórz w bezpiecznej lokalizacji kopię zapasową tego pliku stokenizowanego klucza.

> [!IMPORTANT]
> W przypadku późniejszego przeniesienia klucza do usługi Azure Key Vault firma Microsoft nie może wyeksportować tego klucza z powrotem do użytkownika, dlatego niezwykle ważne jest, aby można było bezpiecznie chować kopię zapasową klucza i zabezpieczeń w świecie zabezpieczeń. Skontaktuj [się z nCipher,](https://www.ncipher.com/about-us/contact-us) aby uzyskać wskazówki i najlepsze rozwiązania dotyczące kopii zapasowej klucza.
>


Teraz możesz przenieść klucz do Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Krok 4. Przygotowanie klucza do przesłania

W tym czwartym kroku wykonaj następujące procedury na odłączonych stacjach roboczych.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Krok 4.1. Utworzenie kopii klucza z obniżonymi uprawnieniami

Otwórz nowy wiersz polecenia i zmień bieżący katalog na lokalizację, w której został rozpakowany plik zip BYOK. Aby zmniejszyć uprawnienia do klucza, w wierszu polecenia uruchom jedną z następujących czynności, w zależności od regionu geograficznego lub wystąpienia platformy Azure:

* Dla Ameryki Północnej:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-
   ```
* Dla Europy:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
   ```
* Azja:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
   ```
* Na Ameryka Łacińska:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
   ```
* Japonia:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
   ```
* For Korea:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
   ```
* Dla Południowej Afryki:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
   ```
* W przypadku Zjednoczonego Emiratu Emiratów Zjednoczonych:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
   ```
* For Australia:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
   ```
* W [Azure Government](https://azure.microsoft.com/features/gov/), które używa wystąpienia platformy Azure dla instytucji rządowych USA:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
   ```
* W przypadku dod dla instytucji rządowych USA:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
   ```
* Kanada:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
   ```
* Niemcy:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* For Germany Public:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* Indie:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
   ```
* Francja:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
   ```
* Dla Zjednoczonego Królestwa:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
   ```
* Dla Niemiec:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1
   ```

Po uruchomieniu tego polecenia zastąp *wartość contosokey* tą samą wartością, która jest określona w kroku **Krok 3.5.** Utwórz nowy klucz w kroku [Generowanie](#step-3-generate-your-key) klucza.

Zostaniesz poproszony o podłączenie kart administratora security world.

Po zakończeniu wykonywania polecenia w pliku o nazwie zostanie wyświetlony tekst **Result: SUCCESS (Wynik:** POWODZENIE), a kopia klucza o ograniczonych uprawnieniach będzie key_xferacId_ \<contosokey> .

Za pomocą narzędzi nCipher nShield można sprawdzić listy ACLS przy użyciu następujących poleceń:

* aclprint.py:

   ```cmd
   "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
   ```
* kmfile-dump.exe:

   ```cmd
   "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
   ```
  Po uruchomieniu tych poleceń zastąp wartość contosokey wartością określoną w kroku **Krok 3.5.** Tworzenie nowego klucza w kroku [Generowanie](#step-3-generate-your-key) klucza.

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Krok 4.2. Szyfrowanie klucza przy użyciu klucza wymiany kluczy firmy Microsoft

Uruchom jedno z następujących poleceń w zależności od regionu geograficznego lub wystąpienia platformy Azure:

* Dla Ameryki Północnej:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Dla Europy:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Azja:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Na Ameryka Łacińska:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Japonia:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* For Korea:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Dla Południowej Afryki:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* W Przypadku Zjednoczonego Emiratu Emiratów Zjednoczonych:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Dla Australii:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* W [Azure Government](https://azure.microsoft.com/features/gov/), które używa wystąpienia platformy Azure dla instytucji rządowych USA:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* W przypadku dod dla instytucji rządowych USA:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Kanada:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Niemcy:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* For Germany Public:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Indie:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Francja:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Dla Zjednoczonego Królestwa:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Dla Niemiec:

  ```azurepowershell
  KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
  ```


Podczas uruchamiania tego polecenia należy zastosować się do następujących instrukcji:

* Zastąp *element contosokey* identyfikatorem użytym do wygenerowania klucza w kroku **Krok 3.5.** Tworzenie nowego klucza w kroku [Generowanie](#step-3-generate-your-key) klucza.
* Zastąp *element SubscriptionID* identyfikatorem subskrypcji platformy Azure, która zawiera Magazyn kluczy. Ta wartość została pobrana wcześniej w kroku **Krok 1.2.** Uzyskiwanie identyfikatora subskrypcji platformy Azure w kroku [Przygotowywanie](#step-1-prepare-your-internet-connected-workstation) stacji roboczej połączonej z Internetem.
* Zastąp *tekst ContosoFirstHSMKey* etykietą używaną dla nazwy pliku wyjściowego.

Po pomyślnym zakończeniu tego procesu zostanie wyświetlony komunikat **Result: SUCCESS (Wynik: POWODZENIE)** i w bieżącym folderze znajduje się nowy plik o następującej nazwie: KeyTransferPackage-ContosoFirstHSMkey .byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Krok 4.3. Kopiowanie pakietu transferu kluczy na stację roboczą podłączaną do Internetu

Użyj dysku USB lub innego przenośnego magazynu, aby skopiować plik wyjściowy z poprzedniego kroku (KeyTransferPackage-ContosoFirstHSMkey.byok) na stację roboczą podłączaną do Internetu.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Krok 5. Przesłanie klucza do usługi Azure Key Vault

W tym ostatnim kroku na stacji roboczej podłączonej do Internetu użyj polecenia cmdlet [Add-AzKeyVaultKey,](/powershell/module/az.keyvault/add-azkeyvaultkey) aby przekazać pakiet transferu kluczy skopiowany z odłączonego stacji roboczej do modułu HSM Azure Key Vault:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Jeśli przekazywanie powiedzie się, zostaną wyświetlone właściwości właśnie dodanego klucza.

## <a name="next-steps"></a>Następne kroki

Teraz możesz używać tego klucza chronionego przez moduł HSM w magazynie kluczy. Aby uzyskać więcej informacji, zobacz to porównanie cen i [funkcji.](https://azure.microsoft.com/pricing/details/key-vault/)