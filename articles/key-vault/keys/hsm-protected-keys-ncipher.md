---
title: Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault-Azure Key Vault
description: Ten artykuł ułatwia planowanie, generowanie i transferowanie własnych kluczy chronionych przez moduł HSM w celu korzystania z Azure Key Vault. Znany również jako BYOK lub własny klucz.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/24/2021
ms.author: ambapat
ms.openlocfilehash: f7761cf011a3a678bb7609e1063ac6ebec90d395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499190"
---
# <a name="import-hsm-protected-keys-for-key-vault-ncipher"></a>Importuj klucze chronione przez moduł HSM dla Key Vault (oprogramowanie wspomagające nCipher)

> [!WARNING]
> Metoda importowania klucza HSM opisana w tym dokumencie jest **przestarzała** i nie będzie obsługiwana w przyszłości. Działa on tylko z rodziną oprogramowanie wspomagające nCipher sprzętowego nshield of sprzętowych modułów zabezpieczeń z oprogramowaniem układowym 12.40.2 lub 12,50 z poprawką. Używanie [nowej metody do importowania kluczy HSM](hsm-protected-keys-byok.md) jest zdecydowanie zalecane.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby uzyskać gwarancję, w przypadku korzystania z Azure Key Vault można importować lub generować klucze w sprzętowych modułach zabezpieczeń (sprzętowych modułów zabezpieczeń), które nigdy nie opuszczają granicy modułu HSM. Ten scenariusz jest często określany jako *używania własnego klucza* (BYOK). W celu ochrony kluczy Azure Key Vault używa rodziny oprogramowanie wspomagające nCipher sprzętowego nshield sprzętowych modułów zabezpieczeń (zweryfikowany poziom 2 trybu FIPS 140-2).


Informacje przedstawione w tym temacie ułatwiają planowanie, generowanie i transferowanie własnych kluczy chronionych przez moduł HSM w celu korzystania z Azure Key Vault. 

Ta funkcja jest niedostępna dla platformy Azure w Chinach.

> [!NOTE]
> Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../general/overview.md)  
> Aby zapoznać się z samouczkiem wprowadzającym, które obejmuje tworzenie magazynu kluczy dla kluczy chronionych przez moduł HSM, zobacz [co to jest Azure Key Vault?](../general/overview.md).

Więcej informacji o generowaniu i przesyłaniu klucza chronionego przez moduł HSM przez Internet:

* Klucz jest generowany z stacji roboczej offline, co zmniejsza obszar narażony na ataki.
* Klucz jest szyfrowany przy użyciu klucza wymiany klucza (KEK), który pozostaje zaszyfrowany, dopóki nie zostanie przetransferowany do Azure Key Vault sprzętowych modułów zabezpieczeń. Tylko zaszyfrowana wersja klucza opuszcza pierwotną stację roboczą.
* Zestaw narzędzi ustawia właściwości klucza dzierżawy, który wiąże klucz ze światem zabezpieczeń Azure Key Vault. Tak więc po Azure Key Vault odsprzętowych modułów zabezpieczeń i odszyfrowania klucza tylko te sprzętowych modułów zabezpieczeń mogą go używać. Nie można wyeksportować klucza. To powiązanie jest wymuszane przez oprogramowanie wspomagające nCipher sprzętowych modułów zabezpieczeń.
* Klucz wymiany klucza (KEK) służący do szyfrowania klucza jest generowany wewnątrz Azure Key Vault sprzętowych modułów zabezpieczeń i nie można go eksportować. Sprzętowe moduły zabezpieczeń wymagają, aby poza ich obrębem nie był dostępny żaden jasny obraz klucza wymiany klucza (KEK). Ponadto zestaw narzędzi zawiera zaświadczanie od oprogramowanie wspomagające nCipher, że KEK nie można eksportować i został wygenerowany w oryginalnym module HSM, który został wyprodukowany przez oprogramowanie wspomagające nCipher.
* Zestaw narzędzi zawiera zaświadczanie od oprogramowanie wspomagające nCipher, że świat zabezpieczeń Azure Key Vault został również wygenerowany na oryginalnym module HSM wytwarzanym przez oprogramowanie wspomagające nCipher. To zaświadczanie potwierdza, że firma Microsoft używa oryginalnego sprzętu.
* W każdym regionie geograficznym firma Microsoft używa oddzielnych KEKs i oddzielenia zabezpieczeń. Ta separacja zapewnia, że klucz może być używany tylko w centrach danych w regionie, w którym została zaszyfrowana. Na przykład klucz z klienta Europejskiego nie może zostać użyty w centrach danych w Ameryce Północnej ani Azji.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Więcej informacji na temat oprogramowanie wspomagające nCipher sprzętowych modułów zabezpieczeń i usług firmy Microsoft

Oprogramowanie wspomagające nCipher bezpieczeństwo, firma Entrust Datacard, jest liderem na rynku modułu HSM ogólnego przeznaczenia, umożliwiając organizacjom wiodącym na świecie oferowanie zaufania, integralności i kontroli do ważnych informacji i aplikacji. rozwiązania kryptograficzne oprogramowanie wspomagające nCipher zabezpieczają nowe technologie — Cloud, IoT, łańcucha bloków, płatności cyfrowe — i pomagają w spełnieniu nowych mandatów związanych z zgodnością, korzystając z tej samej sprawdzonej technologii, w której organizacje globalne zależą od dzisiaj do ochrony przed zagrożeniami dotyczącymi poufnych danych, komunikacji sieciowej i infrastruktury przedsiębiorstwa. Oprogramowanie wspomagające nCipher zapewnia zaufanie do aplikacji o krytycznym znaczeniu dla firmy, zapewniając integralność danych i wprowadzanie klientów w całościowej kontroli — dzisiaj, jutro, przez cały czas.

Firma Microsoft współpracuje z oprogramowanie wspomagające nCipher zabezpieczeniami w celu zwiększenia stanu kompozycji dla sprzętowych modułów zabezpieczeń. Opracowane udoskonalenia pozwalają użytkownikom czerpać korzyści z typowych zalet usług hostowanych bez potrzeby rezygnacji z kontroli nad kluczami. Ulepszenia te umożliwiają firmie Microsoft w szczególności zarządzanie sprzętowymi modułami zabezpieczeń, zdejmując ten ciężar z użytkownika. Jako usługa w chmurze Azure Key Vault skaluje się w krótkim czasie, aby sprostać wzrostom użycia w organizacji. W tym samym czasie klucz jest chroniony w sprzętowych modułów zabezpieczeń firmy Microsoft: zachowujesz kontrolę nad kluczowym cyklem życia, ponieważ generujesz klucz i przeniesiesz go do sprzętowych modułów zabezpieczeń firmy Microsoft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementowanie używania własnego klucza (BYOK) na potrzeby usługi Azure Key Vault

Poniższe informacje i procedury umożliwiają wygenerowanie własnego klucza chronionego przez moduł HSM, a następnie przekazanie go do Azure Key Vault — scenariusz tworzenia własnego klucza (BYOK).

## <a name="prerequisites-for-byok"></a>Wymagania wstępne dotyczące funkcji BYOK

Zapoznaj się z poniższą tabelą, aby uzyskać listę wymagań wstępnych dotyczących przenoszenia własnych kluczy (BYOK) dla Azure Key Vault.

| Wymaganie | Więcej informacji |
| --- | --- |
| Subskrypcja platformy Azure |Aby utworzyć Azure Key Vault, musisz mieć subskrypcję platformy Azure: [zarejestruj się, aby skorzystać z bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/) |
| Warstwa usługi Azure Key Vault Premium do obsługi kluczy chronionych przez moduł HSM |Więcej informacji o warstwach i możliwościach usługi dla Azure Key Vault można znaleźć w witrynie internetowej [cennika Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) . |
| Oprogramowanie wspomagające nCipher sprzętowego nshield sprzętowych modułów zabezpieczeń, karty inteligentne i oprogramowanie pomocy technicznej |Musisz mieć dostęp do oprogramowanie wspomagające nCipher sprzętowego modułu zabezpieczeń i podstawowej znajomości operacyjnej oprogramowanie wspomagające nCipher sprzętowego nshield sprzętowych modułów zabezpieczeń. Aby uzyskać listę zgodnych modeli lub kupić moduł HSM, jeśli go nie masz, zobacz [oprogramowanie wspomagające nCipher sprzętowego nshield Hardware Security module](https://go.ncipher.com/rs/104-QOX-775/images/nCipher_nShield_Family_Brochure.pdf?_ga=2.106120835.1607422418.1590478092-577009923.1587131206) . |
| Następujący sprzęt i oprogramowanie:<ol><li>Stacja robocza offline x64 z minimalnym systemem operacyjnym Windows w wersji Windows 7 i oprogramowanie wspomagające nCipher sprzętowego nshield, który ma co najmniej wersję 11,50.<br/><br/>Jeśli na tej stacji roboczej jest uruchomiony system Windows 7, należy [zainstalować Microsoft .NET Framework 4,5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Stacja robocza, która jest połączona z Internetem i ma minimalny system operacyjny Windows z systemem Windows 7 i [Azure PowerShell](/powershell/azure/) zainstalowaną **minimalną wersję 1.1.0** .</li><li>Dysk USB lub inne przenośne urządzenie magazynujące z co najmniej 16 MB wolnego miejsca.</li></ol> |Ze względów bezpieczeństwa odradza się podłączanie pierwszej stacji roboczej do sieci. Jednakże to zalecenie nie jest wymuszane programowo.<br/><br/>W poniższych instrukcjach stacja robocza jest określana jako odłączona stacja robocza.</p></blockquote><br/>Ponadto, jeśli klucz dzierżawy jest przeznaczony dla sieci produkcyjnej, zalecamy użycie drugiej oddzielnej stacji roboczej do pobrania zestawu narzędzi i przekazanie klucza dzierżawy. Do celów testowych można jednak użyć pierwszej stacji roboczej.<br/><br/>W poniższych instrukcjach druga stacja robocza jest określana mianem stacji roboczej podłączonej do Internetu.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generowanie i transferowanie klucza do Azure Key Vault modułu HSM

Aby wygenerować i przesłać klucz do modułu HSM Azure Key Vault, należy wykonać następujące pięć czynności:

* [Krok 1. Przygotowanie stacji roboczej podłączonej do Internetu](#step-1-prepare-your-internet-connected-workstation)
* [Krok 2. Przygotowywanie rozłączonej stacji roboczej](#step-2-prepare-your-disconnected-workstation)
* [Krok 3. Generowanie klucza](#step-3-generate-your-key)
* [Krok 4. Przygotowanie klucza do przesłania](#step-4-prepare-your-key-for-transfer)
* [Krok 5. Przesłanie klucza do usługi Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Krok 1. Przygotowanie stacji roboczej podłączonej do Internetu

W pierwszym kroku wykonaj następujące procedury na stacji roboczej, która jest połączona z Internetem.

### <a name="step-11-install-azure-powershell"></a>Krok 1,1: Zainstaluj Azure PowerShell

Z poziomu stacji roboczej podłączonej do Internetu Pobierz i zainstaluj moduł Azure PowerShell, który zawiera polecenia cmdlet służące do zarządzania Azure Key Vault. Instrukcje instalacji znajdują się w temacie [How to Install and configure Azure PowerShell](/powershell/azure/).

### <a name="step-12-get-your-azure-subscription-id"></a>Krok 1,2: uzyskiwanie identyfikatora subskrypcji platformy Azure

Rozpocznij sesję Azure PowerShell i zaloguj się do konta platformy Azure przy użyciu następującego polecenia:

```Powershell
   Connect-AzAccount
```
W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Następnie użyj polecenia [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) :

```powershell
   Get-AzSubscription
```
Na podstawie danych wyjściowych Znajdź identyfikator subskrypcji, która będzie używana dla Azure Key Vault. Ten identyfikator subskrypcji będzie potrzebny później.

Nie zamykaj okna Azure PowerShell.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Krok 1,3: Pobierz zestaw narzędzi BYOK dla Azure Key Vault

Przejdź do centrum pobierania Microsoft i [Pobierz zestaw narzędzi Azure Key Vault BYOK](https://www.microsoft.com/download/details.aspx?id=45345) dla regionu geograficznego lub wystąpienia platformy Azure. Skorzystaj z poniższych informacji, aby zidentyfikować nazwę pakietu do pobrania i odpowiedni skrót pakietu SHA-256:

---
**Stany Zjednoczone:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Terenie**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**Wsch**

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

C41060C5C0170AAAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**Emiraty**

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
**Rząd USA:**

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
**Niemcy publiczne:**

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


Aby sprawdzić integralność pobranego zestawu narzędzi BYOK, należy użyć polecenia cmdlet [Get-FileHash](/powershell/module/microsoft.powershell.utility/get-filehash) w ramach sesji Azure PowerShell.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Zestaw narzędzi zawiera następujące:

* Pakiet klucza wymiany klucza (KEK) o nazwie rozpoczynającej się od **BYOK-KEK-pkg-.**
* Pakiet "Security World" o nazwie rozpoczynającej się od **BYOK-SecurityWorld-pkg-.**
* Skrypt języka Python o nazwie **verifykeypackage.py.**
* Plik wykonywalny wiersza polecenia o nazwie **KeyTransferRemote.exe** i skojarzone biblioteki DLL.
* Visual C++ pakiet redystrybucyjny o nazwie **vcredist_x64.exe.**

Skopiuj pakiet na dysk USB lub do innego przenośnego urządzenia pamięci masowej.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Krok 2. Przygotowywanie rozłączonej stacji roboczej

W tym drugim kroku wykonaj następujące procedury na stacji roboczej, która nie jest połączona z siecią (Internetem lub siecią wewnętrzną).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Krok 2,1: przygotowywanie rozłączonej stacji roboczej przy użyciu modułu HSM oprogramowanie wspomagające nCipher sprzętowego nshield

Zainstaluj oprogramowanie oprogramowanie wspomagające nCipher support na komputerze z systemem Windows, a następnie Dołącz moduł HSM oprogramowanie wspomagające nCipher sprzętowego nshield do tego komputera.

Upewnij się, że narzędzia oprogramowanie wspomagające nCipher znajdują się w ścieżce (**% nfast_home% \ bin**). Na przykład wpisz następujące polecenie:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Aby uzyskać więcej informacji, zobacz Podręcznik użytkownika dołączony do modułu HSM sprzętowego nshield.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Krok 2,2: Instalowanie zestawu narzędzi BYOK na odłączonej stacji roboczej

Skopiuj pakiet zestawu narzędzi BYOK z dysku USB lub innego przenośnego urządzenia pamięci masowej, a następnie wykonaj następujące czynności:

1. Wyodrębnij pliki z pobranego pakietu do dowolnego folderu.
2. Z poziomu tego folderu uruchom plik vcredist_x64.exe.
3. Postępuj zgodnie z instrukcjami, aby zainstalować składniki środowiska uruchomieniowego Visual C++ dla Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Krok 3. Generowanie klucza

W tym trzecim kroku wykonaj następujące procedury na odłączonej stacji roboczej. Aby ukończyć ten krok, moduł HSM musi być w trybie inicjalizacji. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Krok 3,1: zmiana trybu HSM na "I"

Jeśli używasz oprogramowanie wspomagające nCipher sprzętowego nshield Edge, aby zmienić tryb: 1. Użyj przycisku tryb, aby wyróżnić wymagany tryb. 2. W ciągu kilku sekund naciśnij i przytrzymaj przycisk Wyczyść przez kilka sekund. Jeśli tryb ulegnie zmianie, dioda LED trybu zostanie migana i pozostaje niewidoczny. Dioda LED stanu może być nieregularna w ciągu kilku sekund, a następnie regularnie błyska, gdy urządzenie jest gotowe. W przeciwnym razie urządzenie pozostaje w bieżącym trybie z zaświeceniem odpowiedniego trybu.

### <a name="step-32-create-a-security-world"></a>Krok 3,2: Tworzenie środowiska zabezpieczeń Security World

Uruchom wiersz polecenia i uruchom program oprogramowanie wspomagające nCipher New-World.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Uruchomienie programu powoduje utworzenie pliku **Security World** w lokalizacji %NFAST_KMDATA%\local\world, co odpowiada lokalizacji folderu C:\ProgramData\nCipher\Key Management Data\local. Możesz użyć różnych wartości dla kworum, ale w naszym przykładzie zostanie wyświetlony monit o wprowadzenie trzech pustych kart i numerów PIN dla każdej z nich. Następnie wszystkie dwie karty zapewniają pełen dostęp do środowiska zabezpieczeń Security World. Karty te tworzą od tej chwili **zestaw kart administratora** nowego środowiska zabezpieczeń Security World.

> [!NOTE]
> Jeśli moduł HSM nie obsługuje nowszego pakietu szyfr Suite DLf3072s256mRijndael, można zastąpić polecenie--cipher-Suite = DLf3072s256mRijndael z--cipher-Suite = DLf1024s160mRijndael
> 
> Sieć World Security stworzyła new-world.exe, która jest dostarczana z oprogramowaniem oprogramowanie wspomagające nCipher w wersji 12,50, nie jest zgodna z tą procedurą BYOK. Dostępne są dwie opcje:
> 1) Obniżenie wersji oprogramowania oprogramowanie wspomagające nCipher na 12.40.2 w celu utworzenia nowego świata zabezpieczeń.
> 2) Skontaktuj się z pomocą techniczną usługi oprogramowanie wspomagające nCipher i poproś o udostępnienie poprawki dla wersji 12,50 oprogramowania, która umożliwia korzystanie z wersji 12.40.2 new-world.exe zgodnej z tą procedurą BYOK.

Następnie wykonaj poniższe czynności:

* Utwórz kopię zapasową pliku środowiska zabezpieczeń. Zabezpiecz i chroń plik środowiska zabezpieczeń, karty administratora oraz ich kody PIN, a także upewnij się, że nikt nie ma dostępu do więcej niż jednej karty.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Krok 3,3: zmiana trybu HSM na "O"

Jeśli używasz oprogramowanie wspomagające nCipher sprzętowego nshield Edge, aby zmienić tryb: 1. Użyj przycisku tryb, aby wyróżnić wymagany tryb. 2. W ciągu kilku sekund naciśnij i przytrzymaj przycisk Wyczyść przez kilka sekund. Jeśli tryb ulegnie zmianie, dioda LED trybu zostanie migana i pozostaje niewidoczny. Dioda LED stanu może być nieregularna w ciągu kilku sekund, a następnie regularnie błyska, gdy urządzenie jest gotowe. W przeciwnym razie urządzenie pozostaje w bieżącym trybie z zaświeceniem odpowiedniego trybu.

### <a name="step-34-validate-the-downloaded-package"></a>Krok 3,4: weryfikowanie pobranego pakietu

Ta czynność jest opcjonalna, ale zaleca się jej wykonanie w celu upewnienia się, że:

* Klucz wymiany klucza uwzględniony w zestawie narzędzi został wygenerowany na podstawie oryginalnego modułu HSM oprogramowanie wspomagające nCipher sprzętowego nshield.
* Skrót środowiska zabezpieczeń Security, który został uwzględniony w zestawie narzędzi, został wygenerowany w oryginalnym module HSM oprogramowanie wspomagające nCipher sprzętowego nshield.
* Klucz wymiany klucza nie może zostać wyeksportowany.

> [!NOTE]
> Aby sprawdzić pobrany pakiet, moduł HSM musi być połączony, włączony i musi mieć na nim świat zabezpieczeń (na przykład właśnie utworzony).

Aby zweryfikować pobrany pakiet:

1. Uruchom skrypt verifykeypackage.py, wpisując jedną z następujących opcji, w zależności od regionu geograficznego lub wystąpienia platformy Azure:

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
   * Dla Ameryka Łacińska:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
        ```
   * Dla Japonii:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
        ```
   * W przypadku Korei:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
        ```
   * W Republice Południowej Afryki:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
        ```
   * Za Zjednoczone Emiraty Arabskie:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
        ```
   * Dla Australii:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
        ```
   * W przypadku [Azure Government](https://azure.microsoft.com/features/gov/), który używa wystąpienia platformy Azure dla instytucji rządowych:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
        ```
   * W przypadku instytucji rządowych USA:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
        ```
   * W przypadku Kanady:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
        ```
   * Dla Niemiec:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * Dla Niemiec Public:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * W przypadku Indii:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
      ```
   * Dla Francji:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
        ```
   * W Zjednoczonym Królestwie:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
        ```
   * Dla Szwajcarii:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1
        ```

     > [!TIP]
     > Oprogramowanie oprogramowanie wspomagające nCipher sprzętowego nshield obejmuje środowisko Python w lokalizacji% NFAST_HOME% \ python\bin
     >
     >
2. Potwierdź, że zobaczysz następujące polecenie, które wskazuje na pomyślne sprawdzenie poprawności: **wynik: sukces**

Ten skrypt sprawdza poprawność łańcucha podpisywania do klucza głównego sprzętowego nshield. Skrót klucza głównego jest osadzony w skrypcie; jego wartość powinna wynosić **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Tę wartość można także potwierdzić osobno, odwiedzając [witrynę sieci Web oprogramowanie wspomagające nCipher](https://www.ncipher.com).

Teraz możesz utworzyć nowy klucz.

### <a name="step-35-create-a-new-key"></a>Krok 3,5: Utwórz nowy klucz

Wygeneruj klucz przy użyciu programu oprogramowanie wspomagające nCipher sprzętowego nshield **do generowania kluczy** .

Uruchom następujące polecenie, aby wygenerować klucz:

```azurepowershell
generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=
```

Podczas uruchamiania tego polecenia należy zastosować się do następujących instrukcji:

* Parametr *protect* musi mieć ustawioną wartość **module**, jak przedstawiono. Spowoduje to utworzenie klucza chronionego przez moduł. Zestaw narzędzi BYOK nie obsługuje kluczy chronionych z użyciem protokołu OCS.
* Zamień wartość *contosokey* dla pozycji **ident** i **plainname** na dowolną wartość ciągu. Aby zminimalizować ogólne koszty administracyjne i zmniejszyć ryzyko błędów, zalecamy użycie tej samej wartości dla obu tych elementów. Wartość **ident** musi zawierać tylko cyfry, łączniki i małe litery.
* W tym przykładzie parametr pubexp został pozostawiony pusty (wartość domyślna), można jednak określić konkretne jego wartości. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją oprogramowanie wspomagające nCipher.](https://www.entrust.com/-/media/documentation/brochures/entrust-nshield-general-purpose-hsms-br-a4.pdf)

To polecenie tworzy plik klucza z tokenem w folderze% NFAST_KMDATA% \ Local o nazwie rozpoczynającej się od **key_simple_**, a następnie **ident** , który został określony w poleceniu. Na przykład: **key_simple_contosokey**. Ten plik zawiera zaszyfrowany klucz.

Utwórz w bezpiecznej lokalizacji kopię zapasową tego pliku stokenizowanego klucza.

> [!IMPORTANT]
> Gdy później przeniesiesz klucz do Azure Key Vault, firma Microsoft nie będzie mogła wyeksportować tego klucza z powrotem do Ciebie, dlatego bardzo ważne jest, aby w bezpieczny sposób utworzyć kopię zapasową klucza i bezpieczeństwa. Skontaktuj się z [oprogramowanie wspomagające nCipher](https://www.ncipher.com/about-us/contact-us) , aby uzyskać wskazówki i najlepsze rozwiązania związane z tworzeniem kopii zapasowej klucza.
>


Teraz możesz przystąpić do przenoszenia klucza do Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Krok 4. Przygotowanie klucza do przesłania

W tym czwartym kroku wykonaj następujące procedury na odłączonej stacji roboczej.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Krok 4,1: Utwórz kopię klucza z ograniczonymi uprawnieniami

Otwórz nowy wiersz polecenia i Zmień bieżący katalog na lokalizację, w której został rozpakowany plik zip BYOK. Aby zmniejszyć uprawnienia do klucza, w wierszu polecenia Uruchom jedną z następujących czynności, w zależności od regionu geograficznego lub wystąpienia platformy Azure:

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
* Dla Ameryka Łacińska:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
   ```
* Dla Japonii:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
   ```
* W przypadku Korei:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
   ```
* W Republice Południowej Afryki:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
   ```
* Za Zjednoczone Emiraty Arabskie:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
   ```
* Dla Australii:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
   ```
* W przypadku [Azure Government](https://azure.microsoft.com/features/gov/), który używa wystąpienia platformy Azure dla instytucji rządowych:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
   ```
* W przypadku instytucji rządowych USA:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
   ```
* W przypadku Kanady:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
   ```
* Dla Niemiec:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* Dla Niemiec Public:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* W przypadku Indii:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
   ```
* Dla Francji:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
   ```
* W Zjednoczonym Królestwie:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
   ```
* Dla Szwajcarii:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1
   ```

Po uruchomieniu tego polecenia Zastąp *contosokey* wartością określoną w **kroku 3,5: Utwórz nowy klucz** w kroku [Generuj klucz](#step-3-generate-your-key) .

Zostanie wyświetlony monit o podłączenie kart administratora World Security.

Po zakończeniu wykonywania polecenia zostanie wyświetlony **wynik: sukces** , a kopia klucza z ograniczonymi uprawnieniami znajduje się w pliku o nazwie key_xferacId_ \<contosokey> .

Można sprawdzić listy ACL przy użyciu następujących poleceń przy użyciu narzędzi oprogramowanie wspomagające nCipher sprzętowego nshield:

* aclprint.py:

   ```cmd
   "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
   ```
* kmfile-dump.exe:

   ```cmd
   "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
   ```
  Po uruchomieniu tych poleceń Zastąp contosokey z tą samą wartością określoną w **kroku 3,5: Utwórz nowy klucz** w kroku [Generuj klucz](#step-3-generate-your-key) .

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Krok 4,2: szyfrowanie klucza przy użyciu klucza wymiany kluczy firmy Microsoft

Uruchom jedno z następujących poleceń, w zależności od regionu geograficznego lub wystąpienia platformy Azure:

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
* Dla Ameryka Łacińska:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Dla Japonii:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* W przypadku Korei:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* W Republice Południowej Afryki:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Za Zjednoczone Emiraty Arabskie:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Dla Australii:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* W przypadku [Azure Government](https://azure.microsoft.com/features/gov/), który używa wystąpienia platformy Azure dla instytucji rządowych:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* W przypadku instytucji rządowych USA:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* W przypadku Kanady:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Dla Niemiec:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Dla Niemiec Public:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* W przypadku Indii:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Dla Francji:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* W Zjednoczonym Królestwie:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Dla Szwajcarii:

  ```azurepowershell
  KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
  ```


Podczas uruchamiania tego polecenia należy zastosować się do następujących instrukcji:

* Zastąp *contosokey* identyfikatorem użytym do wygenerowania klucza w **kroku 3,5: Utwórz nowy klucz** w kroku [Generuj klucz](#step-3-generate-your-key) .
* Zastąp *Identyfikator subskrypcji kluczem* subskrypcji platformy Azure, który zawiera Magazyn kluczy. Ta wartość została wcześniej pobrana, w **kroku 1,2: Pobierz swój identyfikator subskrypcji platformy Azure** z kroku [przygotowanie stacji roboczej z połączeniem z Internetem](#step-1-prepare-your-internet-connected-workstation) .
* Zastąp *ContosoFirstHSMKey* etykietą, która jest używana jako nazwa pliku wyjściowego.

Po pomyślnym zakończeniu zostanie wyświetlony **wynik: sukces** , a w bieżącym folderze istnieje nowy plik o następującej nazwie: KeyTransferPackage-*ContosoFirstHSMkey*. BYOK

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Krok 4,3: Skopiuj pakiet transferu klucza do stacji roboczej połączonej z Internetem

Za pomocą dysku USB lub innego przenośnego magazynu Skopiuj plik wyjściowy z poprzedniego kroku (KeyTransferPackage-ContosoFirstHSMkey. BYOK) do stacji roboczej podłączonej do Internetu.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Krok 5. Przesłanie klucza do usługi Azure Key Vault

W tym ostatnim kroku na stacji roboczej połączonej z Internetem Użyj polecenia cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) w celu przekazania pakietu transferu klucza skopiowanego z rozłączonej stacji roboczej do modułu HSM Azure Key Vault:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Jeśli przekazywanie zakończy się pomyślnie, zobaczysz wyświetlone właściwości klucza, który właśnie został dodany.

## <a name="next-steps"></a>Następne kroki

Teraz można używać tego klucza chronionego przez moduł HSM w magazynie kluczy. Aby uzyskać więcej informacji, zobacz to [porównanie](https://azure.microsoft.com/pricing/details/key-vault/)cen i funkcji.